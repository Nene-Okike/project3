## STEP 1 – BACKEND CONFIGURATION

sudo apt update 
sudo apt upgrade
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
Verify node and npm installation with the following commands
              
              $ node -v 
              $ npm -v 

Create a new directory for your To-Do project and cd into the directory
$ mkdir To-Do

Use this command to initialise your project

     $ npm init
     
    ![project](projectinit.png) 

Run the command ls to confirm that you have package.json file created.
![package](package.json.png)


## Install ExpressJS

use npm to install express

       $  npm install express

create a file index.js

       $  touch index.js

Install the dotenv module with this command

       $  npm install dotenv

vim into index.js directory and paste these command inside, save and quit

const express = require('express');
require('dotenv').config();
const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use((req, res, next) => {
res.send('Welcome to Express');
});

      $  vim index.js

const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})
app.listen(port, () => {
console.log(`Server running on port ${port}`)
});

use this command to see if the server works

     $  node index.js


output ![webserver](webserver.png)

Open TCP port 5000 on the remote server and access the server from the browser using this command

       $  http://<PublicIP-or-PublicDNS>:5000

Server ![browser](browseroutput.png)

To enable the To-Do app execute the required tasks of POST, GET and DELETE we need to create, we need to create routes that will define the various endpoints that  the app will depend on.
Create a directory cd routes and make a file api.js and vim into it and paste the following commands and save and quit.

const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})
module.exports = router;

##  PROJECT 3: MERN STACK IMPLEMENTATION

To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier.
cd into Todo directory and run the command

        $ npm install mongoose

Make an new directory models and cd into it and make a file todo.js

  $  mkdir models && cd models && touch todo.js

  vim todo.js and paste the following commands and save and quit

  const mongoose = require('mongoose');
const Schema = mongoose.Schema;

//create schema for todo
const TodoSchema = new Schema({
action: {
type: String,
required: [true, 'The todo text field is required']
}
})

//create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;


Now we need to update our routes from the file api.js in ‘routes’ directory to make use of the new model.

In Routes directory, open api.js with vim api.js, delete the code inside with :%d command and paste the code below into it then save and exit

const express = require ('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res, next) => {

//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});
router.post('/todos', (req, res, next) => {
if(req.body.action){
Todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
}
});

router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
})

module.exports = router;


###  MongoDB Database

Using MLab I signed up for a shared database MongoDB account. I chose aws as my cloud provider.
In the index.js file, we specified process.env to access environment variables, now we create the .env in the Todo directory

touch .env && vi .env

Add the connection string to access the database in it as below:

DB = mongodb+srv://Nelly:oxf9l9C7JG4FzJQk@cluster0.kcuhxox.mongodb.net/favourDB?retryWrites=true&w=majority

Save and quit

Update the index.js to reflect the use of .env so that Node.js can connect to the database.

To do this, vim into index.js file and delete the content of the file
            $   vim index.js
            $   :%d

Copy and paste the entire code below in the file.

const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

//connect to the database
mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
.then(() => console.log(`Database connected successfully`))
.catch(err => console.log(err));

//since mongoose promise is depreciated, we overide it with node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use(bodyParser.json());

app.use('/api', routes);

app.use((err, req, res, next) => {
console.log(err);
next();
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});

Save and quit

Start your server using the command:

node index.js
![databaseconnected](databaseconnected.png)


Now we will do Backend Code testing without Frontend using RESTful API. In this application we will use Postman to test our API.

Download and install Postman by clicking on this link
[Install Postman](https://www.postman.com/downloads/)

Open your Postman and create a POST request to the API http://<PublicIP-or-PublicDNS>:5000/api/todos. 

This is meant to send a new task to our To-Do list so the application could store it in the database.

Make sure your set header key Content-Type as application/json
and modify the tge raw subheading under Body with this {"action" : "Projecr3"}

The output should show 200
 ![Postman](Postman.png)

 Step 2 – Frontend creation

 At this stage, we create a user interface for a Web client (browser) to interact with the application via API.

 First we will make use of the create-react-app command. so run

     $  npx create-react-app client
This command will create a new folder in our Todo directory called client, where all the react code will be added.

Use the following commands to create these two dependencies before testing the react react app

    $  npm install concurrently --save-dev
    $  npm install nodemon --save-dev


Open the Todo folder vim into the package.json file. Replace the "script" portion of the code qith the following code:-

"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},

save and quit

Open the client file and vim into package.json

Configure Proxy in package.json

Add this keyvalue pair

 "proxy": "http://localhost:5000".

 Save abd quit


 This will make it possible to access the application directly from the browser by simply calling the server url like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos

In the Todo directory, run the command below for the app to open and run

npm run dev

![apprun](apprunning.png)

TCP port 3000 should be open when you run the command in the aws server

From the Todo directory, cd clients and then cd src. 

Inside the src folder

   $  mkdir components &&cd components

  Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js with this command

touch Input.js ListTodo.js Todo.js 

Open Input.js file with the vim command

Copy and paste the following command

import React, { Component } from 'react';
import axios from 'axios';

class Input extends Component {

state = {
action: ""
}

addTodo = () => {
const task = {action: this.state.action}

    if(task.action && task.action.length > 0){
      axios.post('/api/todos', task)
        .then(res => {
          if(res.data){
            this.props.getTodos();
            this.setState({action: ""})
          }
        })
        .catch(err => console.log(err))
    }else {
      console.log('input field required')
    }

}

handleChange = (e) => {
this.setState({
action: e.target.value
})
}

render() {
let { action } = this.state;
return (
<div>
<input type="text" onChange={this.handleChange} value={action} />
<button onClick={this.addTodo}>add todo</button>
</div>
)
}
}

export default Input

Save and quit

We need to make use of Axios, which is a Promise based HTTP client for the browser and node.js To install axios we cd into the client directory  with cd ..//..
and run 

yarn add axios or npm install axios.

Then install axios with

  $  npm install axios

  Open the components directory  and vi inti the ListTodo.js

      $  cd src/components
      $  vi ListTodo.js
  Paste the following commands

  import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {

return (
<ul>
{
todos &&
todos.length > 0 ?
(
todos.map(todo => {
return (
<li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
)
})
)
:
(
<li>No todo(s) left</li>
)
}
</ul>
)
}

export default ListTodo

Save and quit

vim into Todo.js and copy and paste the following commands

import React, {Component} from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {

state = {
todos: []
}

componentDidMount(){
this.getTodos();
}

getTodos = () => {
axios.get('/api/todos')
.then(res => {
if(res.data){
this.setState({
todos: res.data
})
}
})
.catch(err => console.log(err))
}

deleteTodo = (id) => {

    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))

}

render() {
let { todos } = this.state;

    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )

}
}

export default Todo;

Save and quit

We need to make little adjustment to our react code. cd src folder

       $    cd ..
       $   vi App.js

Copy and paste the following

import React from 'react';

import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;

Save and quit

In the src directory open the App.css

    $  vi App.css
Copy and paste

.App {
text-align: center;
font-size: calc(10px + 2vmin);
width: 60%;
margin-left: auto;
margin-right: auto;
}

input {
height: 40px;
width: 50%;
border: none;
border-bottom: 2px #101113 solid;
background: none;
font-size: 1.5rem;
color: #787a80;
}

input:focus {
outline: none;
}

button {
width: 25%;
height: 45px;
border: none;
margin-left: 10px;
font-size: 25px;
background: #101113;
border-radius: 5px;
color: #787a80;
cursor: pointer;
}

button:focus {
outline: none;
}

ul {
list-style: none;
text-align: left;
padding: 15px;
background: #171a1f;
border-radius: 5px;
}

li {
padding: 15px;
font-size: 1.5rem;
margin-bottom: 15px;
background: #282c34;
border-radius: 5px;
overflow-wrap: break-word;
cursor: pointer;
}

@media only screen and (min-width: 300px) {
.App {
width: 80%;
}

input {
width: 100%
}

button {
width: 100%;
margin-top: 15px;
margin-left: 0;
}
}

@media only screen and (min-width: 640px) {
.App {
width: 60%;
}

input {
width: 50%;
}

button {
width: 30%;
margin-left: 10px;
margin-top: 0;
}
}

Save and quit

In the src directory open the index.css using 
  $vim index.css, copy and paste the following code

'body {
margin: 0;
padding: 0;
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
box-sizing: border-box;
background-color: #282c34;
color: #787a80;
}

code {
font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
monospace;
}'

Saave and quit

Go to the Todo directory

cd ..//..

and run

    $   npm run dev

This is the output
![Todoapp](Todoapp.png)






















        