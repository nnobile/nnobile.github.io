---
layout: post
title:      "Execution context, *this* keyword, & arrow (=>) vs. regular functions"
date:       2021-05-21 12:10:47 -0400
permalink:  execution_context_this_arrow_vs_regular_functions
---


**Execution Context**

In order to talk about the concept of execution context, I am going to reference my `index.js` document that is part of my JavaScript portfolio project as an example (see below):


```document.addEventListener('DOMContentLoaded', () => {
    CategoryApi.getCategories()
    HeadphoneApi.getHeadphones()

    const createHeadphoneForm = document.querySelector("#create-headphone-form")
    const filterDropdown = document.querySelector("#filtered-category")
    const filterButton = document.querySelector("#create-sort-button")
    filterDropdown.addEventListener("change", HeadphoneApi.getHeadphones)

    createHeadphoneForm.addEventListener("submit", createFormHandler)
    filterButton.addEventListener("click", HeadphoneApi.filterHeadphones)
})

function createFormHandler(event) {
    event.preventDefault()
    const brandInput = document.querySelector('#input-brand').value
    const modelInput = document.querySelector('#input-model').value
    const descriptionInput = document.querySelector('#input-description').value
    const priceInput = document.querySelector('#input-price').value
    const categoryId = parseInt(document.querySelector('#categories').value)
    
    HeadphoneApi.postFetch(brandInput, modelInput, descriptionInput, priceInput, categoryId)
    document.querySelector('#input-brand').value = "";
    document.querySelector('#input-model').value = "";
    document.querySelector('#input-description').value = "";
    document.querySelector('#input-price').value = "";
}```

The above will get loaded into the HTML page. The moment that this gets opened, an execution context opens for the entire document.

The first pass of the interpreter is going to look through the code and recognize that there is a `function` called `createFormHandler`. 

In the second pass, the interpreter will recognize `document.addEventListener('DOMContentLoaded'`). Nothing else happens until the `DOMContentLoaded` event fires, at which point the anonymous arrow function on line 1 is called (we will get more into arrow functions later).

Within these curly braces following the arrow function, a whole new execution context is opened. Keep in mind, we are still within the original document execution context too! So there is an execution context, within an execution context.

Now, this "second" execution context has it's own two passes as well. The first thing the interpreter will recognize is that there is a `const createHeadphoneForm` variable. During the first pass, JavaScript will set the `createHeadphoneForm` variable to `undefined`. Then, during the second pass, the interpreter will go back to the top of the execution context and execute the code. So it will execute line 2, line 3, and then on line 5 `createHeadphoneForm` gets converted from `undefined` to an `element`. Then, line 7 is executed (which is also an event listener).

In general, once a function is done, it's execution context is packed up and the memory space that it was given is reallocated efficiently.


**Value of `this`**

What is `this` in JavaScript? It is the object that is executing the current function.

So without any context - `this` refers to the global object, which in a browser in the window.

What about in a standard function? `this` again refers to the global object window, since the function is being executed from the global object.

Outside of the function, `this` still refers to the global window object.

An easy way to remember what `this` refers to, is to look at the line where the function is called or method is called. See example below.

```
const user = {
	 firstName: "John",
	 lastName: "Doe",
	 fullname() {
		 console.log(this);
		 console.log(`${this.firstName} ${this.lastName}`);
		}
};

user.fullName();
```

In the above, we have a user object with a method of `fullName`. Within the object, `this` now refers to the object in which it is called. 

In our first `console.log`, we are getting the entire object. In the second log, we are getting the first and last name using our template string. Here's another way to write the same method as the above:

```
const user = {
firstName: "John",
lastName: "Doe",
fullname: function() {
	console.log(this);
	console.log(`${this.firstName} ${this.lastName}`);
	}
	};
```

Here, we are defining the `function`, but what if we use an arrow function (=>) instead? 

```
const user = {
firstName: "John",
lastName: "Doe",
fullname: () **=>** {
	console.log(this);
	console.log(`${this.firstName} ${this.lastName}`);
	}
	};
```

It's important to understand how arrow functions (=>) work, along with when to use them and when not to use them.

In this instance, we will ***not*** want to use an arrow function because arrow functions always bind to the global object.

This time, in our first console log, we get the window object. In our second console log we get `undefined`. 

Now, let's add an array of pets within the user object and then a new method to list each pet.

```
const user = {
firstName: "John",
lastName: "Doe",
pets: ["cat,", "dog", "fish"]
listPets: () {
	this.pets.forEach(function(pet){
	console.log(`${this.firstName}'s ${pet}`
```

In the above execution context, `this` will refer to the user object.

Let's look at another example and in this case we'll look at my constructor method.

```
class Headphone {
    static all = []
    constructor (headphone, headphoneAttributes){
       this.id = headphone.id
       this.brand = headphoneAttributes.brand
       this.model = headphoneAttributes.model
       this.description = headphoneAttributes.description
       this.price = headphoneAttributes.price
       this.category = headphoneAttributes.category
       Headphone.all.push(this)
    }
```
	
In the constructor, `this` does not have a value. The value of `this` will become the new object **once it has been created**.


**More on Arrow functions vs. Regular functions**

A `function` in JavaScript is a grouping of reusable code. If you use the `function` keyword, you can give the function a name. For example, 

```
function createFormHandler(event) {
    event.preventDefault()
    const brandInput = document.querySelector('#input-brand').value
    const modelInput = document.querySelector('#input-model').value
    const descriptionInput = document.querySelector('#input-description').value
    const priceInput = document.querySelector('#input-price').value
    const categoryId = parseInt(document.querySelector('#categories').value)
    
    HeadphoneApi.postFetch(brandInput, modelInput, descriptionInput, priceInput, categoryId)
    document.querySelector('#input-brand').value = "";
    document.querySelector('#input-model').value = "";
    document.querySelector('#input-description').value = "";
    document.querySelector('#input-price').value = "";
}
```

Arrow functions, HAVE to be anonymous (in other words, they have no name). 

They are useful for instances in which you want to create a function within a function, but not overwrite "`this`".

A potential drawback of using too many arrow functions is that even if they are assigned to named variables, if you come across an error and need to do a stack trace, the error message you will receive will be akin to "there is an error in anonymous function, called by anonymous fucntion, within anonymous function", which can lead to confusion. That's why it's important to understand the different use cases for arrow functions and when it's best to use named functions vs. when to use arrow functions.
