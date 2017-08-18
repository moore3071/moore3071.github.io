---
layout: post
title:	"A tale of GPG idiocies"
date:	2017-08-17
categories: blogging
---
Strap in for a story of my foolish younger years, aka two and a half years ago, with GPG and an attempt to do the improbable and fix this. My specific error in this case was making a keypair with no expiration date.

GPG stands for Gnu Privacy Guard, and implements the [OpenPGP standard](https://tools.ietf.org/html/rfc4880). In simple terms, it allows me to show that I'm actually the one who sent an email or made a Git commit(non-repudiation), or receive encrypted information that only I can read. GPG is an older tool, yet is still used today by developers, computer scientists, journalists, and more.

My (public) key(s) can be found on the [MIT pgp keyserver](http://pgp.mit.edu/pks/lookup?search=moore.3071%40osu.edu&op=index). At the time of writing this, my keys look like this:

| Type | bits/keyID | Date | User ID |
| pub | 4096R/299FE115 | 2016-05-06 | Brandon Moore <moore.3071@osu.edu> |
| pub | 4096R/CFDE2E99 | 2015-10-08 | \*\*\* Key Revoked \*\*\* [not verified] |
|     |                |            | Brandon Moore <moore.3071@osu.edu> |
| pub | 4096R/AD0A96D7 | 2015-03-06 | Brandon Moore <moore.3071@osu.edu> |

You'll notice that the second key is revoked. As such it is no longer to be used or trusted. If we take a closer look at the third key though, we can ssee that the key is signed by [three different keys including itself](http://pgp.mit.edu/pks/lookup?op=vindex&search=0x6FB1887CAD0A96D7). Note that none of these keys have an expiration date. Now would be a good time to discuss some potential problems that can happen with improper use of GPG:

1. Losing your GPG private key
2. Forgetting your GPG password
3. Having your GPG private key stolen
4. Believing your key to be compromised in any manner

All of these problems are in the same category. They can be solved with one easy solution, a revocation certificate. Always generate a revocation certificate and store it somewhere safe. This can be used to revoke your key on the server if needed. To generate a revocation certificate run `gpg --output revoke_key.asc --gen-revoke <key_identifier>`. This will prompt you for some choices on how to generate a revocation certificate. For a new key, I'd recommend not specifying a reason for revoking the key, and giving a description like 'Revocation in case of key loss at a later point'. Note that anyone who obtains this revocation certificate can go ahead and revoke your key.

A key reason I haven't revoked my third key is that I never made a revocation certificate. In fact, I believed that I had completely lost this key when changing from Ubuntu to ArchLinux on my machines. If this were the case, I would have little hope of getting rid of my old key. The only options would be brute-forcing my key for much longer than my life, asking a three letter agency if they have an exploit for RSA, or find an advanced quantum computer to break my key.

In my preparation to change my system over to Qubes OS, I pulled out my backup drive. Much to my surprise, there was my long lost GPG key. Problem solved, right? Well, unfortunately, I didn't know the password for my key. Luckily this is one thing that you can reasonably can recover from. So now let's discuss password cracking!

## Cracking my own password

Cracking a password can't be easy though can it? GPG uses sha-1 by default, and although there have been [good reasons to switch away from sha-1](https://security.googleblog.com/2017/02/announcing-first-sha1-collision.html), the hash can't be accessed and used to find a collision. So, how many hashes are there? Well Sha1 outputs a 40 character or 160 bit hash, which means there's 2^160 or approximately 1.5e48 or 1.5 quindecillion hashes. There's no way a computer could find this single hash, right?

It's important to remember that a password cracker isn't trying to guess this hash, but a password which most humans would keep short to save their fingers and memories. So how long would it take to guess a 10 character password? Let's assume for round numbers' sake that the password is numeric which the naive cracker knows is the case. The cracker will proceed to guess 0,1,2,3,4,5,6,7,8,9,10... until it guesses the correct password. Before it even gets to 10 characters in length it has tried 10e9+10e8+10e7+10e6+10e5+10e4+10e3+10e2+10e1 passwords or 1,111,111,110 passwords. Now remember that this is clearly numeric. An actual password could 26 lowercase letters, 26 uppercase, 10 numerics, plus 32 special characters on a normal US keyboard. This means that each additional character adds a complexity of a power of 94. There are 9216 unique two character passwords alone. So cracking is still near impossible on your own machine, right?

Well, that's taking out most of the human factor. It's important to remember that a password cracker typically isn't trying to guess a *random* password, but a human generated password. Password crackers such as John The Ripper take advantage of the fact that humans are less than random and can only remember shorter passwords and generally default to dictionary words making a password like [correct horse battery staple](https://xkcd.com/936/) very crackable. The algorithms in password crackers tend to try to change with human patterns in password security. Do you typically use your birthdate for a password? Well, an advanced password cracker will probably try all variants from 09/08/2005 to Sept_08_2oOS. Are people taking xkcd's advice, and maing their password 'correct_horse_battery_staple'? Well use a dictionary file and concat different words together.

So, are we doomed to have our password constantly cracked? No, there are a number of ways that you as a user and companies as service providers can help to protect your password and data. We'll talk about some of these ways in my next post, but if you want to protect yourself now, consider looking up password hygiene, and generally safety tips such as [these](https://ssd.eff.org/en/module/creating-strong-passwords) from the Electronic Frontier Foundation.

And what about my lost GPG key? Currently 'John The Ripper' is working on finding my lost password and is entering into the 50th hour of cracking. Just a subtle reminder for me to:

1. Generate a revocation certificate for my GPG keys
2. Keep better track of my files when changing computers or OS
3. Maybe use a password manager

