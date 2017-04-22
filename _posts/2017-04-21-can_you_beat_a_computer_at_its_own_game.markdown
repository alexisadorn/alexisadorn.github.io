---
layout: post
title:  "Can You Beat a Computer at its Own Game?"
date:   2017-04-21 19:58:32 -0400
---


If you code the logic right, no.

And as many movies and TV shows have warned us, that should be scary. We're writing the code that will one day enslave us. We didn't listen!

So I probably didn't just code the new Skynet....probably. But I am very proud to present my first major coding project where I used human logic to make a computer do something fun. *(That can't possibly backfire, right?)*

![](https://i.imgflip.com/1no9bz.jpg)

Alright, now that I have all the killer computer jokes out of my system, I want to lay out my process for finishing this Tic-Tac-Toe with AI project from the Flatiron School.

## To Partner or Not to Partner?

One of the options in this lab was to work with a partner. At first, I thought that would be pretty fun, but I ended up not doing it. I told myself that my schedule was too difficult and I wouldn't be able to find common times to meet, and wondered how long it would take me reaching out to people on Slack to find someone. 

I was just making excuses to make it more convenient for myself to get through the project. In restrospect, I probably should've committed to finding a partner. 

It seems like so much of working as a programmer involves talking to people about your code and *why* you are choosing to code a certain way, learning to listen and understand how *others* talk about code, and even figuring out how to work with complicated, unmatching schedules. I probably would've benefited from these lessons had I been determined enough to find a partner. There's also something daunting about asking a random stranger online to become your work buddy. But it's something I need to get over, because this is what a good developer does. 

Partner or no, I'm still proud of my work. But next time, I'll remind myself not to take the easy way out, and collaborate with my peers.

## Rebuilding the Board and Game

After I had made the decision to work alone, I spent my first chunk of time re-building the game environment. Luckily, we had done this in previous exercises - and I had no problem following the DRY (Don't Repeat Yourself) rule. 

However, I made a decision to not just copy/paste from my previous Github repos. I still took the time to read what the rspec test was asking for, read through my already written code to make sure I knew exactly what it was doing, then copy/paste into my new file if I felt that it was addressing the rspec test. 

So why didn't I just copy/paste from my Github repo? I already did the work, so it's not like I didn't know it, right? Well taking that extra time to read through what I had already written reinforced the knowledge in my brain and was a great refresher on how I had structured the classes and methods. 



There were new rspec tests that I hadn't written code for before - like adding a human and a computer player. Building my human player was a breeze. Inheritance seems to be a concept I get a little faster than things like object relationships and self. Building the computer player, that was more difficult...

## Thinking through the AI Logic

I started off by researching the MiniMax algorithm that was suggested in the lab's instructions. I would like to spend more time learning about that algorithm, but at the time, it seemed very complicated and the lab mentioned that there was a way to think about the logic of a Tic-Tac-Toe game in a more basic sense.

So I sat down with my notebook and played a game of Tic-Tac-Toe and wrote down the thoughts that came to my head when playing on why I was making a decision. This helped me build my logic, which at first was:

```
1. If we are on the first or second turn, check if the middle spot is open
2. If the middle spot is not open, and we are still on our first or second turn, try a corner spot
3. On any other turn, check to see if your token is placed in a 'WIN_COMBINATIONS' combo
4. If you have two tokens in a 'WIN_COMBINATIONS' combo, find out what the missing combo number is, and put your token there
5. Else, check your opponents tokens to see if they have tokens in a 'WIN_COMBINATIONS' combo
6. If they have two tokens in a 'WIN_COMBINATIONS' combo, find out what the missing combo number is, and put your token there
7. Else, choose another spot at random
```

Writing this out was super helpful to get started - I knew what I should start with and where it needed to go.

Now, if you've built a Tic-Tac-Toe with AI before, you might already see what's wrong with my logic above. And I won't go into the full details of my week-long struggle, but essentially as I was coding this, using a lot of `detect` and `any?` enumerators and pulling index numbers into arrays, the stupid thing wouldn't work. I was able to successfully play a couple of rounds before my computer player would go into an infinite loop or raise an error. I just wasn't seeing what was wrong, and all the sudden I had a huge mess of code and comments and commented-out `binding.pry`s. I stepped away from my computer for a couple of days feeling grumpy and out of ideas. 

## Learn Superheros to the Rescue

It was time to call in some back-up. I made an office hours appointment with Corinna, the instructor at Flatiron covering Intro - OO Ruby. That 30-minute meeting was so helpful. 

Corinna helped me re-think my logic a bit. 

If a player has two of the three winning combinations, why even bother checking the middle and corners first? Just make the winning move there. If that isn't a factor, then choose your spot based on the most preferable spots on the board.

She also gave me a great piece of advice: re-write my code. 

Currently, I was looking at a jumbled up mess that I couldn't even remember how I got there. She suggested that I start fresh, and knowing what I have already built and that my logic essentially needed to be flipped around, build it again. 

And that's what I did. My logic now looks like:
```
1. Check to see if the computer has a winning combo with two tokens and one empty space
    --> If yes: put the token in the empty space
2. Check to see if the opponent has a winning combo with two tokens and one empty space
    --> If yes: put the token in the empty space
3. Else, check if the middle space is open
4. Else, check if a corner space is open
5. Else, choose another space at random
```

I was able to rewrite my Computer player AND get it passing all tests and was able to play game after game successfully drawing with the computer each time. 

## Bringing it All Together
The last piece I refined was my CLI. I had been working on this throughout so that I could test my computer player. But now that I knew my computer player was working, I decided to do the fun part and beautify my CLI!

I found a neat Ruby gem called 'colorize', built a cool little design as a welcome to the game, and added some color!

```
puts %q[
            ---------   ---------   ---------
            \__   __/   \__   __/   \__   __/
               | |         | |         | |
               |_|         |_|         |_|
  ].green
  puts "\nWelcome to Tic Tac Toe!".green
  puts "~~~~~~~~~~~~~~~~~~~~~~~".blue
  puts "How many players? [0 (comp v. comp) || 1 (comp. v. human) || 2 (human v. human) || wargames]"
	```
	
Nothing super fancy, but it was fun, and I needed to remind myself that these things should be fun!
	
Lastly, I built a `#wargames` method that has the computer play itself 100 times, then `puts` the number of times won and number of times tied. Number of times tied was 100 - looks like I have one unbeatable computer!

[Check out my code if you'd like](https://github.com/alexisadorn/ttt-with-ai-project-v-000) and feel free to play a game by cloning the repo and running `ruby bin/tictactoe`. 

This was a really great project to get me thinking like a programmer, and feeling stuck like a programmer, and using my resources like a programmer, and having fun with my code, like a programmer. I guess I sound like a programmer now, huh?
	
	


