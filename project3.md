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
