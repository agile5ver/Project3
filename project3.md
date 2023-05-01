# Project-3

**STEP 1 – BACKEND CONFIGURATION**

`sudo apt update` --update ubuntu

`sudo apt upgrade`--upgrade ubuntu

`curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -` --To get the location of Node.js on the server

`sudo apt-get install -y nodejs` --To install Node.js and npm

node -v && npm -v

![Node&npm version](./Images/node%26npm_version.png)

`mkdir Todo`

`cd Todo`

`npm init` --To initialise the project inorder to create a file name package.json

![package.json installed](./Images/npm%20initialising.png)

`npm install express` --To install expressjs

`touch index.js` --To create a file index.js

`npm install dotenv` ---To install the dotenv module

`vim index.js`

```
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

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```
`node index.js`  ---Status checked OK

![working express](./Images/express%20working.png)

`mkdir routes`

`cd routes`

`touch api.js`

`vim api.js`

```
const express = require ('express');
const router = express.Router();
router.get('/todos', (req, res, next) => {
});
router.post('/todos', (req, res, next) => {
});
router.delete('/todos/:id', (req, res, next) => {
})
module.exports = router;

```

`npm install mongoose` --Changed dir to /Todo and install mongoose

`mkdir models`

`cd models`

`touch todo.js`

`vi todo.js`

```
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
```
`cd routes` --Go back into Todo directory, enter the route directory and edit api.js

`vim api.js` --Delete the content inside api.js and replace with the one below

```
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
```
`touch .env`
`vi .env`

```
DB = mongodb+srv://wale-idowu:S3cur1tyGlory2012@cluster0.vxst8rq.mongodb.net/?retryWrites=true&w=majority
```
`vim index.js` --Deleted everything in the curent index.js and replaced this with the below

```
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
```
`node index.js` --To start the server

![working nodeServer](./Images/nodejs%20server%20working.png)

**MONGO DB**
```
To be able to store our data we need a platform that offers DBaaS; It was signed up and get started checklist was completed.
```

![MongoDB DB Configured](./Images/MongoDB_DBAccess.png)

![MongoDB NetAccess Configured](./Images/MongoDB_NetAccess.png)

![MongoDB Cluster Connect](./Images/MongoDB_Cluster_Connect.png)

`touch .env` ---In the index.js file, we specified process.env to access environment variables, but we have not yet created this file. So we need to do that now. Create a file in your Todo directory and name it .env.

`vi .env` --- Edit the file and paste the below in it (Replace the password with the password for DB wale-idowu); saved and quit afterwards.

DB = mongodb+srv://wale-idowu:<password>@cluster0.vxst8rq.mongodb.net/?retryWrites=true&w=majority 

```
Next step is to update the index.js to reflect the use of .env so that Node.js can connect to the database. We deleted existing content in the file, and update it with the entire code below.

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
```
`node index.js` --- Started the nodejs after and check if the DB connect

![MongoDB Cluster Connect](./Images/MongoDB_Connected.png)

```
Next Step is that we tested our Backend using postman as an API to communicate between MongoDb and Frontend
```

![Post Request Status OK](./Images/post_request.png)

![Get Request Status OK](./Images/get_request.png)

**STEP 2 – FRONTEND CREATION**

Since we are done with the functionality we want from our backend and API, it is time to create a user interface for a Web client (browser)
to interact with the application via API. To start out with the frontend of the To-do app, we will use the create-react-app command to
scaffold our app.

`npx create-react-app client` ---This created a new folder in the Todo directory called client.

Before testing the react app, there are some dependencies that need to be installed like concurrently and nodemon.

`npm install concurrently --save-dev` ---used to run more than one command simultaneously from the same terminal window.

`npm install nodemon --save-dev` ---used to run and monitor the server, if there is any changes in the server code, nodemon will restart it
automatically and load the new changes.

In Todo folder the package.json file was Change leaving the code inside with the below script.
```
{
  "name": "todo",
  "version": "1.0.0",
  "description": "A todo App",
  "main": "index.js",
  "scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
  },
  "keywords": [
    "todo",
    "application"
  ],
  "author": "Olawale Idowu",
  "license": "ISC",
  "dependencies": {
    "dotenv": "^16.0.3",
    "express": "^4.18.2",
    "mongoose": "^7.0.3"
  },
  "devDependencies": {
    "concurrently": "^8.0.1",
    "nodemon": "^2.0.22"
  }
}
```
package.json file in the client dir was configured

`cd client`
`vi package.json` --- "proxy": "http://localhost:5000" this key value pair was added for the purpose to access the application directly from the browser by simply calling the server url like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos

`npm run dev` ---Run from Todo dir

![Nodejs running on port 3000](./Images/running_on-port3000.png)

Creating react component

`cd client` ---From Todo dir

`cd src` ---From the client dir

`mkdir components` ---Make a new dir called component from the src folder

`cd components` ---Changed to this new dir and create three files Input.js, ListTodo.js and Todo.js.

`vi Input.js` ---Copied and pasted  the below script

```
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
```
`npm install axios` ---Install Axios from the client dir

`cd src/components` 

`vi ListTodo.js` ---Copied and pasted  the below script

```
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
Then in your Todo.js file you write the following code
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
```
`cd ..` ---To move to src dir

`vi App.js` ---Copied and pasted  the below script

```
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
```
`vi app.css` ---copied and pasted the below script in it

```
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
font
-size: 1.5rem;
color: #787a80; }
input:focus {
outline: none; }
button {
width: 25%;
height: 45px;
border: none;
margin
-left: 10px;
font
-size: 25px;
background: #101113;
border
-radius: 5px;
color: #787a80;
cursor: pointer; }
button:focus {
outline: none; }
ul {
list
-style: none;
text
-align: left;
padding: 15px;
background: #171a1f;
border
-radius: 5px;
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
```

`vim index.css` ---Open index.css in the src dir, copied and pasted the below script

```
body {
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
}
```
`cd ../..` ---In Todo dir, run the below code

`npm run dev`

![Todo App Working](./Images/project3_completed.png)
