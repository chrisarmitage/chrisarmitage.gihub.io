---
layout: post
title: "P is for Passwords"
comments: false
tags:
  - A to Z 2013
published: false
---

Passwords have become an ingrained part of our life. For a developer like me, I have unique passwords for multiple servers and accounts that I have to use on a daily basis. Luckily, maybe because I’ve been doing this for a while, I have a knack for remembering them and can jump from system to system without frying my brain too much.

There have been a few high-profile password thefts in recent years, so it’s worth understanding what actually happens to your password and how you can help keep yourself from trouble.

The first way a website can store a password is plain text. Hopefully any website doing so will fire their technical staff before you try to create an account there. This means in their database, there’ll be a line containing “name = chris, password = swordfish”. If this data is ever stolen, your password is there for all to see. Now if the website has been hacked, the damage they could do with the password there is probably limited. But how many of you use the same password for different sites? The hacker can now access your email, e-commerce accounts, maybe even your financial records!

Whenever I see a website that states a maximum length of password, I go on the defensive. As you’ll see below, the only reason to say a password must be less that 12 characters is they’re storing it in plain text and have only allocated 12 characters in the database.

At the lowest level of security, a website can do a one-way hash (a mathematical funciton that only works in one direction). In PHP, it’s common to use the md5() function. This will turn “swordfish” into “15b29ffdce66e10527a65bc6d71ad94d”. As this is a one-way hash, it’s impossible to turn that long string of characters back in to your password. When you log on, they take what you entered, hash it again, and compare it to the value they stored when you created your account. Nice and safe, yeah?

Until you [search Google for the hash](https://www.google.co.uk/search?q=15b29ffdce66e10527a65bc6d71ad94d). It is still a one-way hash, but hackers soon realised they could run through a dictionary and generate all the md5 hashes to make what’s known as a rainbow table; a reverse to the md5 hash. If the website has any wits about them, they can ‘salt’ the hash, changing it into something unique. So now, “mywebsite.swordfish.47″ turns to “47c7bded6b9dedf5be43c442fb82e2e6″, which isn’t in any Google results. As long as the website keeps the hash secret it’s now (hypothetically) impossible to turn that hash into the original password.

The eagle-eyed amongst you may have noticed that even though the second password was a lot longer than the first, the hash was the same length, 32 characters. This is the nature of md5. So if any password (or any book!) can be reduced to a 32 character line, why do must I have a password of less than 12 characters?

On the technical side of it, md5 is now considered weak for passwords, though to be fair it was never designed for that use. Far better functions like sha() and bcrypt() mean rainbow tables are useless. Oddly, these function are also engineered to be slow, the opposite of every other computer function. This is to deter hackers from generating their own scripts to try to brute-force a password. If you’re dropping the calculations from hundreds of thousands a second to two a second, it’ll take a very devoted hacker to see it through.

Hopefully this has made some of the dark arts of websites a little more transparent, and helped explain why high-profile thefts (the Playstation Network one of the most publicised) can cause major problems for the users if decent password hashing wasn’t used.
