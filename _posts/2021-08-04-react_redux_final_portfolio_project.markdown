---
layout: post
title:      "React/Redux Final Portfolio Project"
date:       2021-08-04 04:23:04 +0000
permalink:  react_redux_final_portfolio_project
---


I've always had a love for small merchants - those hole-in-the-wall gems that you stumble upon. The neighborhood diner that only you and your high school friends know about. The place that only locals know abot. Typically, whenever they have some merchandise, I make it a point to buy a shirt or hat as a way to not only support the business but also have something physical in my possession to look back on. This app, "Small Town Apparel Co.", is a collection of my favorite merchants and items that I've collected. In future iterations, it can be used as a larger ecommerce site to help merchants sell items via an online channel outside of their brick and mortar store or existing ecommerce supply chains.

One thing that I learned during the React module is the definition of "state" - the heart of every React component. 'State' is a JS object that contains data relevant to a component. It's a special attribute of the instance of the component and we can access it within the component by running `this.state`. 

**Rules of state:**
* Only usable with class components
* Do not confuse props with state
* Updating 'state' on a component causes the component to (almost) instantly rerender
* State must be initialized when a component is created

In my application, if a user is on the homepage, and then clicks on the Katz's Delicatessen merchant card, they will navigate to the URL http://localhost:3001/merchant/2. The current state of the app is this object that has the name, location, description, and the two Items that currently map to Katz's Deli. If a user navigates to http://localhost:3001/merchant/1 (Sherwood Diner), there will be a different set of states. We want to be able to go to our API and update the state of the object to then render onto the page correctly. Redux is a tool that allows us to do this. It is a state management tool which helps organize the state of the application.

A method I am using throughout the application, is `ComponentDidMount`. This is a lifecycle method and basically the initialization of the component. It's a place where React will tell us that the component did successfully mount. From there, we call `fetchMerchants`, which uses fetch to make an API call to our database. We then take the response from the API, parse it as JSON, and use the reducer to update the state of the application. This means that we got the merchants back from the API, but we need to grab it and put it on the page.

```
class App extends React.Component {
  componentDidMount() {
    this.props.fetchMerchants();
  }
```

Another instance of where we are using this method is in the addItemPage and addMerchantPage components. It is the first method to fire when the component is loaded.

```
class AddItemPage extends React.Component {

  state = {
    name: '',
    description: '',
    price: 0, 
    category: '',
    merchant_id: '',
    image_url: ''
  }
  componentDidMount() {
    console.log("props", this.props)
  }
```
  
Then, when the component is loaded, we are telling our application to create an empty item to fill-in.

We also make use of the `setState` method, which is used any time where we want to make a change to state as it triggers a re-render. For example, take at some of the implementations in the addItemPage component:

```
  handleNameChange = (event) => {
    this.setState({name: event.target.value});
  }

  handleDescriptionChange = (event) => {
    this.setState({description: event.target.value});
  }

  handlePriceChange = (event) => {
    this.setState({price: event.target.value});
```

When the name changes in our form, for example, it will call `this.setState`, and it will set the state of the name inside of our state object to the value that the user typed in. 

Once we submit the state to our API, we are going to create a new item:

```
handleSubmitButton = () => {
    this.props.addItem(this.state, this.props.history);
  }
```
  
In the above code, `addItem` is our action which we get from our props. If you open up addItem.js, this is what posts to the database and send the fields from our form to our API.

In our itemList component we're using mapStateToProps to bring the state into our component and add it to our props. Our component get props passed into it as an argument, so those props now have items on them. We can do props.item.map in order to loop through those and render each one of them to the page.

```
const ItemList = (props) => {
  return (
    <div className="row row-cols-1 row-cols-sm-2 row-cols-md-3 g-3" style={{margin: '10px'}}>
      {props.items.map((item) => {
        return <ItemCard item={item}></ItemCard>
      })}
    </div>
  )
}
```

```
const mapStateToProps = state => {
  return {
    items: state.items
  }
}
```

This is just some of the most important functionality of my app that I wanted to highlight. All in all these was a really fun and challenging project, which I am excited to continue building on. I learned a ton about React during this module and am looking forward to learning more!



