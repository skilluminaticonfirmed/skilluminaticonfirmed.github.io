---
layout: single
title: "MITRE STEM CTF Cyber Challenge 2019 - Scaredy Cat (Grab Bag 100pts)"
date: 2019-02-24
---

|filename|
|--------|
|gb100-3-d383bbf9756eeb09dab4841044b44a5d15fd6264b28dcfdf180b2f4cabf0a997_1.zip|

|md5sum|
|------|
|5c467f8170026f0997c4f309d1162aa4|

## What is this cat?

This challenge is quite hard to solve if you don't know what to do with image or are unfamiliar with [stegonography](https://en.wikipedia.org/wiki/Steganography) but is quite simple if you are familiar or stumble upon the solution like we did.

We figured we'd give the grab bag challenges a try and having never attempted or seen a stego challenge we stared at the cat while the cat returned our gaze.


![Scaredy Cat](/writeups/images/Scaredy_Cat.png)


## Up all night to get lucky

One of our players happened to notice faint letters on the background of the image when viewed from specific angles. Off to google...


## Stegonography

>"Steganography includes the concealment of information within computer files. In digital steganography, electronic communications may include steganographic coding inside of a transport layer, such as a document file, image file, program or protocol. Media files are ideal for steganographic transmission because of their large size. For example, a sender might start with an innocuous image file and adjust the color of every hundredth pixel to correspond to a letter in the alphabet. The change is so subtle that someone who is not specifically looking for it is unlikely to notice the change."

We were able to solve this challenge by opening the image in mspaint.exe and inverting the image. This allowed us to see the other half of the lettering enough to make out the once hidden flag.

![mspaint Scaredy Cat](/writeups/images/Scaredy_Cat_mspaint.png)

## A Better Way

We knew there had to be a better way and returned to google where we stumbled upon this awesome tool called [Stegsolve](http://www.caesum.com/handbook/stego.htm)
by Caesum.


after flipping through several filters we got the following:

![Half Solved Cat](/writeups/images/halfsolvedcat.png)

![Solved Cat](/writeups/images/solvedcat.png)


### We certianly won't be Scaredy_Cat.png of stego challenges in the future.
