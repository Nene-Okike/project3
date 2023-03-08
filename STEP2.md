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
