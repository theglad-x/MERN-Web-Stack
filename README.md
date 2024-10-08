![mlab](https://github.com/user-attachments/assets/b379599c-8b5f-4a13-b3f2-d71bc14d70ca)## Prerequisites
**Launch EC2 instance**
![EC2 Lanuch](https://github.com/user-attachments/assets/9176dc8b-5a00-4bfc-8f93-29959d921a1a)
![Connect to instance](https://github.com/user-attachments/assets/2e10ae98-611e-428c-8bb7-ff8192a6efbb)
![Running Instance](https://github.com/user-attachments/assets/8cd31174-d727-443d-8050-8e75b2884c19)

## Step 1 - Backend Configuration
1. update and upgrade server package index
   ``` bash
   sudo apt update
   ```
   ``` bash
   sudo apt upgrade
   ```
2. Node.js software location
   ``` bash
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   ```
3. Install Node.js on the server
   ``` bash
   sudo apt-get install -y nodejs
   ```
4. Verify the node installation
   ``` bash
   node -v
   ```
   ``` bash
   npm -v
   ```
## Step 2 - Application Code Setup
1. Create and change current directory to the newly create To-Do project directory
   ``` bash
   mkdir Todo && cd Todo
   ```
2.  Initialise the project
   ``` bash
   npm init
  ```
 Follow the prompts after running the command. Press enter several times to accept default values. A new file ``` package.json``` will be created

## Step 2 - Install ExpressJS
1. Install express using npm
   ``` bash
   npm install express
   ```
2. Create ``` index.js ```file
   ``` bash
   touch index.js
   ```
3. Install ``` dotenv ``` module
   ``` bash
   npm install dotenv
   ```
4. Open the index.js file with any text editor
   ``` bash
   nano index.js
   ```
  Type the code below into it and save.
  ``` bash
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
    console.log(`Server running on port ${port}`);
  });
  ```
5. Start the server to confirm that is working
   ``` bash
   node index.js
   ```
![Server](https://github.com/user-attachments/assets/14053493-ebd6-4c98-b68f-52099ee810f3)
Edit the security group to add an inbound rule to open TCP port 80 and port 5000
![Security-group](https://github.com/user-attachments/assets/07cb58f8-bac0-4baf-a539-f794ed5c2794)
Access the appilication on the browser
  ``` bash
  http://<PublicIP-or-PublicDNS>:5000
  ```
![Welcome](https://github.com/user-attachments/assets/ed09905d-8989-4762-80d4-14a385086eef)

## Step 3 - Creating Routes
There are three actions that our To-Do application needs to be able to do:

- Create a task
- Display list of all tasks
- Delete a completed task
Each task will be associated with some particular endpoint and will use differnet standard HTTP request menthods: POST, GET, DELETE.

For each task, routes that will define various endpoints that the To-do app will depend on will be created.
1. Create a folder
   ``` bash
   mkdir routes
   ```
2. Change directory to ``` routes ``` folder
   ``` bash
   cd routes
   ```
3. Create a new blank file ``` api.js ``` using any preferred a text editor and paste the below code
   ``` bash
   const express = require('express');
    const router = express.Router();
    
    router.get('/todos', (req, res, next) => {
    
    });
    
    router.post('/todos', (req, res, next) => {
     
    });
    
    router.delete('/todos/:id', (req, res, next) => {
     
    })
    
    module.exports = router;
   ```
## Step 4 - Define models
This app makes use of MongoDB, we need to create a model and a schema.

To create a schema and a model, install ``` mongoose``` which is a Node.js package that makes working with MongoDB easier.

1. Change directory back Todo folder and install Mongoose
   ``` bash
   cd ..
   ```
   ``` bash
   npm install mongoose
   ```
2. Create a new directory ``` models```, change to it and create a new file ``` touch.js ```
   ``` bash
   mkdir models && cd models && touch todo.js
   ```
3. Open ``` todo.js```with any preferred text editor and paste the code
   ``` bash
    const mongoose = require('mongoose');
    const Schema = mongoose.Schema;
    
    // Create schema for todo
    const TodoSchema = new Schema({
      action: {
        type: String,
        required: [true, 'The todo text field is required']
     }
    })
    
    // Create model for todo
    const Todo = mongoose.model('todo', TodoSchema);
    
    module.exports = Todo;
   ```
4. Update routes from the file ``` api.js ```in ```routes``` directory to make use of the new model. 
  ``` bash
  conconst express = require('express');
  const router = express.Router();
  const Todo = require('../models/todo');
  
  router.get('/todos', (req, res, next) => {
  
  
    // This will return all the data, exposing only the id and action field to the client
    Todo.find({}, 'action')
      .the(data => res.json(data))
      .catch(next);
  });
  
  router.post('/todos', (req, res, next) => {
    if (req.body.action) {
      Todo.create(req.body)
        .then(data => res.json(data))
        .catch(next);
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
## Step 5 - MongoDB Database
Connect to the mongodb database using [mlab](https://www.mongodb.com/products/try-free/platform/atlas-signup-from-mlab)
1. [Sign up](https://www.mongodb.com/products/try-free/platform/atlas-signup-from-mlab) to mongodb database

![mlab](https://github.com/user-attachments/assets/af1c3be6-31e5-4d53-a1fe-658ce3065158)

2. Create a cluster, select AWS as the cloud provider and choose a close region.
![cloud-mongodb](https://github.com/user-attachments/assets/571b2668-1b95-4b2f-baba-c387cbe53f44)

3. Cluster is created
![mongo-cluster](https://github.com/user-attachments/assets/9d716719-7d5a-435f-b247-73ffced5d8aa)

4. Create a user and give it admin access. Click on ```database Access``` on the left sidebar.
![mongo-database](https://github.com/user-attachments/assets/05f04a8a-6cb6-432d-8d64-cbbbb74cb06a)
![mongo-cluster](https://github.com/user-attachments/assets/19d2b078-0270-43f5-9d66-20ae07fdba09)
![mongo-detabase-access](https://github.com/user-attachments/assets/d44652af-3d59-4109-b181-036739fc49b9)

5. Click on ```Browse collection```, to create a database.
![mongo-cluster](https://github.com/user-attachments/assets/ab94ed27-9ca3-4ac8-8699-8a0bc0bfb228)

6. Edit IP Access List Entry to anywhere to access your database
![mongo-IP-access](https://github.com/user-attachments/assets/e85e0ab2-eccd-4804-b2e0-5a881a4c20f0)

7. Click on ```Connect``` to connect the Database with a Driver. Select ```mongoose``` and copy the Connection strings
![mongo-create-cluster](https://github.com/user-attachments/assets/e9a3f9bd-d68b-41b6-90fd-378507826148)
![connect-web-stack](https://github.com/user-attachments/assets/72a501b6-39ae-422d-b353-a4df07f44bbf)

8. Create a file in the ```Todo``` directory and name it ```.env```
   ``` bash
   touch .env
   ```
9. Open the ```.env``` file with a text editor and paste the connection string
    ``` bash
    DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
    ```
10. Update the ```index.js``` to reflect the use of ```.env``` so that Node.js can connect to the databse.
  ``` bash
    const mongoose = require('mongoose');
    const routes = require('./routes/api');
    const path = require('path');
    require('dotenv').config();
    
    const app = express();
    
    const port = process.env.PORT || 5000;
    
    // Connect to the database
    mongoose.connect(process.env.DB, { useNewUrlParser: true,useUnifiedTopology: true })
      .then(() => console.log(`Database connected successfully`))
       .catch((err) => console.log(err));
    
    // Since mongoose's Promise is deprecated, we override it with Node's Promise
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

