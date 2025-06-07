# All About the Model-View-Controller
A short rambling about the Model-View-Controller(MVC).

## What it is:

NOTE: If you are reading this, it's assumed you have an understanding of Node, Express, EJS and Mongoose.

The MVC allows an engineer to encapsulate different aspects of their project. You could always just throw all of your code into one file or a single folder, then call it a day. However, that can lead to many issues.

Think of it like this. It would really suck if your apartment flooded. Everything's wet. The roomba's ruined. Your cat's mad. Terrible. However, life goes on as normal for every other resident.

Think of the building like your project. If everyone lived in one big room, that flood leads to frustration for everyone. 
That's why using the MVC can be so beneficial. The MVC allows you to structure your project into different sections to avoid issues that wreck the whole project. Let's take a deep dive into what the MVC has to offer.

![Alt text](images/mvc.png)

In the image above, we can see the different parts of the MVC. We'll  take a look at each of them now.

## Controller

The controller is typically one of the first parts of the MVC that the browser and user will interact with. A user would send a request to the server, then the router would evaluate the request and pass it to the controller. Then, the controller would decide what happens with that request. As you can see, the controller could render a view and send it back to the browser. It could be a simple GET request which, when sent to the controller, will render the page associated with that route. Here's an example:

Our Router:
```javascript
const express = require('express')
const router = express.Router()
const todosController = require('../controllers/todos')

router.get('/', todosController.getTodos)

// ...
```
Our Controller:
```javascript
// Specifies a Model to use
const Todo = require('../models/Todo')

module.exports = {
    getTodos: async (req,res)=>{
        try{
            // Finds all documents in the Todo model
            const todoItems = await Todo.find()
            // Returns the number of documents
            const itemsLeft = await Todo.countDocuments({completed: false})
            // Renders todos.ejs and inserts the todoItems and itemsLeft variables
            res.render('todos.ejs', {todos: todoItems, left: itemsLeft})
        }catch(err){
            console.log(err)
        }
    },

    // ...
}
```
To understand what's going on here, we must first understand Mongoose. Mongoose is a library that simplifies your interactions with MongoDB in your Node applications. Pretty nifty.

Mongoose allows you to create Schemas. A Mongoose Schema basically tells you what can be inserted into a database. It will act as our Model.

## Model 

Below, you can see an example of our Model in action.
```javascript
// Import mongoose
const mongoose = require('mongoose')

// Create "todo" and "completed" objects. We also define what type of information is stored inside of it and specify whether or not it's required.
const TodoSchema = new mongoose.Schema({
  todo: {
    type: String,
    required: true,
  },
  completed: {
    type: Boolean,
    required: true,
  }
})

// Export the moddel for external use.
module.exports = mongoose.model('Todo', TodoSchema)
```
In the Mongoose Schema, we define two objects. "todo" and "completed". Basically, the schema is what tells the developer what is allowed to be stored in the database. This can be beneficial not only for quality control, but also in situations where the developer might not have access to the database. They can see the typical format of the data they're working with and not worry about causing any disruptions. Our Model is what's being imported into the Controller.

That really just leaves our Views.

## View
Our View, as you may guess from the name, is what the user might see. This is usually in the form of HTML and would be rendered by the Controller.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="css/style.css">
</head>
<body>
    <h1>Todos</h1>
    <ul>
    <% todos.forEach( el => { %>
        <li class='todoItem' data-id='<%=el._id%>'>
            <span class='<%= el.completed === true ? 'completed' : 'not'%>'><%= el.todo %></span>
            <span class='del'> Delete </span>
        </li>
    <% }) %>    
    </ul>

    <h2>Things left to do: <%= left %></h2>

    <form action="/todos/createTodo" method='POST'>
        <input type="text" placeholder="Enter Todo Item" name='todoItem'>
        <input type="submit">
    </form>

    <script src="js/main.js"></script>
</body>
</html>
```
Here's our EJS file. EJS is simply a templating language that allows you to render different states of the page based on the request.

To recap, the user (via the browser), will send a request to the server. The router will catch the request and pass it off to the controller. Then, the controller will look for the model to use and, with the instructions provided in the code, render the page.

This is a very rudimentary explanation of the model view controller and there are a trillion other resources that go into it with much greater depth. I took this as an opportunity to practice writing and breaking the concept down. I believe that if you can explain it to someone else, that's a sign that you understand the concept better yourself. Thank you for reading!
