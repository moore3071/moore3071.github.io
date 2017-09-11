---
layout: post
title:	"Non-repudiation in information security"
date:	2017-09-10
categories: blogging
---
# Vocab and what is non-repudiation

I originally planned on writing this post on common attacks on services as I mentioned in a previous post. That won't be this post, but should be coming up in the next post.

 While doing a little research, I found that there's an interesting conversation in the information security community about how the feature of non-repudiation fits into infosec. I'll be giving a small summary of the debate and my own opinion on the debate. First though, we should probably go over some basics. First there are [multiple definitions of information security](https://en.wikipedia.org/wiki/Information_security#Definitions). We'll be sticking with the ISO/IEC definition:

>preservation of confidentiality, integrity, and availability of information

This requires three more definitions: confidentiality, integrity, and availability. The definitions below are stolen from [ISO/IEC 27000:2016](http://standards.iso.org/ittf/PubliclyAvailableStandards/), the English version (although feel free to read the French version if you prefer).

- Confidentiality: property that information is not made available or disclosed to unauthorized individuals, entitites, or processes
- Integrity: property of accuracy and completeness
- Availability: property of being accessible and usable upon demand by an authorized entity

The non-repudiation debate is more closely related to authentication, a major component of access control. So let's walk through the steps of access control along with a few examples.

| Step name | Definition | Logging into FaceSpace | Running `sudo shutdown -Ph now` | Ordering a drink at a bar |
|---------------------------------------------------------------------------------------------------------------|
| **Identification** | Declare who you are | Your username is you declaring who you are | The system grabs your uuid as your id | You ask for a lager implicitly identifying yourself as older than 21 |
| **Authentication** | Prove you are who you say you are | You enter your password and it matches the one on file with your username | You enter your password and it matches the one on file with your username | You hand over your ID and the birthdate shows that you're 22 |
| **Authorization** | Check if the authenticated user is allowed to perform this action | The user is displayed his page | The system sees that you are in the sudoers file and executes shutdown turning off the computer | Since 22>21, the bartender gives you a lager |
{: .bordered-table }

Each step relies on the previous one, and in fact non-repudiation relies on authentication too. In fact, a Stackexchange user lays out a list of requirements for non-repudiation in [this answer](https://security.stackexchange.com/a/48728). A simple graph of these dependencies looks like:

<svg viewBox="0 0 500 300" xmlns="http://www.w3.org/2000/svg">
	<defs>
		<circle id="svgCircle" r="30" style="fill: white; stroke: black;"> </circle>
		<g id="svgArrow">
		<path fill="black" d="m0 0 v 10 h -70 v 10 l -20 -20 l 20 -20 v 10 h 70 v 10 Z"/>
		</g>
	</defs>
		
	<g>
		<use x="40" y="40" href="#svgCircle"/>
		<text font-size="8" textLength="55" stroke="black" text-anchor="middle" x="40" y="40"> Identification </text>
	</g>
	<g>
		<use x="250" y="40" href="#svgCircle"/>
		<text font-size="8" textLength="55" stroke="black" text-anchor="middle" x="250" y="40"> Authentication </text>
		<use x="185" y="40" href="#svgArrow"/>
	</g>
	<g>
		<use x="460" y="40" href="#svgCircle"/>
		<text font-size="8" textLength="55" stroke="black" text-anchor="middle" x="460" y="40"> Authorization </text>
		<use x="395" y="40" href="#svgArrow"/>
	</g>
	<g>
		<use x="250" y="225" href="#svgCircle"/>
		<text font-size="8" textLength="65" stroke="black" text-anchor="middle" x="250" y="225"> Non- <tspan dy="1em" x="250">Repudiation</tspan> </text>
		<use x="185" y="225" href="#svgArrow"/>
		<use x="315" y="225" transform="rotate(180 315 225)" href="#svgArrow"/>
		<use x="250" y="175" transform="rotate(90 250 175)" href="#svgArrow"/>
	</g>
	<g>
		<use x="40" y="225" href="#svgCircle"/>
		<text font-size="8" textLength="55" stroke="black" text-anchor="middle" x="40" y="225"> integrity </text>
	</g>
	<g>
		<use x="460" y="225" href="#svgCircle"/>
		<text font-size="8" textLength="55" stroke="black" text-anchor="middle" x="460" y="225"> Availability </text>
	</g>
</svg>

It's clear that for non-repudiation we need to know all four of the above indicated dependencies: identification, authentication, availability, and integrity. Identification and Authentication tells us that we are dealing with Alice. If we wanted to be a bit more accurate with ISO/IEC terminology, I would opt for the term authenticity to convey these both at once, but either way gets the point across informally. Availability means that the info can be retrieved by authorized users and can't be accidentally lost by being deleted or otherwise. Integrity means that the information can be assured to be correct including the author and that we can tell that the information hasn't been tampered with.

So, what does the ISO/IEC document define non-repudiation as?

> ability to prove the occurrence of a claimed event or action and its originating entities

# What's the debate about?

So, we know what non-repudiation is, now what's the actual debate in infosec about non-repudiation? When looking on the security StackExchange about non-repudiation, you'll end up finding someone claiming that non-repudiation is not a technical term, but instead a legal term. One very well-written answer claiming this with a handful of sources can be found [here](https://security.stackexchange.com/a/6108). Others claim that it is (also) a technical term in cryptographic schemes. Finally there's the belief in non-repudiation also being a technical term in infosec.

# Legal non-repudiation

Typically when someone argues any of these sides, they put it in terms of a specific application or field. For those who argue that non-repudiation is a purely legal term, this is accountability. You can't through technological terms prove that Alice committed an action that she attempts to repudiate. You can make a system that shows that Alice's account performed the action, but it is exceedingly difficult to show that Alice was the one using her account. For infosec, this is clearly a problem in access control if another person can access Alice's account. Legally we also have to consider if we can verify that Alice was the originating entity, then was Alice performing this under duress or while legally insane? If so then she can repudiate her actions to an extent. 

# Crytographic non-repudiation

If we talk about cryptographic non-repudiation, then we're playing a theoretical game. Just like physics where you discount air resistance and friction, basic cryptography assumes some ideal conditions. Cryptographic schemes assume that secrets such as your secret key or passwords are kept secret, the scheme is the only attack vector, etc. More advanced cryptography and information security get rid of these ideal conditions and try to be more realistic.

# Back to the definition

The major argument you'll find in support of non-repudiation being a technical term, is that it IS a technical term, but in fact an anti-feature. They'll state that it's clear that repudiation should be an option for users whether that's cancelling an order or transaction, or revoking a secret key (e.g. PGP). So, that's that.

That last argument is close to the truth, but requires slightly more inspection. Recall what non-repudiation is according to ISO/IEC: the `ability to prove the occurrence of a claimed event or action and its originating entities`. It would help if we knew what an entity is. Unfortunately there's no definition in this ISO/IEC document, but there are other instances including:

> risk-owner: person **or** entity with the accountability and authority to manage a risk.

It would seem from this sentence that people are not including in the set of entities. If this were true, then the conversation would change considerably. If we look at the definition for `trusted information communication entity` refers us to organizations which does reference people. Meanwhile ISO 29110 includes projects and enterprises. It would seem that entity is a flexible term, and as such it would be perfectly acceptable to include accounts (you can also include servers and more if you're so inclined). We can reasonably prove to a third party that a transaction was performed by Alice's account or through a certain server.

So, we can be certain that a transaction came from Alice's account, and we can prove this to a third party. So, we can make Alice aware of this transaction, but what if she didn't make this transaction? Well, if possible Alice should have the option to repudiate this transaction and undo it. It's possible that a system is made without the ability to recover from the transaction, but repudiation is a much better goal than non-repudiation to a human level. Non-repudiation of non-human entities (e.g. accounts and servers) is a more reasonable feature/goal, but there are applications where non-repudiation of any type is an antifeature.

# Non-repudiation as an antifeature

Non-repudiation can be a significant problem when pursuing deniability. This may be the case for journalist seeking protection for their sources, and one application that seeks plausible deniability is [Off-the-Record Messaging](https://otr.cypherpunks.ca/).
