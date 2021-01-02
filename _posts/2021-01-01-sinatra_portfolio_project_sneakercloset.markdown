---
layout: post
title:      "Sinatra Portfolio Project: SneakerCloset"
date:       2021-01-01 15:14:10 -0500
permalink:  sinatra_portfolio_project_sneakercloset
---

![](https://lapoesiedelobjet.tumblr.com/image/71852590917)

Being that Frank Sinatra is legitimately one of my favorite musicians of all time, I was excited to dive head first into our next module. Over the past few months I've been learning more about the underlying connections between Users and databases, how to create forms for data entry, how to manipulate that data, and how browsers and servers talk to each other. 

Two major components of both the curriculum and this project are ActiveRecord and Sinatra. In the previous CLI project, we didn't focus too much on how the API was queried, but rather how you could connect to it and access data. This project was more involved in developing code to create database tables, write requests to query them, develop forms, and create a user-facing web application will full C.R.U.D functionality.

My Sinatra Portfolio project is for all the sneaker lovers out there. Personally, I'm not a *huge* sneaker collector myself, but I definitely have a few pairs for different occasions (a pair for running, strength training, doing ordinary errands, a fancy pair, etc). And I'm not alone. According to a [worldshoeliquidator article](http://worldshoeliquidator.com/shoe-news-how-many-pairs-does-average-person-own), the average man owns 12 pairs of shoes. With the SneakerCloset app, everyone will now have a dedicated digital space for their collection.

One of the main requirements for this project was to develop an application that contains full C.R.U.D functionality for at least one resource. This project has two resources: Users and Sneakers. Having C.R.U.D functionality is important because it will enable a User to create a new sneaker, update/edit the sneaker attributes, read (or view) their collection, and delete their entry should they choose to. This is done through various actions that we have built in our SneakerController file (see snippet below).

```
    patch '/sneakers/:id/edit' do
        if logged_in?
            @sneaker = Sneaker.find_by(id: params[:id])
            if current_user.id == @sneaker.user_id
                @sneaker.brand = params[:brand]
                @sneaker.model = params[:model]
                @sneaker.sport = params[:sport]
                @sneaker.price = params[:price]
                @sneaker.size = params[:size]
                @sneaker.save
                redirect to "/sneakers/#{@sneaker.id}"
            else
                erb :"/users/unauthorized_failure"
            end
        else
            erb :"/users/unauthorized_failure"
        end
    end

    delete '/sneakers/:id/delete' do
        if logged_in?
            @sneaker = Sneaker.find_by(id: params[:id])
            if  current_user.id == @sneaker.user_id
                @sneaker.destroy
                redirect to '/sneakers'
            else
                erb :"/users/unauthorized_failure"
            end
        else
            erb :"/users/unauthorized_failure"
        end
    end
end
```

While there is more to the sneaker controller not all code posted above, see GitHub repo for more) the above represents two very imporant actions: Edit and Delete. Both actions are using similar logic to give the end user the ability to edit and delete a sneaker, but only if they are a) logged in and b) the id of the current user matches the user_id of the sneaker. The helper methods 'logged_in' and 'current_user' are working hand in hand to know whether someone is logged in and who that user is.

```
  helpers do 
    
    def logged_in?
      !!current_user
    end
    
    def current_user
      @current_user ||= User.find_by(:id => session[:user_id]) if session[:user_id]
    end

  end
end
```

The presence of a session user_id is how we know whether someone is logged in. But we don't want to return the ID, we want to return true or false. In order to return true or false we use th double bang operator. One bang returns the opposite boolean value, two returns the boolean. 

Knowing who is logged in is where our current_user method comes into play. We use the find_by method in order to search for the user id of the session and save it to the instance variable @current_user. The or equals operator gives us the ability to check to see if the value on the left is defined. If it's not, then we use the value on the right.

Throughout the SneakerController class (which is inherited from ApplicationController), I've leveraged the 'logged_in' and 'current_user' methods in order to set conditionals for logged_in users and what the current user does/does not have access to. Additionally, it was intentional that only users who are logged_in can see More Details of a particular sneaker beyond the Brand and Model in the All Sneakers view (allsneakers.erb). The idea behind this is to incentivize users to sign up or log in (hence the redirect should the user not be logged in).

I'm proud about how the project has come together, in particular how I've built error views (users to prevent users who are unauthorized to edit and delete data. I also wanted to make sure that no matter what page a user ended up on, they had a link that they could click on to get back to the homepage, or the previous page, and navigate the site with ease. 

While the app is quite basic in comparison to your average every day website, I'm continuing to better understand how the internet works and think that this is a great first step towards becoming a legitimate web developer. In terms of improvements, the app could definitely benefit from the inclusion of pictures and redirects to external sites where users can purchase the sneakers. Additonally, a social element where users can chat about their sneaker collection can help to create a feedback loop where users are logging in more frequently also pave the way for a more viable business should we want to start think about monetization.

