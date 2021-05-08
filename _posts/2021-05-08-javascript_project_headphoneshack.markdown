---
layout: post
title:      "JavaScript Project: HeadphoneShack"
date:       2021-05-08 17:13:08 +0000
permalink:  javascript_project_headphoneshack
---


My JS + Rails API project is a headphone review site, that allows users to create and compare different types of headphones. The object model relationship is a category `has_many` headphones, and a headphone` belongs_to` a category. Headphones have a few different attributes -` :brand`, `:model`, `:description`, `:price`, and `:category`.

I decided to separate the front-end from the back-end in order to allow for both to be used either together, or separately.

In order to allow for what is called 'cross-origin resource sharing' - I included the gem ‘`rack-cors`’. CORS provides browsers a way to request remote URLs only when they have permission, and allows servers to reject requests from unknown origins. For this project, the config/initializers/cors.rb is configured to allow requests from all origins and to allow [:get, :post, :patch, :delete] requests to the API.

Additionally, in order to serialize the data and select specific information to be displayed, I used the gem '`active_model_serializers`'. 

Since this is the first version of the API, the controller lives in api/v1. All headphones are rendered in JSON format. The `headphone_params` permit the aforementioned parameters :brand, :model, :description, and :price.

One of the requirements for this project was that ***the application should have at least 3 AJAX calls, covering at least 2 of Create, Read, Update, and Delete (CRUD). The client-side JavaScript code must use `fetch` with the appropriate HTTP verb, and your Rails API should use RESTful conventions.***

We are solving for this within our category_api.js, headphjone_api.js, and index.js files where we are making three unique API calls in order to retrieve all of the categories, headphones, and post a new headphone to the database. The two different CRUD functions that I am using are Create and Read. We are Creating a new headphone, and Reading all of the Headphones, and all of the categories.

One element of the project that I'm particularly proud of is organizing the headphone cards in the order that they were created. The newest Headphone instance is prepended to the beginning of the container. So rather than scrolling all the way to the bottom of the page to see the newest headphone, it now appears at the very top, which makes for a better user experience.

```
class HeadphoneApi{
    static endPoint = "http://127.0.0.1:3000/api/v1/headphones"
    static getHeadphones(){
        fetch(HeadphoneApi.endPoint)
        .then(res => res.json())
        .then(headphones => {
          headphones.data.forEach(headphone => {
              let newHeadphone = new Headphone(headphone, headphone.attributes)
              const headphoneCard = document.createElement('div');
              headphoneCard.innerHTML= newHeadphone.renderHeadphoneCard()
            **  document.querySelector('#headphone-container').prepend(headphoneCard)**
        })
      })
    }

}
```


