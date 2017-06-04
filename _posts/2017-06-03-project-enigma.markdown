---
title: "Project Enigma - a WWII Inspired Text Cryptography Suite"
layout: post
date: 2017-06-03 15:02
tag:
- project
- cli
- backend
- Mod 1
image: /assets/images/alan-logo.png
headerImage: true
projects: true
hidden: false # don't count this post in blog pagination
description: "This was my first pair project I was assigned in Module 1 of Turing"
categories: [project, blog]
author: samlim
externalLink: false
---

# Historical Context

<div class="side-by-side">
    <div class="toleft">
        <p>Inspired by our school's namesake, <a href="https://en.wikipedia.org/wiki/Alan_Turing">Alan Turing</a>, widely known as the "<em>father of modern computers</em>," was the inspiration for our first large project. My partner and I were tasked with designing an encryption suite using the concepts utilized by the infamous <a href="https://en.wikipedia.org/wiki/Enigma_machine">Nazi Enigma Machine</a> used by the Axis powers during WWII.</p>
        <p>In December of 1942, London-born Alan Turing, a brilliant mathematician educated at prestigious universities Cambridge and Princeton, travelled to the United States to head the <a href="https://en.wikipedia.org/wiki/Hut_8">HUT 8</a> codebreaking team. These people were tasked with carrying out cryptanalysis of all German naval signals; an integral source of intel that eventually led to the Axis' demise.</p>
        <p>Prior to 1932 the encrypted German naval messages were unbreakable; we could intercept them, but we couldn't read them. Polish mathematicians <em>did</em> work out how to read these messages (which they shared with the British). But the Germans were wary; every passing day they worked on increasing the efficiency of their cryptography. Breaking the code by hand was quite the lengthy and arduous process, time the Allies could not spare to waste.</p>
    </div>

    <div class="toright">
        <img class="image-alan" src="https://upload.wikimedia.org/wikipedia/commons/a/a1/Alan_Turing_Aged_16.jpg" alt="Alt Text">
        <figcaption class="caption">Photo of Alan Turing</figcaption>
    </div>
</div>

However around 1940, with the assistance of fellow codebreaker [Gordon Welchman](https://en.wikipedia.org/wiki/Gordon_Welchman), Turing made the monumental breakthrough that led to our eventual victory. He and Welchman created what many refer to as the foundation for modern computing: The [Bombe Machine](https://en.wikipedia.org/wiki/Bombe). This reduced the work of breaking the Enigma code _significantly_. It was designed to discover the daily settings used during each encryption process:

- The set of rotors in use and their relative positions,
- the rotor core start position,
- the message decryption key,
- and the specific layout of the actual wiring in the plugboard.

Convinced their encryption could not be broken, this misplaced sense of invulnerability resulted in the Germans using their machine for _all sorts_ of communication: troop movements, planned invasions and attacks, naval and Luftwaffe activity, wehrmacht transmissions, and most important of all, the immense wealth of wartime secrets known to Germany's secret services.

# How The Machine Worked

<iframe width="560" height="315" src="https://www.youtube.com/embed/ASfAPOiq_eQ" frameborder="0" allowfullscreen></iframe>

# Project Specs

Linked [here](http://backend.turing.io/module1/projects/enigma) are the official specs we were evaluated on. However for those who are afraid of reading links, I will provide a quick summary of how exactly the key and offset work in the context of encrypting/decrypting messages.

## Our Charmap

This is the set of characters we needed to support.
{% gist limsammy/ebeca27cf4ec5e1b68e4d74ae84b4ee6 %}

Generating this charmap as Ruby code was surprisingly a very simple one-liner:
{% highlight ruby %}
def gen_map
  [*('a'..'z')] + [*('0'..'9')] + [' ', '.', ',']
end
{% endhighlight %}

Using the splat operator is incredibly efficient and a great example of [syntactic sugar](https://en.wikipedia.org/wiki/Syntactic_sugar) for generating ranges contained in an `Array` datatype.

## The Key

- Each message uses a unique, randomly generated encryption key
- The key is five digits, like, for example, `41521`
- The first two digits of the key are the _A_ rotation (41)
- The second and third digits of the key are the _B_ rotation (15)
- The third and fourth digits of the key are the _C_ rotation (52)
- The fourth and fifth digits of the key are the _D_ rotation (21)

## The Offset

- The date of the message encryption is grabbed in the format of DDMMYY, for example `020315`*
- This date, as an integer, is squared (412699225) and the last four digits are taken from this squared number (9225)
- The first digit is the _A_ offset (9)
- The second digit is the _B_ offset (2)
- The third digit is the _C_ offset (2)
- The fourth digit is the _D_ offset (5)

*Ruby's built in [DateTime library](https://ruby-doc.org/stdlib-2.3.1/libdoc/date/rdoc/DateTime.html) was immensely helpful for everything related to dealing with and manipulating dates.

## Generating The Full Encryption Key

Once the key and offset are generated, the respective ID's (_A_, _B_, _C_, and _D_) are simply summed together to determine the distance each rotation needs to travel.
In the example above, the rotations would be as follows:
- _A_: 50
- _B_: 17
- _C_: 54
- _D_: 26

# Detailing our Problematic Approach, and Some Bonus Code Snippets :)

My partner and I first decided to research not only Enigma's protocols, but other `String` type encryption methods as well. After messing around with a very simple single-lookup-table [Caesar Cipher](https://en.wikipedia.org/wiki/Caesar_cipher) we settled on our first "module" (this application was written before we learned the correct ettiquete of an actual module), a cipher-table builder. We would build this as the basis of the application, which we named `CipherBuilder`.

Due to the Caesar Cipher's methodology of a lookup-table, we settled on representing our four virtual "rotors," or _lookup-tables_, as a `Hash` datatype - the keys being our ordered charmap, and the values being the charmap with the shift being the key + offset for each index.

*Note:* Due to leading zero's being excluded in `Integer` datatypes, we had to write a quick hack to cast that variable as a `String` while it's being stored. From there, we had to convert it back to an `Integer` for all of the math-related functions, and of course the logic of determining whether or not a key needs a leading 0 added or not.
Here is that snippet of code:
{% highlight ruby %}
def generate_key(length = @length)
  key = rand(10**@length)
  key = '0' + key.to_s if key.to_s.length < 5
  key.to_s
end
{% endhighlight %}

Another issue we faced was with our _cracking_ method. Not only did we have to implement both encryption AND decryption, but we had to write a function that would break the encryption without a provided key and offset - much like the issue Alan Turing faced over 70 years ago.

My partner and I figured there are two ways to accomplish this task:

- a [bruteforcing](https://en.wikipedia.org/wiki/Brute-force_attack) approch
- and an algorithmic approach

## Bruteforce Attack

Since we tackled this cracking problem after designing the actual Enigma machine, we were pretty familiar with the ins and outs of how exactly the machine worked. Ours even was a bit more user-friendly and easier-to-understand than an `Array` based solution or whatever else all you clever programmers might come up with, as using key-value pairs (the basis of how a `Hash` (*BONUS FACT:* _in Javascript these are known as `Collections`, and in Python `Dictionaries`) datatype works) to lookup the translation for the respective character.

This approach essentially allowed us to break the encryption process in 5 distinct pieces: The _A_, _B_, _C_, and _D_ specific index calculations, and then the very simple application of the `offset` (a number generated from the current date squared*) variable. With that info we were able to figure out the constants of each and every message; it always ends in the same 5 characters: `..end..`. From there we simply ran a ~technically infinite loop trying all shifted posibilities of the given charmap until `..end..` was succesfully translated.

*Our `offset` generation method (the `length` instance variable is defaulted to 4 digits, as indicated in our project spec):
{% highlight ruby %}
def generate_offset(length = @length)
    date = Date.today.strftime('%d%m%y').to_s.to_i
    offset = (date**2).to_s[-4..-1].to_i
    offset = '0' + offset.to_s if offset.to_s.length != 4
    offset
end
{% endhighlight %}

## Algorithmic Approach

Considered the "smarter" way to crack this cipher (smarter as in much more efficient, less memory-intensive), an algorithmic approach to cracking the Enigma encryption involves the _modular_ math operator. With this, you determine the length of the message. Determining the length of the message allows one to determine if the last character of the encrypted message is an _A_, _B_, _C_, or _D_ index. With a series of conditionals, determining this is relatively simple. Once determined, you can use the adjacent characters of the last letter or symbol to determine their respective cipher table identities, and once you are able to assign the encrypted character to either _A_, _B_, _C_, or _D_, you can use the given information that all messages end in `..end..`, and calculate the distance between the encrypted character, and it's respective identity.

For example, if we encrypted the following message, `sample message to encrypt..end..` following the standards of ending in `..end..`, we encrypt it with a key of `35929` and an offset of `0689`. The final rotations are
- _A_: 35 + 0 = `35`
- _B_: 59 + 6 = `65`
- _C_: 92 + 8 = `100`
- _D_: 29 + 9 = `38`

To encrypt this manually, we would shift the _A_ indices of the message by 35 (so from the example message, the following letters fall under the _A_ index: `s, l, m, a, t, n, p, ., .`). To find the translated encrypted version, you simply shift that letter by the distance calculated by the key and offset. You would then go on to perform this task for each of the four indices. Encrypting that example message with the aforementioned example key and offset gives us the following encrypted message:

`f08o.4tl4fb,64tsbx0m2ehogyuda3u `

If we shift each of the _A_ indices in that encrypted message by what we calculated earlier (35) using the given charmap, we end up getting back to the character `s`, as `f` is 35 indices away from the starting character*.

*The charmap is wrapped, so when a rotation reaches the last index, it goes back to `[0]` aka `A`.

Following that protocol for each individual index allows one to translate the encrypted string back to the original message.

So in the context of creating a fully automated system to perform the crack, I employed two methods:

* a rotation function to shift the cipher tables as per index
{% highlight ruby %}
def rotation(message, rotation_id)
    switch = message.length % 4
    encrypted_char = message[rotation_id-switch]
    given = '..end..'
    decrypted_char = given[rotation_id-switch]
    message_rotation = (@char_map.index(encrypted_char)) - (@char_map.index(decrypted_char))
    message_rotation % @char_map.count
end
{% endhighlight %}

* and finally, the logic in the form of several conditionals for actually performing the subtraction on the cipher values, shifting them to match with their correct key-index
{% highlight ruby %}
def crack(message, offset=@offset)
    final = []
    message.chars.map!.with_index do |char, index|
      case index % 4
      when 0
        rotation = @char_map.index(char) - rotation(message, -4)
        decrypted_char = @char_map.rotate(rotation).shift
        final << decrypted_char
      when 1
        rotation = @char_map.index(char) - rotation(message, -3)
        decrypted_char = @char_map.rotate(rotation).shift
        final << decrypted_char
      when 2
        rotation = @char_map.index(char) - rotation(message, -2)
        decrypted_char = @char_map.rotate(rotation).shift
        final << decrypted_char
      when 3
        rotation = @char_map.index(char) - rotation(message, -1)
        decrypted_char = @char_map.rotate(rotation).shift
        final << decrypted_char
      end
    end
  final.join
end
{% endhighlight %}

# Installing and Usage

Prerequisites:
- Ruby => 1.9
- RVM
- Git (Optional)

1. Clone or [download](https://github.com/limsammy/project_enigma/archive/master.zip) [my repo](https://github.com/limsammy/project_enigma) to a local directory.
`git clone git@github.com:limsammy/project_enigma.git`

2. Navigate into the directory
`cd project_enigma`

3. *OPTIONAL STEP:* Run `bundle` if you would like to run my testing suite or see test coverage

## Encrypting a Message/File

1. Create a new text file with the contents of what you would like to encrypt
`touch to_encrypt.txt`
`echo "Encrypt this example message plz..end.."` << to_encrypt.txt`

2. Run the following command to output a txt file of the encrypted message. Use the format `ruby lib/encrypt.rb $NAME_OF_PLAINTEXT_FILE $OUTPUT_FILE_NAME`
Using the example file names:
`ruby lib/encrypt.rb to_encrypt.txt encrypted_message.txt`
(This will output a file called encrypted_message.txt in the parent directory with the encrypted message)

3. A success message containing the key and offset will be printed in terminal. Following the example message, the following will be outputted:
`$ Created file encrypted.txt with key 93707 and offset 0689`

## Decrypting an Encrypted Message/File

1. Create a new text file, or use an existing encrypted file, with the contents of what you would like to decrypt
`touch to_decrypt.txt`
`echo "rc72ttnxli8bixqqtlubqe8wegubtlccce3hc."` << to_decrypt.txt`

2. Run the following command to output a txt file of the decrypted message. Use the format `ruby lib/decrypt.rb $NAME_OF_FILE_TO_DECRYPT $DESIRED_OUTPUT_FILE_NAME $KEY $OFFSET`
Following our example:
`ruby lib/decrypt.rb to_decrypt.txt decrypted.txt 93707 0689`
(This outputs a decrypted.txt file containing the real message)

3. A success message is printed to console:
`$ Created file 'decrypted.txt' with key 93707 and offset 0689`

## Cracking an Encrypted Message/File

1. Create a text file containing an encrypted message you would like to crack
`touch to_crack.txt`
`echo "rc72ttnxli8bixqqtlubqe8wegubtlccce3hc." << to_crack.txt`

2. Run the following command in terminal in the format
`ruby lib/crack.rb $FILE_WITH_ENCRYPTED_MESSAGE $OUTPUT_FILE_NAME`
Following our example;
`ruby lib/crack.rb to_crack.txt cracked.txt`

3. The following success message will be printed to console:
`$ Created file cracked.txt`

Here is our cracker code:
{% highlight ruby %}
def crack(message, offset=@offset)
    final = []
    message.chars.map!.with_index do |char, index|
      case index % 4
      when 0
        rotation = @char_map.index(char) - rotation(message, -4)
        decrypted_char = @char_map.rotate(rotation).shift
        final << decrypted_char
      when 1
        rotation = @char_map.index(char) - rotation(message, -3)
        decrypted_char = @char_map.rotate(rotation).shift
        final << decrypted_char
      when 2
        rotation = @char_map.index(char) - rotation(message, -2)
        decrypted_char = @char_map.rotate(rotation).shift
        final << decrypted_char
      when 3
        rotation = @char_map.index(char) - rotation(message, -1)
        decrypted_char = @char_map.rotate(rotation).shift
        final << decrypted_char
      end
    end
    final.join
  end

  def rotation(message, rotation_id)
    switch = message.length % 4
    encrypted_char = message[rotation_id-switch]
    given = '..end..'
    decrypted_char = given[rotation_id-switch]
    message_rotation = (@char_map.index(encrypted_char)) - (@char_map.index(decrypted_char))
    message_rotation % @char_map.count
  end
{% endhighlight %}