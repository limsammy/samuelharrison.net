---
title: "Project Enigma - a WWII Inspired Text Cryptography Suite"
layout: post
date: 2017-06-03 15:02
tag:
- project
- cli
- backend
<!-- image: https://koppl.in/indigo/assets/images/jekyll-logo-light-solid.png -->
<!-- headerImage: true -->
projects: true
hidden: false # don't count this post in blog pagination
description: "This was my first pair project I was assigned in Module 1 of Turing"
categories: [project, blog]
author: samlim
externalLink: false
---

## Historical Context

<div class="side-by-side">
    <div class="toleft">
        <p>Inspired by our school's namesake <a href="https://en.wikipedia.org/wiki/Alan_Turing">Alan Turing</a>, widely known as the "<em>father of modern computers</em>," we were tasked with designing an encryption suite using the concepts utilized by the infamous <a href="https://en.wikipedia.org/wiki/Enigma_machine">Nazi Enigma Machine</a> used by the Axis powers in WWII.</p>
        <p>In December of 1942, London-born Turing, a brilliant mathematician educated at prestigious universities Cambridge and Princeton, travelled to the United States to lead the <a href="https://en.wikipedia.org/wiki/Hut_8">HUT 8</a> codebreaking team. These people were tasked with carrying out cryptanalysis of all German naval signals; an integral source of intel that eventually led to the Axis' demise.</p>
    </div>

    <div class="toright">
        <img class="image" src="https://upload.wikimedia.org/wikipedia/commons/a/a1/Alan_Turing_Aged_16.jpg" alt="Alt Text">
        <figcaption class="caption">Photo of Alan Turing</figcaption>
    </div>
</div>

Prior to 1932 the encrypted German naval messages were unbreakable; we could intercept them, but we couldn't read them. Polish mathematicians _did_ work out how to read these messages (which they shared with the British), but even with the fact that the Germans were working on increasing the security of their encryption methods daily, breaking the code was quite the lengthy and arduous process, time the Allies could not spare to waste.

However around 1940, with the assistance of fellow codebreaker [Gordon Welchman](https://en.wikipedia.org/wiki/Gordon_Welchman), Turing made the monumental breakthrough that led to our eventual victory. He and Welchman created what many refer to as the foundation for modern computing: The [Bombe Machine](https://en.wikipedia.org/wiki/Bombe). This reduced the work of breaking the Enigma code _significantly_. It was designed to discover the daily settings used in the Enigma machine:

- The set of rotors in use and their relative positions,
- the rotor core start position,
- the message decryption key,
- and the specific layout of the actual wiring in the plugboard.

Convinced their encryption could not be broken, this misplaced sense of invulnerability resulted in the Germans using their machine for _all sorts_ of communication: troop movements, planned invasions and attacks, naval and Luftwaffe activity, and most important of all, the immense wealth of wartime secrets known to Germany's secret services.

## How It Worked

<iframe width="560" height="315" src="https://www.youtube.com/embed/ASfAPOiq_eQ" frameborder="0" allowfullscreen></iframe>

## Approach Details

My partner and I first decided to research not only Enigma's protocols, but other `String` type encryption methods as well. After messing around with a very simple single-table [Caesar Cipher](https://en.wikipedia.org/wiki/Caesar_cipher) we settled that the first "module" (this application was written before I learned real modules) we would build as the basis of the application would be a `CipherBuilder` object. Due to the Caesar Cipher's concept of a lookup table, we settled on representing our four virtual "rotors," or _lookup tables_, as a `Hash` datatype - the keys being our charmap, and the values being the charmap with the `offset` variable generated with the current date and the `rotation` variable being a randomly generated 5 digit number.

*Note:* Due to leading zero's being excluded in `Integer` datatypes, we had to write a quick hack to cast that variable as a `String` whilst it's being stored, converting it back to an `Integer` for all math-related functions, and of course the logic of determining whether a key needs a leading 0 added or not. Here is that snippet of code:
{% highlight ruby %}
def generate_key(length = @length)
  key = rand(10**@length)
  key = '0' + key.to_s if key.to_s.length < 5
  key.to_s
end
{% endhighlight %}
