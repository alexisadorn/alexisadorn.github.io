---
layout: post
title:      "Sinatra's Adventure Bucket List"
date:       2017-11-10 23:34:22 -0500
permalink:  sinatras_adventure_bucket_list
---


My [Adventure Bucket List](https://github.com/alexisadorn/Sinatra-Adventure-Bucket-List) was created as a portfolio project for the Flatiron School's Online Web Development program. The purpose of this app is to give users a place to collect all of the fun and amazing places they want to visit before they kick the bucket! 

![](https://github.com/alexisadorn/Sinatra-Adventure-Bucket-List/raw/master/public/img/SinatraApp.jpg)
![](https://github.com/alexisadorn/Sinatra-Adventure-Bucket-List/raw/master/public/img/BucketList.png)

Using ActiveRecord for my associations and Sinatra for my routes, this was a great exercise to test my skills learned in the Flatiron course so far. Below are some of the fun and challenging areas I faced during this project:

# Setting up my Models & AR Associations
I started my project out drawing my tables on a whiteboard and saying out loud the basic logic of my app:

* Users have {Name, Email, Password}. A User `has_many` Experiences and `has_many` Categories `through` Experiences
* There is an Experience class, which will `belong_to` a User and to a Country. An Experience also `has_many` categories. So my Experience table needs to have {Description, User ID, Country ID}.
* My Country class has {Name}, and a Country `has_many` Experiences.
* Category class also has {Name}, and `has_many` Experiences and many Users `through` Experiences
* Since Experiences can have many Categories, and Categories can have many Experiences, a join table and model was needed as well

With my logic written out, I began creating migrations to create each table. I then created each model class inherting from `ActiveRecord::Base` and used `belongs_to`, `has_many` or `has_many, through` depending on the logic outlined above. 

The `tux` gem was my friend here and helped me work out any kinks I had with connecting the associations. 

# User Accounts
An important feature of my app is the ability for users to create an account to maintain their list, log in and out of their account, and view other users' Bucket List's to get inspiration (but not make changes). I achieved this feature by adding the following to my app:

## Enabling Sessions
By adding `enable :sessions` to my ApplicationController, I was able to maintain a user's ID in the `session` hash. This helped me to do a number of things, including:
1. Verifying if the user was logged in or out by checking if `session[:user_id]` was present
2. Disallowing access to pages if a user was logged out by redirecting the page if the `session[:user_id]` wasn't present
3. Allowing a user to view, edit and delete their own experiences
4. Preventing a user from editing or deleting an experience not belonging to them by removing the edit/delete buttons and redirecting the page if the `session[:user_id]` and `experience.user_id` did not match

## Helper Methods and Filters
There were several areas in the app where a verification check was needed to see if the user was logged in or if the current user's ID matched the experience's user ID. Following the DRY method, I created helper methods in the ApplicationController that the Experiences, Countries, Users and Categories controllers could reference.

* `#is_logged_in?` checks to see if the `session[:user_id]` has been set and returns true if it has, indicating that a user is logged in
* `#current_user` finds the User object based on the ID that has been set in `session[:user_id]` and is used to compare if the user that is currently logged in matched with an experience's user.

Reading through the Sinatra Documentation introduced me to the idea of [Sinatra filters](http://www.sinatrarb.com/intro.html). Filters can be evaluated before or after your route code is executed, providing a perfect place to do checks.

```
class ExperiencesController < ApplicationController
  before '/experiences/*' do
    if !is_logged_in?
      flash[:login] = "You need to be logged in to performance that action"
      redirect to '/login'
    end
  end
```

Here you can see that I've used Sinatra to perform a universal route check to ensure that the user accessing the route is logged in. Now anytime an `/experiences/` route was accessed, my app would first execute the code in the filter block. If the user was not logged in, it would redirect the user to the login page. If the user was logged in, it would continue on with the code in the route that they requested.

By repeating this filter in each of the controllers, I secured each page and ensured that they could not be accessed or changed by users unless they are logged in. 


## Password Verification
In order to securely store and verify a user's password, I used the gem `bcrypt`. Setting up bcrypt and authenticating a user's password included:
* Adding bcrypt to my Gemfile and installing the gem
* Including the key phrase `has_secure_password` in my User model
* Authenticating the password entered in the login form using bcrypt's `#authenticate` method in the Users Controller
* If the password did not match the one stored in the database, the user is returned to the login screen and asked to try again. 
* If the password does match, the session's `[:user_id]` is set and the user is brought to their Bucket List

# CRUD Pages
Once a user is logged in, there are three areas to create or edit content. They can:
1. Add a New Experience (`/create`)
2. Edit an Experience (`/edit`)
3. Add a New Experience from Another User (`/create_from_user`). 

The difference between the first and last form is that the user can view another user's Bucket List item, and if they would like to add to their own list, they create a new Experience object from that user's object. This ensures that the user isn't editing someone else's content, but also provides a quicker way to add an idea to their list. 

All three forms request the same information - the Description, as a text field, the Country, as a text field, and the Category, as a checkbox field to choose from existing Categories, or a text field to create a new Category. 

Because there are three forms gathering the same information and posting this information in almost the same way (with the `edit` form updating an already-created Experience rather than creating one), I decided to make methods in the Experiences class that each of these forms could call in the `post` route. 

```
def self.create_new_experience(details, category_name, category_ids, session_uid)
    @details = details
    @category_name = category_name
    @category_ids = category_ids
    @user = User.find(session_uid)

    set_country

    @experience = Experience.new(
      :description => @details[:description],
      :country => @country,
    )

    set_categories
    @experience.user = @user

    @experience.save
    @experience
  end
```

and

```
def self.update_experience(details, category_name, category_ids, experience)
    @details = details
    @category_name = category_name
    @category_ids = category_ids
    @experience = experience

    set_country

    @experience.update(
      :description => @details[:description],
      :country => @country
    )

    @experience.categories.clear
    set_categories

    @experience.save
    @experience
  end
```

`#self.create_new_experience` and `#self.update_new_experience` take in the details from the form that are passed in through the `params` hash. 

Each method goes through the process of setting the country attribute by creating a new Country object (or finding a Country object that already exists).

```
def self.set_country
    @country = Country.find_by(:name => @details[:country]).presence || Country.create(:name => @details[:country])
  end
```

Then either a new Experience is created or the current Experience is updated with the description and the country. 

Each method then sets categories (with the `update` method clearing the current categories first) by checking for the selected categories first and setting those based on the Category objects it finds in the database. Then it checks if anything has been entered in a new Category, and if so, creates a new Category based on the name. The categories are added to the Experience. 

The `create` method will also set the user_id based on the `session[:user_id]` that was passed. 

Once all attributes have been set, the new/updated instance of the Experience is passed back to the controller to be displayed by the view. 

# Styling and Notifications
The styling of the website was done simply with Twitter's Bootstrap. A great tutorial here walked me through [How to integrate Bootstrap CSS Into Your Sinatra Site](http://ningbit.github.io/blog/2013/06/28/how-to-integrate-bootstrap-css-into-your-sinatra-site/). 

I also installed Sinatra's `sinatra-flash` gem in order to create useful flash messages when a user accesses a page they should not have access to or creates/deletes an experience. 

Using my `layout.erb` page, I was able to set my flash messages in an iterator which told my app to display each of the flash messages when they were triggered. This cut a lot of my code down by removing multiple `if end` statements. By adding it to my layout page above the `<%= yield %>` I was also able to cut down on the lines of code in my view files. 

```
<% flash.keys.each do |type| %>
        <div class="text-center alert alert-warning">
          <%= flash[type] %>
        </div>
      <% end %>
```


# Wrapping Up
I had a lot fun creating this project and am exicted to use it for my own personal Adventure Bucket List. I can already see some areas for new features, including: being able to add more items to an Experience (like an image, link to a travel blog, etc.), being able to check off when things are completed on the Bucket List, and pulling in more travel suggestions from travel sites. But overall I'm pleased with the project at this point and thinks this will be a very useful app!

