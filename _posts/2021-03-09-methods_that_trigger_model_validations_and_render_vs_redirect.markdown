---
layout: post
title:      "Methods that trigger model validations, and  render vs. redirect?"
date:       2021-03-09 16:40:44 -0500
permalink:  methods_that_trigger_model_validations_and_render_vs_redirect
---



***Which methods will trigger model validations to run?***


Validating data is one of the most important parts of building functional web apps, as it ensures that the data being persisted to the database is valid and useful. We want to avoid situations where we allow for invalid data to be persisted to our database which can cause confusion down the road. For example, in my Rails project, all users (Opticians) must provide a `:username` and an `:email`. Not only am I validating for the presence of these attributes, but I'm also validating for uniqueness, so that no two users with the same username can be created. This may seem like pretty standard stuff, but it's very important nonetheless.

```
class Optician < ApplicationRecord
    has_secure_password
    has_many :patients
    has_many :optometrists, through: :patients
    validates :username, :email, presence: true
    validates :username, :email, uniqueness: true
    validates :certificate_number, length: { in: 5..6 }, allow_nil: true, uniqueness: true
```

The best place to build validations is in the User Model (Optician) because controller-level validations may become unwieldy and difficult to test / maintain.

The following methods trigger validations, and will only save the object to the database if the object is valid:

* `create`
* `create!`
* `save`
* `save!`
* `update`
* `update!`

The difference between the bang and non-bang method versions above is that the bang version (ex: `update!`) will raise exceptions in the event where the record is invalid. The non-bang method versions (`create`, `save`, `update`) will not raise an exception. `save` and `update` will return `false`, whereas `create` will return the object.

Before saving an Active Record object, Rails will run the validation checks and in the event that the validation produces errors, Rails will not save the object.

Outside of the methods listed above, you can also run the validations on your own. If no errors were found in the object, calling `.valid?` will return the boolean value `true`. Otherwise, it will return false.
```

It's important to note that any new object saved to the database will not report errors, even if it's technically invalid.


***Why do we use render after a failed attempt to create an object?
***

Redirection and rendering are important parts to building functional web applications. When to use each of them can get complicated and it's important to understand the differences and use cases for each.

At a high-level, if you’re going to need to use the instance variable from the controller in the view, use `render.` Otherwise, use `redirect_to.`

When a user or site visitor is redirected, the application treats this as a brand new HTTP request. Since the web application is now responding to a new HTTP request, we no longer have access to instance variable data defined in the controller. Take the following `create` action outlined below for example.

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
	
In the above example, if we are able to successfully save a new patient object to the database, the next course of action will be to redirect the user to a new view. In this case, we lose access to @patient.

However, with `render`, we still hold onto the @patient instance variable data defined in the `create` action (because for whatever reason we were not able to `save` the patient object). That's why for actions like create and update, where we are instantiating or editing data, there may be situations in which we want to maintain access to the information stored in the params. Say for example a form submission does not go through, we still want to have access to the attributes that were valid on submission (versus wiping the entire form and needing to start from scratch -- a poor user experience). The instance variable holds on to the object which gives us access to our errors.

In short, `render` tells rails which view or asset to show a user, without losing access to any instance variables defined in the controller action. Redirect (or `redirect_to`) tells the browser to send a request to another URL and since the request is completely different, the view to which you redirect won't have access to any instance variables defined in the controller.

