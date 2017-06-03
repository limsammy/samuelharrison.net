---
title: "Project Enigma - a WWII inspired encryption suite"
layout: post
date: 2017-06-03 15:02
tag:
- project
- cli
- backend
<!-- image: https://koppl.in/indigo/assets/images/jekyll-logo-light-solid.png -->
<!-- headerImage: true -->
projects: true
hidden: true # don't count this post in blog pagination
description: "This was my first pair project I was assigned in Module 1 of Turing"
category: project
author: samlim
externalLink: false
---

## Historical Context
Inspired by our school's namesake [Alan Turing](https://en.wikipedia.org/wiki/Alan_Turing), widely known as the "_father of modern computers_," we were tasked with designing an encryption suite using the concepts utilized by the infamous [Nazi Enigma Machine](https://en.wikipedia.org/wiki/Enigma_machine) used by the Axis powers in WWII.

In December of 1942, London-born Turing, a brilliant mathematician educated at prestigious universities Cambridge and Princeton, travelled to the United States to lead the [HUT 8](https://en.wikipedia.org/wiki/Hut_8) codebreaking team. These people were tasked with carrying out cryptanalysis of all German naval signals; an integral source of intel that eventually led to the Axis' demise.

Prior to 1932 the encrypted German naval messages were unbreakable; we could intercept them, but we couldn't read them. Polish mathematicians _did_ work out how to read these messages (which they shared with the British), but even with the fact that the Germans were working on increasing the security of their encryption methods daily, breaking the code was quite the lengthy and arduous process, time the Allies could not spare to waste.

However around 1940, with the assistance of fellow codebreaker [Gordon Welchman](https://en.wikipedia.org/wiki/Gordon_Welchman), Turing made the monumental breakthrough that led to our eventual victory. He and Welchman created what many refer to as the foundation for modern computing: The [Bombe Machine](https://en.wikipedia.org/wiki/Bombe). This reduced the work of breaking the Enigma code _significantly_. It was designed to discover the daily settings used in the Enigma machine:

- The set of rotors in use and their relative positions,
- the rotor core start position,
- the message decryption key,
- and the specific layout of the actual wiring in the plugboard.

Convinced their encryption could not be broken, this misplaced sense of invulnerability resulted in the Germans using their machine for _all sorts_ of communication: troop movements, planned invasions and attacks, naval and Luftwaffe activity, and most important of all, the immense wealth of wartime secrets known to Germany's secret services.

## How It Worked
