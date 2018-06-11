---
layout: post
title:      "Bingeworthy - a Rails TV Show App"
date:       2018-06-11 03:32:16 +0000
permalink:  bingeworthy_-_a_rails_tv_show_app
---

> **Check out my project at https://github.com/alexisadorn/bingeworthy**

Getting to the final project is Rails was such an exciting and relief-filled moment for me. I had been struggling through the Rails section, especially towards the end, and then a travel adventure made me lose all momentum. I ended up going back and re-doing many of the Rails labs to remind myself of several key concepts, like nested routes and authentication; so, reaching the final level was exciting.

I had already had an idea for an app in my head based on my own personal frustrations. I was going to create an app where you can keep a list of all the TV shows you are watching so that you don’t forget about them. I have a small note list on my phone with reminders to stay caught up on Silicon Valley or don’t forget about Outlander while it’s on mid-season break! But I thought there could be a better solution than a note on my phone.

*(Halfway through my project, I decided to google my idea and found many, many other apps that do that, and better than mine would. **Tip #1**: Don’t google to see if your idea already exists! It probably does, but build it anyway!)*

### Creating the Models
Figuring out my models and how they associated to each other, and how to get a user-submittable attribute on one of the join models, was the hardest part of my project.

You don’t want to set everything up only to realize it is completely wrong halfway through when you’ve built most of the functionality. It’s important to think through as much as you can how things will look when you set up the models. 

I knew I needed to have a Show. I also had to have some sort of list model to put the show on. I’d also need a User that could have many lists and have many shows through those lists. 

But which model should I put the user-submittable attribute on? And how do I represent that User A has the show “Game of Thrones” on their Caught-Up list because they are all caught up, and User B has the same show on their Behind list because they are several seasons behind. 

I drew out the relationships on pieces of paper and tried to make sense of how things would fit together and be submitted in the app. 

![](https://dzwonsemrish7.cloudfront.net/items/1W263O3h1o0R3G2L192i/notes.jpg)
![](https://dzwonsemrish7.cloudfront.net/items/1a3J391m411B0E0N1s3Z/notes2.jpg)

After several rewrites of my database columns and association macros, and copious amount of `rails console` tests, I had a structure that I liked:

A User model, Watchlist model, and Show model would all be connected through a joins model called Listing, which represents a user’s unique relationship with one show on one list, and would allow the user to submit information about this relationship onto that model (like what season the user is on and if it is one of the user’s favorite shows). 

With the basic structure in place, I began building out the models, then controllers and views.

### Validations
I find validations to be fun! I love the interesting combination of things that you can validate, and I love how simple and easy to read the macros are for setting validations.

I set some basic validations on most of my models (like making sure names and titles were present), and had a little bit more fun creating a custom validation. 

```
class Listing < ApplicationRecord
  # code here
  validate :user_season_vs_current_season

  def user_season_vs_current_season
    if user_season.present? && user_season > show.current_season
      errors.add(:user_season, "can't be ahead of the show's Season #{show.current_season}")
    end
  end
end
```

My custom validation made sure that when a user adds a show to one of their lists, which creates a new Listing instance, the season the user is on can’t be ahead of the show’s season. This seemed like a pretty basic thing to validate for – it wouldn’t make sense for a user to say they were on season 10 of Game of Thrones when there’s only 7 seasons!

### Rspec
I decided I want to try setting up Rspec for this project. It is something that my current tech company uses and I wanted the experience of writing the tests. 

This ended up being quite a chore! 

I’m not going to write out the instructions for how I set it up here because the links below really were the guiding force for helping 1) get Rspec set up 2) create factories to reuse model instances and 3) learn about the structure and organization of Rspec tests. 

* [Setting up Rspec and Factory Bot](https://medium.com/@lukepierotti/setting-up-rspec-and-factory-bot-3bb2153fb909)
* [Faker – Library for generating fake data](https://github.com/stympy/faker)
* [Factory Bot cheatsheet](https://devhints.io/factory_bot)
* [Testing with Rspec, FactoryGirl, Faker and Database cleaner](https://medium.com/brief-stops/testing-with-rspec-factorygirl-faker-and-database-cleaner-651c71ca0688 )
* [Shoulda Matchers](http://matchers.shoulda.io/)
* [Rspec](http://rspec.info/)

I added some basic model Rspec tests into my application, but because I was learning and teaching myself this new testing process, my project was sitting idle and very little Rails work was being done.

In the future, I will be continuing to add on more specs to this application to learn more Rspec techniques, but in terms of getting through my portfolio project, I didn’t want this to be my focus. 

### V and Cs
Once I had set up my models and relationships, building out the views, controllers and forms was a breeze. This was an area that I felt very knowledgeable of from continued practice in the Rails section. When I ran into problems or needed to think of a clever way to build out a form or get something through a controller, the Rails Guides would almost always provide an answer. 

### Scope Methods
One of Flatiron’s requirements for your Rails application is to include a class level ActiveRecord scope method. If you were like me with the freaking sailboats lab, you probably had a bit of PTSD reading that requirement. 

Thinking of a scope method, though, is much harder when you’re just given an arbitrary goal to meet. When it’s your project and you can come up with the scope method, it comes a lot easier than you think.

For my scope method, I looked at various attributes on my models and thought about what a user might want to see differently about that attribute.

I thought it might useful to have some sort of method to show users when they are behind on a show (based on their season versus the show’s season). 

Using that thought, I created a scope method on Show that I tested several iterations of in the rails console before getting this one to work. 

`scope :behind_on, -> (user_id) { joins(:listings).where('shows.current_season > listings.user_season').where(listings: {user_id: user_id}).uniq }`

Using the scope syntax that is asked for, I am passing in the user_id as an argument, learning this from the [good ole Rails Guides](http://edgeguides.rubyonrails.org/active_record_querying.html#passing-in-arguments).

I then need to join the listings table on the query because I need to compare an attribute from the shows table with an attribute on the listings table. 

I then filter my query by shows where the show's current_season is greater than the user listing’s season.

Then I add an additional filter so that I’m only getting shows that belong to the current user (passing in the user_id argument that I brought over). 

And lastly, because shows can be added to more than one listing, I am making sure I’m only showing the same show once on the list. 

With this method on the Show model, I created a small form_tag form which submits to `‘users/:id/shows/behind’` (a route I created in my config/routes).

```
<%= form_tag(user_shows_behind_path(current_user), method: "get") do %>
        <%= submit_tag("Shows I'm Behind On", class: "btn btn-warning btn-block") %>
<% end %>
```

![](https://dzwonsemrish7.cloudfront.net/items/311y1H0J371j3V3x1U0C/Screen%20Shot%202018-06-10%20at%2010.21.24%20PM.png)

When this button is clicked, the app is routed to the ShowsController, to the #behind action. In the #behind action, I am calling the `behind_on` scope method, passing in the user_id that I’m getting from my nested resource route, then rendering a view page to show all the shows. 

```
def behind
    @shows = Show.behind_on(params[:user_id])
    render :index
end
```

### OmniAuth
For this project, I decided to give the Google OmniAuth a try, having already done Facebook during the OmniAuth lab. 

This was a fun process and there was a lot of helpful documentation. 

I’ve decided to write up my process for setting up Google OmniAuth in a separate blog post so that others who might be interested in doing this in their project can see the steps that I took.

I will update this section with the link once I have written up the blog post.

### Styling
Once I had most of the functionality working and had tested the app significantly, I decided to give it some basic styling. 

I decided to go with Bootstrap since that seems to be a quick way to get some basic styling and components into my app.

I started out by adding the `bootstrap` gem to my Gemfile, as well as `jquery-rails` as suggested by a few blog articles I read about the process.

After installing my Gems, I realized that getting the stylesheets into my Rails project would be much different than with my Sinatra project. With my Sinatra project, I was downloading the stylesheets and adding them into my project along with links to the stylesheets.

With Rails, we are just importing the stylesheets directly - much simpler! The process looked like:

1.	Go to `app/assets/stylesheets/application.css` and rename it to `app/assets/stylesheets/application.scss`
2.	Open that file and add `@import "bootstrap";`
3.	Open `app/assets/javascripts/application.js`
4.	Add `//= require jquery3`, `//=require popper`, and `//=require bootstrap-sprockets` to the file, before the `//=require_tree`
5.	Save and re-boot the server – the app will now have some basic Bootstrap styling!

I took advantage of several of the Bootstrap components in my app, including:
* Navbar – for a simple navbar at the top of the page
* Buttons – for my form submit buttons and links to new pages
* Cards – for listing out watchlists and show details on a watchlist
* Form Styling – for my new and edit forms
* Table – for my table of shows
* Jumbotron – for my homepage

![](https://dzwonsemrish7.cloudfront.net/items/2X0Q1t0H1V1C1E0C3C05/Screen%20Shot%202018-06-10%20at%2010.27.16%20PM.png)

I also decided to abstract a lot of the style into shared folders and partials. For example, my navbar is being called in `views/layouts/application.html.erb`, but it is rendering the navbar partial in `views/shared/_navbar.html.erb`. 

Additionally, I have row and column formatting for each of my pages to separate things into a header, left-hand navigation menu, content area, and right-hand sidebar. This formatting lives inside the `application.html.erb` layout and yields to different areas (:header, :vertical_nav, :content, :sidebar) based on which column-sizing div it’s in.

The yields look for the `<% content_for :header do %><% end %>`, `<% content_for :sidebar do %><% end %>`, etc. inside each of my view pages.

This way, you can easily see which section the code for the view lives. 

```
#/app/views/layouts/application.html.erb
<!DOCTYPE html>
<html>
  <head>
    <title>Bingeworthy</title>
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>

  <body>
    <%= render partial: "shared/navbar" %>
    <%= render partial: "shared/alerts" %>
    <div class="m-3">
      <%= yield :header %>
      <div class="row mt-4">
        <div class="col-2">
          <%= yield :vertical_nav %>
        </div>
        <div class="col-8">
          <%= yield :content %>
        </div>
        <div class="col-2">
          <%= yield :sidebar %>
        </div>
        <%= yield %>
      </div>
    </div>
  </body>
</html>
```

### Conclusion
This was an interesting experience building the application. I found myself frustrated getting started, a bit bored in the middle when I was doing the work I had done repeatedly throughout the Rails assignments, and interested while struggling through some of the newer aspects, like ActiveRecord methods and adding Bootstrap or Rspec. 

I think there are a lot of things I wanted to do and add to this app, but at some point, you should deploy a MVP and come back on a regular basis to add new features, fix bugs and clean-up any technical debt incurred. It will be something I come back to as I learn JavaScript and ways to make the front-end more visually appealing to users. 
