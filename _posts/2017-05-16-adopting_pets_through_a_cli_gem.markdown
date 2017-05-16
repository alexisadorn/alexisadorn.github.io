---
layout: post
title:  Adopting Pets Through a CLI Gem
date:   2017-05-16 01:14:52 -0400
---


When starting out with my CLI data gem final project, the advice I kept hearing was “pick something you are interested in.” I love animals, and I am a big proponent of the #AdoptDontShop movement that encourages everyone to adopt their new best friend from a shelter, rather than buying from a pet store or breeder. I love using any chance I get to educate those around me about the 6.5 million homeless animals who are waiting in US shelters for their loving homes. (That's right...cue the Sarah McLachlan music.) 

![](http://dingo.care2.com/pictures/greenliving/1012/1011871.large.jpg)

I decided that my CLI gem would display adoptable pets and their information from one of my favorite animal welfare nonprofits, [Best Friends Animal Society](http://bestfriends.org/).

I started by drawing how I wanted my program to flow. I knew I would need several classes responsible for different functionality, including:
* A CLI class that would be responsible for getting input from the user and displaying information.
* A Scraper class that scrapes information from the website. 
* Classes to represent the animal – Dog, Cat, etc. 
* A class that pulled the scraping and creating-new-objects functionality together.

I also wrote down the steps for how I would want my user to interact with the program:
1. The user starts the program
2. The CLI asks the user which animal they want to see
3. The user inputs a number that corresponds with the type of animal
4. The program scrapes & creates new animals
5. The CLI displays all available animals of the type the user requested
6. The CLI asks the user which animal they want more information on
7. The user inputs a number that corresponds with the animal
8. The CLI displays additional details about the animal
9. The CLI asks if the user wants to see more animals

Knowing the program’s flow and objects that are needed, I started building out the code from the first step (creating an executable file) and to the final code that asks the user if they want to see more animals. 

Below I go into more detail on each of the classes and some of the major successes & struggles faced with each. If you are interested in the code specifics, please read on. Otherwise, scroll on down to the bottom for links to the GitHub Repo and video walkthrough. 

--------------------------------------------------------------------

**bin/bestfriendfinder**

This is where my user starts the program. The code within this file is small and simply calls my CLI class, which gets the program up and running. 


### CLI
The CLI class introduces my user to the program, asks them which type of animal they are interested in, then sends that information off to the other classes to do their work. 

Once the other classes have performed their duties, the CLI class is responsible for displaying all info to the user, gathering more input from the user, displaying additional pet details to the user and then asking if the user would like to search again.

**Major Struggle & Success**: 
As I started writing my code, I saw something that I knew would eventually be a problem. The website has many animals to choose from – dogs, cats, rabbits, birds, horses, pigs, barnyard and small & furry. Because of this, from my very first method `#start`, I was writing a long `case` statement that in pseudocode said *“If the user types in 1, they want to see dogs. Elsif the user types in 2, they want to see cats…”* and so on and so on. 

This was a pretty big case statement (20 lines!). Then as I was writing a general `AnimalFactory` class that would scrape the page and create specific animal objects, I realized that I would have to do that again - *“if the user wanted to see dogs, scrape the dog page, elsif the user wanted to see cats, scrape the cat page….”.* 

There were many places where I saw myself needing to write extensive case statements and I could imagine my code getting uglier and uglier. I wanted a way to simply save the user’s input in a variable and interpolate the variable where I needed to have it create whatever animal I needed. 

The one aspect that I wasn’t sure about was making objects. If a user tells me that they want to see all available “dogs”, for example, I can easily tell my program to save that input into an `animal` variable, then scrape the website that ends with `www.bestfriends.org/#{animal}`.

But how to take that “dogs” entry, and suddenly turn it into a new object – Dog. 

Luckily, there is the internet! Googling gave me this method:

	`Object.const_get(animal)`

Using this method, I could pass in any argument and my program will turn it into an Object. That was exactly what I needed. So now how do I figure out what variable to pass? A case statement came up again in my head:
	
	```
	#If the user wants “dogs”
	Object.const_get(“dogs”)
       => Dogs
	
	#If the user wants “cats”
	Object.const_get(“cats”)
       => Cats
	```

So this didn’t really fix my original problem of getting rid of those case statements. But then I thought back to the days of Tic Tac Toe, and how we used a fancy little thing called a constant. A constant allows you to set some information about the object that would never change.

So I created a constant called `PETS` set equal to an array of [“Dogs”, “Cats”, “Rabbits”, (etc)] that would match exactly how I want my Objects to be named. 

This ended up saving me 5 lines of code right from the very beginning – instead of welcoming the user and puts-ing… “What animal would you like to view? 
1.	Dogs
2.	Cat
3.	Etc.”
…I just iterated over my constant using `each.with_index(1)`, which listed all of the pets in the correct order. 

Having this array then allowed me pull out the string of the animal, based on the number that the user chose (and converting it to the correct array-indexed number), and then I could pass that as a string if needed, or convert it to an object to perform functions on the class if needed. 

In addition to saving lines, this has the added benefit of ensuring flexibility – so if at any time, I decide to change up the order of how I list my pets in the `PETS` array, the number that the user inputs will always be connected to the correct animal.


### AnimalFactory & Scraper
In the CLI file, the `AnimalFactory.new` method is called and passed the species of the animal in string format, as well as the species of the animal as an object, which are then assigned as instance variables in the AnimalFactory class.  

The AnimalFactory class’ main responsibility is to churn out new animals (like a factory!) It does this by first calling on the `Scraper` object to gather the first page of all adoptable pets and their names, breeds and ages. The Scraper class takes in an argument of the species name, which is appended to the end of the Best Friends website url. The first time we call the Scraper method, we are only collecting a pet’s name, breed, age, and personal page URL.

The Scraper method returns the array of pet details that we just scraped, and we use that to create new animals. We are able to create new animal objects with the details array, and specify the type of animal (`Dog`, `Cat`, etc.) we would like to create by using our animal object that has been stored in an instance variable.

### Animals & animals/Dog, Cat, etc.

Each species of animal lives within the `lib/animals` folder and inherits from the parent `Animals` class, which contains most of the functionality for creating and adding attributes to animals. When the AnimalFactory class calls `#create_new`, it is calling on the individual animal object, but because it inherits from `Animals`, the code lives within the Animals class.

Inheriting from one class follows the DRY principle by ensuring that the methods and attributes are written once. The only thing that each individual animal class has in its class is an `@@all` variable for storing all its instances. 

#create_new creates new instances of an animals and assigns the scraped attributes to each animal object using the `send` method. Each animal is added to its species’ `@@all` array, unless it is already there. 

Now we have a batch of new animal objects with name, breed, age, url and species!

### AnimalFactory #add_attributes_to_animals
After creating animal objects, we want to add more details to each individual animal from their personal pet page. Using the `#add_attributes_to_animals` method, which iterates through the `@@all` variable for the specific type of species that our user has asked for, we will go through each animal instance and scrape the website again, but this time we use the animal’s personal URL. 

We scrape for more details such as size, color, sex and description and return a hash with that information. Now that we have a hash of additional details, we will assign the additional details to each animal object. 

### Animal #add_attributes
`#add_attributes` uses the send method to add the additional attributes as instance variables to our animal object. Then we do a check to see if any of our attributes are missing values.

`#check_attributes_for_nil` is not required to make the program function, but it makes the display for the user a little nicer. 

When we print an attribute like “Size:” for our user, if the animal does not have that specified on the website, our program prints “Size:        ”. The user might wonder if this means there is no information, or if the programmer made an error and this isn’t a great program. 

So this method starts off with an array of all the attributes that we are collecting for each animal object, in string format. Then we define another array that specifies what a missing value would look like – nil or an empty string. 

We then iterate through our attributes array and we use the send method to access the object’s instance variable with the same name. Then we check if the value of that instance variable is equal to nil or an empty string. If it is, we set the instance variable value to “N/A” using the send method. Now, if a user looks up a pet with a missing size, it will list “Size: N/A”, indicating to the user that this information was not available. 

Now our program has fully-detailed animal objects! We will return to our CLI method, and as mentioned above, we display the information for the user, ask the user what other information they would like displayed, and check if the user would like to exit the program or search again.

---------------------------------------------------------------------------------------------------------------------------------

I had a lot of fun creating this from scratch and it’s interesting to see that when I started the OO section of Ruby, my attitude was confusion and frustration. Now, I still need to do research and test things over and over again, but I understand relationships between objects so much better. 

[To see the full code, checkout my GitHub Repo](https://github.com/alexisadorn/BestFriendFinder-cli-app).

[Also check-out the video walkthrough for how to install and use this gem](https://www.youtube.com/watch?v=nJbM3NQelmk&feature=youtu.be). 
