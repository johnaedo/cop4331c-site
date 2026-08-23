---
share_cop4331c: "true"
site-folder: Code Demos and Tutorials/MERN Stack
---

# MERN Production Deployment

So far you've built your MERN application entirely on your local computer.  You've been using `localhost` and unencrypted/plain-text HTTP for all of your communications.  Now it's time to get this out into production, on a real domain name and properly secured with HTTPS!
## Prerequisites
- Domain Name
- Digital Ocean droplet with its IP address mapped to your domain name

# Add Support for Production URL to the Front End

For these next steps, we'll use my API URLs throughout the examples:

http://mern.johnaedo.com:5000/api/login
http://mern.johnaedo.com:5000/api/searchcards
http://mern.johnaedo.com:5000/api/addcard

We'll want to modify the front end to use either `localhost` if it's running on your local computer or `mern.johnaedo.com` if it's running in production.  We'll use a conditional and write a function that supports local and remote URLs.

## Update Login and CardUI Components

TODO: Re-order so dotenv gets installed first
### Add a Dynamic Path Builder Function

For now, until we get to MERN C, place this in `login.tsx` and `cardui.tsx`, thus violating the DRY principle.

```js
const app_name = 'mern.johnaedo.com';
function buildPath(route:string) : string
{
    if (process.env.NODE_ENV != 'development') 
    {
        return 'http://' + app_name + ':5000/' + route;
    }
    else
    {        
        return 'http://localhost:5000/' + route;
    }
}
```
### Update Login and CardUI Components to use the Builder Function

#### Login.tsx

```js
const response = await fetch(buildPath('api/login'),
{method:'POST',body:js,headers:{'Content-Type': 'application/json'}});
```

#### CardUI.tsx

##### addCard
```js
const response = await fetch(buildPath('api/addCard'),
{method:'POST',body:js,headers:{'Content-Type': 'application/json'}});
```
##### searchCards
```js
const response = await fetch(buildPath('api/searchCards'),
{method:'POST',body:js,headers:{'Content-Type': 'application/json'}});
```

## Install `dotenv` on both front- and back-ends

### `cards/front-end`

#### Install `dotenv`
```bash
sudo npm install dotenv
```

#### Create the `.env` file
Create a file with the editor of your choice called `.env`

```bash
vi .env
```

#### Add your `NODE_ENV` variable
```
NODE_ENV=development
```
This will be used to switch back and forth between Production and Development modes.  For now, this only controls which back-end URL we're using (`localhost` vs. your domain name), however in more complex projects, it can be used to control any other variables that would differ between production and deployment.

TODO: Add code for a "DEV MODE" banner.


#### Import and Initialize `dotnev`
On the frontend in both `Login.tsx` and `CardUI.tsx`, you'll need to import dotenv and initialize it:

```js
import {config} from 'dotenv';
```

Then, inside the component object:
```js
config();
```
### `cards/back-end`

#### Install `dotenv`
```bash
sudo npm install dotenv
```

#### Create the `.env` file
Create a file with the editor of your choice called `.env`

```bash
vi .env
```

#### Add your connect string
`MONGODB_URI=Your connect string here`


> [!WARNING] .env has secrets which must not be told!
> Make sure that `.env` has been added to `.gitignore` as this file will contain your database connect string and cannot be uploaded to GitHub

#### Update `server.js` to use the environment file

Now use this instead of the hard-coded connection string in server.js

Change:
`const url = "Your connect string here"`

To:
```js
require('dotenv').config();
const url = process.env.MONGODB_URI;
```

# Build for Production Deployment

Now  you need to build the webapp. From the frontend directory in a terminal type 
```bash
npm run build.
```


You can find the build inside of the `frontend` folder in a folder named `dist`.

# Setup the Server

Use SSH to log on to the server.

TODO: Use unprivileged account instead
## Install server software

###  Install Node via VNM
Use the same instructions as you did on your laptop to install Node on the server under the root account.

TODO: Use `n` instead of NVM
### Install nginx
```bash
sudo apt-get update
sudo apt-get install nginx
```
## Configure nginx on the server

### Setup a site

Navigate to /etc/nginx/sites-available
Open the `default` site with a text editor.
Edit as follows (*make sure to use your domain*)

```toml
server {
        listen 80;
        listen [::]:80;

        root /var/www/html;
        index index.html;

        server_name mern.johnaedo.com www.mern.johnaedo.com 209.38.68.57;

        location / {
                try_files $uri $uri/ /index.html;
        }
}
```
### Update your DNS information
Go to your domain registrant and set your domain name to point to this server.

### Create a basic test web page

Navigate to /var/www/html
Delete the default html file.
Create index.html as follows:
```html
<html>
	<body>
		<h1>We love COP 4331</h1>
	</body>
</html>
```
### Test nginx with your domain

With a browser use the domain name to access your site.
You should see a very simple page with large text: "We love COP 4331"

TODO: Browser screenshot

# Deploy front-end to the server

### Copy the local built files to the server

In the terminal, navigate to your project's `frontend` directory. 
Then from there, use `scp` to copy the contents of the `dist` folder to the server.

```bash
cd ~/myprojectdir/cards/frontend
scp -r ./dist/* root@mern.johnaedo.com:/var/www/html
```
 TODO:  Use unprivileged user here.

### Reboot the server.

Go back to your browser and refresh

Unfortunately, you cannot login because there is no API server listening. Let’s do that next.

# Setup the API Server

## Create the server directory

First, we will create a directory on the server and then copy our server files into it.

From /var to the following:
```bash
mkdir cardsServer
cd cardsServer
```
Now the path to your cards server is `/var/cardsServer` (remember that Linux is case sensitive).

TODO: under unprivileged user, deploy to home directory instead.

## Copy the Node Express code to the server

> [!NOTE]
> We will not copy the node_modules directory since it is very large and we can re-install it on the server.
> 

In the terminal on your local computer, from the `cards/backend` directory:
```bash
scp server.js root@mern.johnaedo.com:/var/cardsServer
scp package.json root@mern.johnaedo.com:/var/cardsServer
scp package-lock.json root@mern.johnaedo.com:/var/cardsServer
scp .env root@mern.johnaedo.com:/var/cardsServer
scp -r .git root@mern.johnaedo.com:/var/cardsServer
```
## Install dependencies

navigate to `/var/cardsServer` and npm install (this rebuilds the node_modules directory):

```bash
npm i
```

edit `package.json` and add a `scripts` entry for our production run.  Remember that this is a production installation, we don't need file monitoring.  Your code should be spit-polished perfect!  Right?  Your code is flawless before you deployed to Production, riiiiight?  ;-)

```json
{
  "name": "backend",
  "version": "1.0.0",
  "description": "MERN Stack Demo",
  "license": "ISC",
  "author": "John Aedo",
  "type": "commonjs",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "nodemon ./server.js",
    "prod": "node ./server.js"
  },
  "dependencies": {
    "cors": "^2.8.6",
    "express": "^5.2.1",
    "mongodb": "^7.1.0"
  }
}
```

Navigate to /var/cardsServer
```bash
Run npm install -g pm2
Run pm2 start server.js --name "express-server"
Run pm2 startup
Run pm2 save
```

The front end cannot contact the back end because the front end is actually running on your local machine and trying to use port 5000 on the back end server. 

> [!IMPORTANT]
Open port 5000 with `sudo ufw allow 5000`


# CI/CD

## CI/CD Pipeline Using GitHub Actions

Source: https://docs.github.com/en/actions 

A continuous integration/ continuous deployment (CI/CD) pipeline is used to automatically deploy your application from GitHub to your digital ocean droplet whenever a condition you set is reached. This simplifies keeping your site up to date with your current production application.

GitHub Actions is GitHubs service for creating CI/CD pipelines from github. It uses a GitHub Action VM to execute commands on your droplet in order to pull your application’s repo to the droplet, build it and run any other commands that need to be run in order to serve your application. You’ll create a workflow file, dictating how your pipeline will execute. To help make this process easier, you can integrate action workflows created by other github users into your workflow.

## Creating Your CI/CD Pipeline
### Configuring GitHub Secrets
        
 In order to have the actions VM access your droplet, you need to store your droplets access information in your repo’s action secrets.

To get to action secrets, on your repo’s page, go to Settings>Secrets and Variables> Actions
You should now be on this page, but you will not have any secrets yet.
You will need to add these 3 secret variables shown below.
SSH_HOST -> your droplet’s IP address
SSH_USERNAME -> the user you want the CI/CD pipeline to use
SSH_KEY -> the password for the provided user

> [!IMPORTANT]
You can not edit or view the secrets after you make them. When you click on the edit button, your only option is to enter a new value to update the secret to.

### Configuring GitHub Workflow File
        
 To dictate how your CI/CD pipeline will work, you need to create a workflow file titled main.yaml in the directory .github/workflows which is in the root of your repo.
You may need to create this directory, so ensure that it is named correctly when you do.

The Github workflow files have their own syntax, so follow the syntax I use in my example to create your file. You can read the linked documentation to learn more about workflow files and what else you can use them for.
To explain how to configure main.yaml, I'll split it into two sections, the header and the jobs section.
#### Header Section
 This section holds information about your pipeline, such as the name and the conditions that cause it to execute.

Name -> pretty obvious what this is
On ->
	This section specifies what causes your workflow file to execute. In my example, I have the workflow run whenever there is a push or pull request to the master branch.
	You can also have it executed at specific times or when other conditions are met, check the docs for more info on other conditions.
#### Jobs Section
This section holds all of the scripts, actions and commands you want GitHub to do in order to prepare your repo for transfer, connect to your droplet, and build/start your application.To prepare a Node.js application, we first need to prepare the GitHub VM to handle a Node.js app.

This build job does just that.

For this section you can use this exact code, except ensure that the node-version is the version of node your application will be using.
The actions/checkout and actions/setup-node are actions created by other users for preparing the actions vm to handle your node app.

#### Deploy Section
This is the section where the action’s vm connects to your droplet, pulls your repo and builds your application.

Above is an example of a deploy section.
The needs tag specifies that deploy will only run if the build job is successfully completed first.
You can copy up to the run step into your workflow
    • Checkout Code  -> prepares the actions vm for this job
    • Install sshpass -> install sshpass so the actions vm can ssh into your droplet
    • env -> specifies the variables that will be used in the run step. These are the variables you created in the configure actions secret step and they’re used for the action vm to ssh into your droplet

The run step is where any commands you want the actions vm to execute are. They are written in order of execution.

**Step 1**: ssh into your droplet. You can copy this line into your workflow

`sshpass -p "${SSH_KEY}" ssh -o StrictHostKeyChecking=no ${SSH_USERNAME}@${SSH_HOST} << 'EOF'`

**Step 2**: navigate to the location of your application’s repo with cd

**Step 3**: pull the desired branch of your repository from github to your droplet.
    • Note that if your repo does not have a clean working tree on your droplet, this pull will fail so ensure that your repo on your droplet never has any uncommitted changes.

**Step 4**: install dependencies with npm install

**Step 5**: build your application with whatever command you use to build it
    • In my case I use npm run build

**Step 6**: restart your application using whatever web serving service you are using.
    • In my case I restart nginx
    • If you are using pm2 you’d use the command “pm2 restart \<app name\>” here instead.

**Step 7**: specify the end of the job with the EOF flag


> [!IMPORTANT]
This is a very rudimentary CI/CD pipeline meant to only keep your website up to date with your production application, there are many more things you can add to this to enhance it such as automatic tests after deploying.

> [!WARNING]
When doing a pull request through GitHub.com, you must wait for the pre tests to be completed before clicking merge pull request, otherwise you risk crashing your droplet    

## The CI/CD pipeline

```yaml
name: CI/CD Pipeline
on:
  push:
    branches:
      - master
  pull_request:
    branches:
      - master
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    - name: Set up Node.js
      uses: actions/setup-node@v2
      with:
        node-version: 20.16.0
    - name: Install dependencies
      run: npm install
  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Install sshpass
        run: sudo apt-get install -y sshpass
      - name: Deploy to DigitalOcean Droplet
        env:
          SSH_HOST: ${{ secrets.SSH_HOST }}
          SSH_KEY: ${{ secrets.SSH_KEY }}
          SSH_USERNAME : ${{secrets.SSH_USERNAME}}
        run: |
          sshpass -p "${SSH_KEY}" ssh -o StrictHostKeyChecking=no ${SSH_USERNAME}@${SSH_HOST} << 'EOF'
          cd /var/www/html
          git pull origin master
          npm install
          npm run build
          systemctl restart nginx
          EOF
```

