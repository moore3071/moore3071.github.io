---
layout: post
title:	"Password practices as a provider"
date:	2017-09-01
categories: blogging
---
Let's talk about what your password (should) look like when it's stored by a service. This post will talk about (cryptographic) hashing algorithms, salts, peppers, and key stretching.  Let's start with our first topic, hashes.

## What is a hash?

A hashing algorithm is a one-way function, taking any input such as 'mypassword' or 'pass' and converting it to a fixed-size value such as '34819d7beeabb9260a5c854bc85b3e44' or '1a1dc91c907325c69271ddf0c944bc72' (this is with the md5 algorithm). A hash function is:

- **Deterministic**: Any time a message m is hashed it creates the same digest h
- **Uniform**: The output should be as uniform as possible to avoid two messages, m and n, from having the same digest (collisions WILL occur, but we want them to occur as infrequently as possible)

**Note: While we want collisions to be rare, there will be collisions since we're taking a much larger set of inputs and mapping it to a smaller set of outputs**

There are some extra features that are desireable in cryptographic hash algorithms as compared to search based algorithms:

- **One way function**: Given a digest `h`, it should be difficult to reverse the hash and find a message `m` that return the digest `h` when passed through the hashing algorithm.
- **Avalanche effect**: Any small change in the message should change the digest drastically. Ideally, changing a single bit would change half the bits in the digest. We can see this with a quick example with the md5 algorithm hashing `b` and `c` which yields `92eb5ffee6ae2fec3ad71c777531578f` and `4a8a08f09d37b73795649038408b5f33`(60 out of 128 bits flipped from 1 bit changing). 

Password should never be stored in the clear (e.g. unencrypted). In order to avoid this, the password is hashed with a cryptographic hash, and the digest stored along with the username.

**Update for clarity: When you hit enter or submit, the hashed value of the password you entered is what's compared to the digest that the service has stored. Most of the time on networked services hashing occurs on the server and your password is sent in the clear to the server. If you're interested in who might be seeing your password in the clear, refer to [this helpful article](https://www.eff.org/pages/tor-and-https) from the EFF. TLDR, don't log in if you don't see that HTTPS lock in the top-left of your browser. There are a few services that do hash on the client-side; specifically LastPass which [uses your username as a salt, hashes the salt+password, gives that to the server, and hashes that while using an additional salt](https://lastpass.com/support.php?cmd=showfaq&id=1116). Expect more mentions of LastPass in a future post on attacks a provider might face.**

## Attacking hashes

So, let's ask ourselves, how do we discover a password given a digest? This is typically done via a rainbow table, which provides a list of precomputed hashes for a number of common passwords or character combinations. You can check out [crackstation.net](https://crackstation.net/) which uses a massive 15GB rainbow table file of a little less than 1.5 billion hashes that you can download along with a smaller 684MB file with around 64 million passwords. If you put in the md5 of any of the three previously mentioned phrases then the specified password will be returned almost immediately. Crackstation isn't limited to md5, but can work on 16 different types of hashing algorithms. While as an administrator we'd like to imagine that our users will choose a smart password, As a user who doesn't use a password manager, I know that many (or most) users will not choose a secure password.

Even worse, say we're Mallory, a user on a service called [ConnectedIn](https://defuse.ca/blog/cracking-linkedin-hashes-with-crackstation.html){: .hidden-link } for job networking and we manage to get ahold of the hashed passwords of the users. The site protects the users' password by hashing them with Sha-1, but this doesn't stop us from running these hashes through a rainbow table which ends up giving us:

| User | Digest | pass |
|----------------------|
| alice | 1cfbd686487a6b2896248da8c4bbe5d6c6957847 | iloveyou |
| David | 7c6a61c68ef8b9b6b061b28c348bc1ed7921cb53 | passw0rd |
| Erin | c6922b6ba9e0939583f973bc1682493351ad4fe8 | 1qaz2wsx |
| mallory(us) | 7fc7ddef5a0a0fbeb8716affafd202b14610df6e | ConnectedIn17 |
| Frank | 7fc7ddef5a0a0fbeb8716affafd202b14610df6e | *(unknown)* |
| Richard | 3c75e7e29ea3d5ccc22d9ab61e47366756634a26 | *unknown* |
| Bob | 3c75e7e29ea3d5ccc22d9ab61e47366756634a26 | *unknown* |
{: .bordered-table }

We obtained Alice's, David's, and Erin's passwords through the rainbow table, but the digests also tell us *a* password for Frank, and key info about Richard and Bob's password. Note that the digest for Frank is the same as our(Mallory's) digest. This means our password `ConnectedIn17` will work on Frank's account. Meanwhile, we can note that Richard and Bob have the same digest and therefore their passwords are interchangeable.

So, how can this attack be avoided? We simply add a piece of text that is unique to the user onto the password before hashing it. This piece of additional text is called a salt. Now if we have two users with the same passwords, they should end up with very different digests. While the text needs to be unique for each user, there's no need to make it secret. It is usually stored with the hashed passwords. Since there's no need for secrecy, you might consider simply using the username as the salt. This is generally inadvisable both for [allowing easy changing of username without necessitating a password change, and the slight security concern of someone generating rainbow tables for common usernames as salts](https://security.stackexchange.com/questions/150007/why-not-use-username-as-a-password-salt). Could someone generate rainbow tables for each salt though? Well, the salt for SHA-512 in Linux is 128 bit, meaning $$ 2^{128} $$ or around 3.4e38 salts. This is currently considered difficult enough for modern day computers to stop an attacker from generated rainbow tables for each salt.

Now we have the salt, but what about a little pepper? There is in fact a cryptographic term pepper, although it is seldom used. Pepper is the exact compliment to a salt, it is a piece of text that is the same for all users, but must be kept secret. It is obfuscated and stored separately from the salts and hashes. If an attacker compromises the salts and hashes, but the service utilizes a pepper that isn't compromised, then an attacker with rainbow tables for every salt will still be useless as they lack the pepper. Again, peppers are an oddity in cryptography and not often seen in actual systems.

## Key-stretching and Linux passwords

If you're running a Linux system, then you probably have a file `/etc/shadow` which stores the hashed passwords along with the salt and other key information following a [specified pattern](https://en.wikipedia.org/wiki/Passwd#Shadow_file).

```
user:$algorithm$salt$digest:::::::
```

The algorithms in GNU/Linux are:

| id | algorithm | default_number_of_iterations | modifiable_iteration_count? |
|--------------------------------------------------------------|
| 1 | md5 | 1000 | no |
| 2a | blowfish | 64 | yes |
| 2y | blowfish(8-bit safe) | 64 | yes |
| 5 | sha-256 | 5000 | yes |
| 6 | sha-512 | 5000 | yes |
{: .bordered-table }

It should be noted that Blowfish isn't supported by default in GNU/Linux, but certain distros opt to add support for Blowfish. You might notice that we mention number of iterations in this table. So you might ask what is a iteration? It took me way too long to figure out how these iterations worked, but it is a key mechanism for protecting passwords, so let's discuss it.

## How iterations don't work

If you're like me you might imagine simply running something like this pseudocode:

```
result = hash(salt+password);

#Option 1
for 1:num_iterations {
  result = hash(result);
}
#Option 2
for 1:num_iterations {
  result = hash(salt+result);
}

```

This would present some problems though. Mainly, the fact that it's [possible to find a value x, such that hash(x)=x](https://stackoverflow.com/questions/235785/is-there-an-md5-fixed-point-where-md5x-x) or simply looping over the same hashes again and again. If the former is true, and [there's around 63% chance it is](https://stackoverflow.com/questions/235785/is-there-an-md5-fixed-point-where-md5x-x), then more iterations lead to a chance of many normal passwords resulting in the same hash when run through a large number of these false iterations. Luckily the above pseudocode isn't how iterating works.

## How iterations do work

You can read this code in GNU's [glibc implementation of libcrypt](https://sourceware.org/git/?p=glibc.git;a=blob;f=crypt/sha256-crypt.c;h=561ff577bf1d14cddfa44df7e8fa00c68c2a9a96;hb=HEAD#l281) or the [BSD implementation](https://github.com/freebsd/freebsd/blob/a4ebbc6d5fe6124929d160aa39bfa9febca345a2/lib/libcrypt/crypt-sha256.c#L182). I personally find the BSD code to be easier to follow. If you're not a coder, hate C, or simply don't want to read code, there's an english explanation for md5 on Wikipedia ([link](https://en.wikipedia.org/wiki/Crypt_(C)#MD5-based_scheme)):

>First the passphrase and salt are hashed together, yielding an MD5 message digest. Then a new digest is constructed, hashing together the passphrase, the salt, and the first digest, all in a rather complex form. Then this digest is passed through a thousand iterations of a function which rehashes it together with the passphrase and salt in a manner that varies between rounds. The output of the last of these rounds is the resulting passphrase hash.

We're going to look at the specific method for sha-256 and sha-512 in the table below. First the password is hashed with the corresponding sha algorithm before being running through x number of iterations (defaults to 5000). The value that gets hashed in each iteration is decided by the iteration number. The table below shows you what's getting hashed in each case.

| Iteration multiple of 7 | iteration multiple of 3 | Even Iteration | what gets hashed |
|---------------------------------------------------------------------------------------|
|   |   |   | password+salt+password+prev_hash |
|   |   | X | prev_hash+salt+password+password |
|   | X |   | password+password+prev_hash |
|   | X | X | prev_hash+password+password |
| X |   |   | password+salt+prev_hash |
| X |   | X | prev_hash+salt+password |
| X | X |   | password+previous_hash |
| X | X | X | prev_hash+password |
{: .bordered-table .centered }

While it is possible to run into looping hashes while following this plan, it is very unlikely and relies on a perfect combination of password salt and previous hashes. In other words, it's not a viable attack to look for a looping hash since it's dependent on the previous hash, the password, and the salt.

## Why iterate?

So we can use multiple iterations when storing our passwords, awesome! Why though? Hashing functions like MD5, sha1, and sha512 are extremely fast. While we assume the user wants the fastest response possible. It's unlikely though that a user is going to care about login time if it's faster than a second. Someone brute-forcing millions of passwords will  notice any delay in the time to do a single password. This is called key-stretching which is used to fight brute-force attacks.
