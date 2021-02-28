---
layout: post
title:      "I Can See Clearly Now - Rails Portfolio Project"
date:       2021-02-28 19:52:19 +0000
permalink:  i_can_see_clearly_now_-_rails_portfolio_project
---

This Rails Project was inspired by my girlfriend’s dad’s Optical store. The business is approaching it’s 30th anniversary and was one of the first stores to carry the Oliver Peoples line during the 80’s in the state of Connecticut. The story is akin to that of Nike founder Phil Knight, who was one of the only original dealers of Tiger Onitsuka shoes in the United States prior to creating Nike. While on a slightly smaller scale, Eddy was relentless in getting a contract with Oliver Peoples during its heyday when the brand cherry picked their retail partners. This exclusive contract with Oliver Peoples helped bring in a lot of customers and remains a successful storefront to this day. However, one of the things that baffles me is the thousands of physical customer cards tucked away in his basement. Each card contains patient and prescription information, and while they do have a website, he admittedly could make more use of tech.

And so the ClearView Relationship Management portal was born. This project contains 3 Models: an Optician (User), Optometrist, and Patient. Both Optician and Optometrist `has_many` Patients, making Patient the join table. 

One of the requirements which was new to this project was implementing Login with Google functionality. This [blog post](https://medium.com/@jenn.leigh.hansen/google-oauth2-for-rails-ba1bcfd1b863) provided a great breakdown of how to build out the Omniauth method in order for a user to log in with their google account. 

Another component of Rails that I enjoyed learning more about was nested routes. While in Sinatra the routes are built in the individual controller files, Rails has a separate routes file in the config folder where the routes live. Given that an Optician is logged in and authenticated, they will be redirected to their Patients Index page (all of the Patients that belong to the specific Optician). 

```
   def index
        if params[:optician_id] && @optician = Optician.find_by_id(params[:optician_id]) 
            if @optician.id == current_user.id
                @patients = @optician.patients
            else
                flash[:message] = "You are not authorized to view another optician's patient list."
                redirect_to root_path  
            end
        else
            flash[:message] = "You are not authorized to view this page."
            redirect_to root_path
        end
    end
```


While the actual route for this is `/opticians/:optician_id/patients(.:format`) Rails comes with path helpers (optician_patients_path). If you navigate to rails/routes/info once your server is running, you will be presented with a comprehensive list of all of the path helpers and their corresponding routes. While using the particular syntax such as index, create, edit, update, etc were not hard requirements for Sinatra, I appreciate our cohort lead Madeline stressing the importance of these controller action names early on, given that they are critical for Rails to work.

Another piece that was challenging was getting my Patient data to persist to the data base. Despite the Optician being logged in, and using collection_select to produce a dropdown of Optometrists, I was struggling to get a Patient to create. I used a current_user helper method in order to ensure that only a current_user was able to create a patient, but also passed in the current user id granted that the patient was saved.

 ```
def create
        @patient = current_user.patients.build(patient_params)
        if @patient.save
            redirect_to optician_patients_path(current_user.id)
        else
            render :new
        end
    end
```

In conjunction with the optometrist attributes write method built in my Optician model, we were able to link a patient to both an Optician and an Optometrist and save the new patient object.

```
   def optometrist_attributes=(optometrist_attributes)
        optometrist_attributes.values.each do |optometrist_attribute|
          optometrist = Optometrist.find_or_create_by(optometrist_attribute)
          self.optometrists << optometrist
        end
    end
```

Rails is a really flexible and powerful framework that’s fun to work with once you get the hang of it. I enjoyed building out this app and working through some major challenges over these past few weeks. Thanks for reading and I look forward to receiving more feedback on how things could be improved!
