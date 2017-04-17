---
layout: post
title:  "My Struggles through Object-Oriented Programming"
date:   2017-04-17 22:13:10 +0000
---

The *"Next Blog Assignment" PAST DUE* notice has been staring me down for weeks now, but I had no clue what to write about. I didn't feel like I knew anything well enough to write about it. So I decided to write about what I don't know, and the struggles that I faced, and am still facing, while going through OO Ruby. 

The concept of objects and Object-Oriented programming was brand new to me when I joined Flatiron. I've coded in C before, so never really learned about objects.

![](https://cboard.cprogramming.com/attachments/general-discussions/11739d1339529618-programing-jokes-huh-cliche-485824_10150717848488360_290539813359_9240665_749831047_n-jpg)

So the beginning lessons on Object-Orientation seemed fairly straightforward – a dog is an object, an owner is an object, and an owner has a dog, and a dog belongs to an owner (unless you live in Boulder, Colorado, where dogs have guardians). That makes sense.

Then the lesson started getting into class methods vs. instance methods and using self. This took me a little more time to understand. I remember reading and re-reading lines over and over again, inflecting different words to see if it made more sense in my brain that way. 

I was told that the Music CLI lab was where students begin to feel the “imposter syndrome”. But that feeling actually came to me in the “Collaborating Objects” lab when I had to connect artists and songs through a number of different methods. 

The hardest part about object-oriented programming for me is not being able to see the flow. I would sit with pry open saying out loud to myself “Okay, so now this value gets returned and it goes over….here….and then it does….something.” First lesson learned - taking the time to map out where things are going and how things are returning is so crucial for me to really understand how my code works.

*Side rant*: Ruby is supposed to make programming easier to read, right? Then why take out `return`? I find writing `return song` at the end of a method so much easier to understand than trying to figure out what my last line is returning. Yeah, I know it’s an extra line of code and why bog down my program with more lines of code. But there’s something much easier for my brain to understand when the method is telling me exactly what I was returning. If typing `return` was a rule in Ruby, I probably would’ve saved myself lots of headache trying to figure out why my `#add_song` method wasn’t working! Okay, side rant over.  

I'm happy to say that I did finish the Music CLI lab with only a couple flare-ups of imposter syndrome. A few of my more important take-aways from my first serious attempt with OOP:
* Writing, and saying OUT LOUD, the flow of my programs is key to understanding what it is actually doing. It also helps to avoid the endless loops that you can trap yourself into when trying to reciprocate relationships.
* Remember to always check what your method is actually returning – is it what you want it to return?
* Stop worrying about whether the code is 'perfect' when you type it. This is a hard one for me. Just type something, anything, and get it to work! Later, go back and make it pretty.
* Don't wait *too* much later to go back and make it look pretty, otherwise you'll forget why you wrote something a particular way. Keep at it every day so your thought process sticks. 

I'll take these lessons into my Tic-Tac-Toe AI and CLI gem project. See ya everyone - I'm off to create artificial life from a children's game!

![](https://pbs.twimg.com/media/BzmDP_yCEAAvXdx.png)


