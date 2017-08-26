---
layout: post
title:	"Password practices as a user"
date:	2017-08-20
categories: blogging
---
In my last post, I spent some time discussing password crackers and how they can be quite powerful. But, you as a user can take precautions to protect your accounts, just as developers should take precautions to protect the data they receive. This post will focus on passsword hygiene, brute-force attacks, leveraged credentials, and brute-force resistance systems design. While I might talk about brute-force attacks and phishing, I won't spend a large amount of time on attack types. If you want to read up on different attack times, maybe check out this post from [Symantec](https://www.symantec.com/connect/articles/security-11-part-3-various-types-network-attacks).

Let's start with the most helpful and general advice in this whole post: use a password manager. A password manager will help you to avoid most common problems such as reusing passwords(BAD!), forgetting passwords(BAD!), or making weak passwords(BAD!). Password managers are like the swiss army knife of password management. I have to confess, I'm a hypocrite and I don't use a password manager. Some things to consider when choosing a password manager are whether being able to reach your manager from anywhere is worth using a networked third party, does the manager support all the OSes that you need it on, and how easy to use is it? Remember that while an online password manager might offer web browser plugins and take care of plugging the result into web-pages automagically, your passwords aren't just on your machine. For some this is an easy tradeoff, for others this is too much trust to place in any one service.

The second general piece of advice for users is to take advantage of multi-factor authentication. Many online service currently offer two factor authentication. For an incomplete list, check [here](https://twofactorauth.org/). So what's multi-factor authentication? Multi-factor authentication means that your account relies on more than just a password. When an unrecognized device attempts to authenticate with your credentials, the service asks for an extra piece of info that you possess in some manner. This may be passed to the user by email, SMS, or a dedicated 2FA software or hardware tool. For the dedicated tools a time-based (usually 30 second) code is generated following the [time-based one time password algorithm rfc](https://tools.ietf.org/html/rfc6238). It should be noted that like most security tools, multi-factor authentication is beneficial, but not foolproof. If your adversaries are state-level actors, don't expect multi-factor authentication to stop them.

Now let's focus on the two of the three common bad practices I mentioned in the password manager paragraph. Why is it bad to reuse passwords? What is a weak password? Well let's start with password reuse. It might be useful to you if every site just needs the credentials `john@email.provider` with a password of `password1?` (which should satisfy all mandatory requirements despite being very weak). What happens though if Wendy gets your password for FaceNook? Well, since many people reuse credentials, why not try to access LockBank, Chitter, or Coldmail with your credentials? In this way a single set of compromised credentials have compromised multiple accounts. What about changing your password slightly? If a service is susceptible to password cracking, then it's easy to try different variants until you get it right. Just like at Ohio State University where your password had to be updated on a regular basis, and many students admitted to simply incrementing from `MyPassword1` to `MyPassword2`.

So, we should make every password unique, but how do we make it strong? You might be thinking of the [Xkcd on password strength](https://xkcd.com/936/) which advocates using a series of dictionary words. This makes a ton of sense when going against an incremental cracking mode, but imagine you're designing a password cracker and know that a chain of dictionary words is common. You'll clearly try different combinations of common words. If you include enough words and have a wide enough vocabulary when making your own password, you can succeed in making a good complex one with a series of 4 or so words, but if you're using common words like `correct horse battery staple`, you drastically lower the strength of your password. Let's go ahead and make some assumptions and then get an informal feel for the password space given different cracking methods.

- Let's look at a purely numeric like a pin. Apparently Ios lets you use a 4-6 digit pin, so let's work with that. How many different 4 digit passcodes are there? $$ 10^4 = 10,000 $$. Meanwhile there are $$ 10^6 $$ or 1,000,000 six digit codes.
- What about a normal password with letters, numbers, and special characters. This is 94 characters. There are two cases, when the attacker knows the length, and when they don't. 
  - Let's look at a function given an unknown length of the password by the adverary, $$ f(n)=\frac{1-94^{n+1}}{1-94} $$. So let's look at two different lengths: 8 and 10 characters. The number of passwords for each is 6e15 and 5.44e19.
  - Then we have a must easier function given a known length, $$ g(n)=94^n $$. Our results for known password length are 6e15 and 5.38e19. Note that the magnitudes given a known and unknown length are the same.
- Let's look at using words as passwords. Obviously if we're using the character guessing strategy above, we can get some impressively large password sets. So let's look instead at guessing combinations of words.
  - Our first try will use a rather large dictionary with rare words such as xenography, valetudinarian, or hymeneal. Specifically the [en_us dictionary used by Aspell](http://wordlist.aspell.net/hunspell-readme/). This comes out to around 50,000 words. So we can basically replace the 94 in g(n) to be 50,000 (we're foregoing trying all combinations less than n words since the magnitude is the same). Let's rememeber that the xkcd comic used only four words, which in this case gives us $$ 50,000^4 $$ or 6.25e18. If we go with 5 words it jumps up to 3.125e23.
  - Now what happens if we use a smaller dictionary, say the [10,000 most common words](https://github.com/first20hours/google-10000-english). Four words would be 1e16 meanwhile five words would be 1e20.
  - For a full understanding, let's look at cracking the word passwords by going through characters. The average English word length from [this](http://www.ravi.io/language-word-lengths) source is apparently just over 8 characters. So let's assume that each word is 8 characters making our 4 word passphrase 32 characters, and our 5 word passphrase is 40 characters. Plugging this in to a modified g(n), using 26 instead of 94, gives us 2e45 for 4 words and 4e56 for 5 words.

So let's look at these results all at once.

| generation style | cracking style | password-pool size |
| 4-digit numeric | iterative | 1e4 |
| 6-digit numeric | iterative | 1e6 |
| 8 characters | character | 6e15 |
| 4 words | small dictionary | 1e16 |
| 4 words | large dictionary | 6e18 |
| 10 characters | character | 5e19 |
| 5 words | small dictionary | 1e20 |
| 5 words | large dictionary | 3e23 |
| 4 words | character | 2e45 |
| 5 words | character | 4e56 |

To be fair to the word generation styles, this is assuming everything is lower case and words aren't separated in any manner. To be fair to the attacker side, there are probably a bunch of more optimized cracking styles. Additionally, we're assuming the words are in a proper nonsensical order like 'correct horse battery staple' instead of an actual phrase like 'turtles are really neat'.

My suggestion for password generation is a series of words forming a nonsensical phrase and extra characters for good measure. For the polyglots out there, I'd recommend mixing languages, even if that language is your occupation (you chem-Es, medical practicioners, and even military members have a plethora of working lingo to choose from). 

Note though that crackers are just a single attack vector. Password managers or proper care in making your passwords unique and strong are great, but do little to protect you from attacks such as someone glancing over your shoulder, recording audio as you type, keylogging, phishing, or social engineering (yes, some password managers do a pretty good job at mitigating all but the last one, but still). We'll go into how developers protect your passwords in a future post.
