---
layout: post
title:	"Rust and actual rust"
date:	2017-08-14
categories: blogging
---
Over the weekend, I drove out to visit a friend graduating from USMC Officer Candidate School in Quantico, Virginia. Despite the inevitable sickness that comes with the constant exertion and limited sleep, my friend was doing remarkedly well. We had limited time to fraternize, but it was clear that he genuinely enjoyed what he was doing, and I wish him well through The Basic School and his career.

One thing that caught my eye on the Marine Corps Base (besides seeing one of HMX1's presidential helicopters) was the large number of rusted decommissioned vehicles which had me as a history nerd struggling to recall what 20th century vehicles they were. This fit even better with my current interest in Mozilla's (relatively) new systems language Rust.

Before leaving for Quantico, I realized that I needed some audio to keep my sanity on the long cartrip. Instead of listening to a mix of stati-filled stations, I loaded up some podcast episodes on my Zune (I'm kidding, it's a Walkman). The podcast that I decided would accompany me was the [New Rustacean](http://www.newrustacean.com/) podcast. Episode 12 focused on how Rust is expression-based as compared to statement-based and episode 2 focused on Rust's unusual ownership model. I've been working on and off again on my project `upm` or `universal package manager` in Rust. The roadtrip provided a great opportunity to get into a Rustacean state of mind, instead of living in the days of C and C++ systems programming.

Let's talk about upm. upm is meant to allow access to many package managers in a single interface (much like packagekit and its GUI frontends). It should be noted that I haven't messed with anything using PackageKit since a brief exploratory install of Fedora, but to the best of my knowledge PackageKit is difficult to add a package manager to and doesn't support language package managers (e.g. Ruby gems, Python pip). Additionally, I'm uncertain how PackageKit handles packages offered by multiple managers, as would be the case in BedRock Linux.

It is my hope that upm will allow for easy addition of supported package managers even on the user's own system via easy configuration files while striving for a small, and concise amount of code as middleware between the user and the actual package managers. The project has grown slowly and cautiously as my first systems program that I hope to expand beyond myself. While I'm happy with my current plans for `upm` it's clear that there are a number of improvements to be made in my work flow.

First, let's talk about the Rust-based problems. As my first major Rust project, I came into this project with a limited understanding of what makes Rust so unique. As such, my ideas for the project have changed over time, and even my decision to make the project an executable only instead of separating the logic into a library that can be used later on.
