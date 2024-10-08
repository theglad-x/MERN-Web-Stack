# MERN WEB STACk
MERN stack is a full-stack development framework consisting of MongoDB, Express.js, React.js, and Node.js. Each component plays a crucial role in the development of modern web applications:

- **MongoDB:** A document-based, No-SQL database used to store application data in a form of documents.
- **Express.js:** A lightweight and flexible web application framework for Node.js. 
- **React.js:** A powerful JavaScript library for building user interfaces. It allows developers to create dynamic and responsive front-end components.
- **Node.js:** A runtime environment that allows JavaScript to be executed server-side.

The MERN stack provides an end-to-end solution for full-stack JavaScript development, enabling seamless interaction between the front-end and back-end. 

This project leverages MERN to create a robust and responsive To-Do application.The project demonstrates the creation of RESTful APIs for handling tasks such as creating, reading, and deleting to-do items, which are stored in a MongoDB database. The integration of React for the front-end provides a responsive user interface, while the back-end services handle data operations efficiently. This project also highlights the deployment of the app on an AWS EC2 instance and secure access via SSH.

## Prerequisites

**Launch EC2 instance**
![EC2 Lanuch](https://github.com/user-attachments/assets/9176dc8b-5a00-4bfc-8f93-29959d921a1a)

![Connect to instance](https://github.com/user-attachments/assets/2e10ae98-611e-428c-8bb7-ff8192a6efbb)

![Running Instance](https://github.com/user-attachments/assets/8cd31174-d727-443d-8050-8e75b2884c19)
# ssh to the instance
![ssh](https://github.com/user-attachments/assets/7d7d10ac-c3b7-44eb-b489-159037c2f282)

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
2. Initialise the project
   ``` bash
   npm init
   ```
 Follow the prompts after running the command. Press enter several times to accept default values. A new file ``` package.json``` will be created

## Step 3 - Install ExpressJS
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
       res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content- 
     Type, Accept");
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

## Step 4 - Creating Routes
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
## Step 5 - Define models
This app makes use of MongoDB, we need to create a model and a schema.

To create a schema and a model, install ``` mongoose``` which is a Node.js package that makes working with MongoDB easier.

1. Change directory back Todo folder and install Mongoose
   ``` bash
   cd ..
   ```
   ``` bash
   npm install mongoose
   ```
2. Create a new directory ``` models```, change to it and create a new file ``` todo.js ```
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
## Step 6 - MongoDB Database
Connect to the mongodb database using [mlab](https://www.mongodb.com/products/try-free/platform/atlas-signup-from-mlab)
1. [Sign up](https://www.mongodb.com/products/try-free/platform/atlas-signup-from-mlab) to mongodb database

![mlab](https://github.com/user-attachments/assets/af1c3be6-31e5-4d53-a1fe-658ce3065158)
2. Create a cluster, select AWS as the cloud provider and choose a close region.
![cloud-mongodb](https://github.com/user-attachments/assets/571b2668-1b95-4b2f-baba-c387cbe53f44)
3. Cluster is created
![mongo-cluster](https://github.com/user-attachments/assets/9d716719-7d5a-435f-b247-73ffced5d8aa)
4. Create a user and give it admin access. Click on ```database Access``` on the left sidebar.
![mongo-database](https://github.com/user-attachments/assets/05f04a8a-6cb6-432d-8d64-cbbbb74cb06a)
![mongo-detabase-access](https://github.com/user-attachments/assets/d44652af-3d59-4109-b181-036739fc49b9)
5. Click on ```Browse collection```, to create a database.
![mongo-cluster](https://github.com/user-attachments/assets/ab94ed27-9ca3-4ac8-8699-8a0bc0bfb228)
6. Edit IP Access List Entry to anywhere to access your database
![mongo-IP-access](https://github.com/user-attachments/assets/e85e0ab2-eccd-4804-b2e0-5a881a4c20f0)
9. Click on ```Connect``` to connect the Database with a Driver. Select ```mongoose``` and copy the Connection strings
![mongo-create-cluster](https://github.com/user-attachments/assets/e9a3f9bd-d68b-41b6-90fd-378507826148)
![connect-web-stack](https://github.com/user-attachments/assets/72a501b6-39ae-422d-b353-a4df07f44bbf)
10. Create a file in the ```Todo``` directory and name it ```.env``` 
   ``` bash
     touch .env
   ```
11. Open the ```.env``` file with a text editor and paste the connection string
   ``` bash
    DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
   ```
12. Update the ```index.js``` to reflect the use of ```.env``` so that Node.js can connect to the databse.
   ``` bash
          const express = require('express');
          const bodyParser = require('body-parser');
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
13. Start the server to see if the database is successfully configured
   ``` bash
    node index.js
   ```
![mongodb](https://github.com/user-attachments/assets/56d00154-b84c-44bb-b65f-98b052928d87)
## Step 7 - Test Backend Code without Frontend using RESTful API
Test the code using RESTfulL API. 
In this project Postman will be used th test the API. [Install Postman](https://www.postman.com/downloads/) on your machine or the browser
![postman-ci](https://github.com/user-attachments/assets/97aa3c7d-d2f8-48f4-9d06-7142f17f5fed)

Open Postman, create a POST request to the API ```http://<PublicIP-or-PublicDNS>:5000/api/todos``` This request sends a new task to our To-Do list so the appilication could store it in the database.
![post-postman](https://github.com/user-attachments/assets/9cbf2866-50f8-4445-82e8-00e75d489923)

Create a GET request to the API on ```http://<PublicIP-or-PublicDNS>:5000/api/todos```.
![get-postman](https://github.com/user-attachments/assets/b76d5fc4-3f56-4a20-a872-457b5e8af7e6)

## Step 8 - Frontend creation
1. In the same directory as the backend code, which is the Todo directory
``` bash
   npx create-react-app client
```
This will create a new folder in your ```Todo``` directory called ```client```
2. lnstall concurrently
   ``` bash
      npm install concurrently --save-dev
   ```
3. Install nodemon
``` bash
   npm install nodemon --save-dev
```
4. Configure nodemon in the package.json in the Todo directory
``` bash
   nano package.json
```
5. Paste this in the Scripts block:
``` bash
   {
     // ...
     "scripts": {
       "start": "node index.js",
       "start-watch": "nodemon index.js",
       "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
     },
     // ...
   }
```
6. Configure proxy in ``` package.json```
   change directory to ```client```
``` bash
   cd client
```
   Open the package.json file with a command line text editor
``` bash
   nano package.jon
```
   Add the key value pair in the package.json file
``` bash
   {
     "proxy": "http://localhost:5000"
   }
```
   ![proxy](https://github.com/user-attachments/assets/b245bb62-613a-45c5-ae45-a8d13de2a71e)
7. Run npm run dev and make sure you are in the todo directory and not in the client directory.
Ensure you are in the ```Todo ``` 
```bash
   npm run dev
````
Your app will be open and running on PublicIP:3000
## Step 9 - Creating React Components
Change to the client directory
   ```bash
   cd client
   ```
Move to the src directory
   ```bash
   src
   ```
Inside your ```src``` folder create another folder called ```components``` and inside it create three files ```Input.js, ListTodo.js, and Todo.js```.
   ```bash
   cd client && cd src && mkdir components && cd components

   touch Input.js ListTodo.js Todo.js
   ```
Open ```Input.js```file
   ```bash
   nano Input.js
   ```
   Copy and paste the following code
   ```bash
   import React, { Component } from 'react';
   import axios from 'axios';
   
   import Input from './Input';
   import ListTodo from './ListTodo';
   
   class Todo extends Component {
     state = {
       todos: []
     }
   
     componentDidMount() {
       this.getTodos();
     }
   
     getTodos = () => {
       axios.get('/api/todos')
         .then(res => {
           if (res.data) {
             this.setState({
               todos: res.data
             });
           }
         })
         .catch(err => console.log(err));
     }
   
     deleteTodo = (id) => {
       axios.delete(`/api/todos/${id}`)
         .then(res => {
           if (res.data) {
             this.getTodos();
           }
         })
         .catch(err => console.log(err));
     }
   
     render() {
       let { todos } = this.state;
       return (
         <div>
           <h1>My Todo(s)</h1>
           <Input getTodos={this.getTodos} />
           <ListTodo todos={todos} deleteTodo={this.deleteTodo} />
         </div>
       );
     }
   }
   export default Todo;
   ```
To make use of axios, which is a Promise-based HTTP client for the browser and node.js, you will need to navigate to the client directory from the terminal and run yarn add axios or npm install axios.

Move to client folder
   ``` bash
   cd ../..
   ```
Install Axios
   ```bash
   npm install axios
   ```
Go back to the component directory
   ```bash
   cd src/components
   ```
open the ```ListTodo.js```
   ```bash
   nano ListTodo.js
   ```
Copy and paste the following code
   ``` bash
      import React, { Component } from 'react';
      import axios from 'axios';
      
      import Input from './Input';
      import ListTodo from './ListTodo';
      
      class Todo extends Component {
        state = {
          todos: []
        }
      
        componentDidMount() {
          this.getTodos();
        }
      
        getTodos = () => {
          axios.get('/api/todos')
            .then(res => {
              if (res.data) {
                this.setState({
                  todos: res.data
                });
              }
            })
            .catch(err => console.log(err));
        }
      
        deleteTodo = (id) => {
          axios.delete(`/api/todos/${id}`)
            .then(res => {
              if (res.data) {
                this.getTodos();
              }
            })
            .catch(err => console.log(err));
        }
      
        render() {
          let { todos } = this.state;
          return (
            <div>
              <h1>My Todo(s)</h1>
              <Input getTodos={this.getTodos} />
              <ListTodo todos={todos} deleteTodo={this.deleteTodo} />
            </div>
          );
        }
      }
      export default Todo;
   ```
6. Adjust the React code by Deleting the logo and adjust App.js:
```bash
      cd ..
```
Make sure that you're in the src folder.
Open App.js with any text editor.
      ```
         vim App.js
      ```
Copy and paste the code below into it
   ```bash
      
      import React from 'react';
      import Todo from './components/Todo';
      import './App.css';
      
      const App = () => {
        return (
          <div className="App">
            <Todo />
          </div>
        );
      };
      
      export default App;
   ```
Open App.css 
      ```
      vim App.css
      ```
Paste this
   ```bash
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
          width: 100%;
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
Open ```index.js```
```bash
   vim index.js
   ```
Copy and paste
   ```bash
      body {
        margin: 0;
        padding: 0;
        font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen", "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue", sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        box-sizing: border-box;
        background-color: #282c34;
        color: #787a80;
      }
      
      code {
        font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New", monospace;
      }
   ```
Go back to the   ```Todo``` directory
   ```cd ../..```
In the Todo directory run:
      ```
      npm run dev
      ```
Todo App is ready and fully functional

<img width="1436" alt="my _todo" src="https://github.com/user-attachments/assets/e7f3ce73-4c37-4428-8be9-e5c11b091e8d">
<img width="1162" alt="todolist_test" src="https://github.com/user-attachments/assets/1304810a-438e-4511-a959-b51ac91253e6">

## Conclusion
This project successfully implements a simple yet powerful To-Do application using the MERN stack (MongoDB, Express, React, Node.js). It covers the process of setting up a backend API, connecting to a MongoDB cloud database, and testing with Postman. The frontend is built with React, providing an intuitive user interface. By deploying the application on AWS EC2, I demonstrate how to host a full-stack application on the cloud, making it accessible globally. This project serves as a foundational step in understanding full-stack development and cloud deployment.
