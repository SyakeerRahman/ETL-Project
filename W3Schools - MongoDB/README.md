# MongoDB Getting Started

## Local vs Cloud Database

1. Signup at MongoDB Atlas https://www.mongodb.com/cloud/atlas/register

## Create a cluster

1. After you have created your account, set up a free "Shared Cluster" then choose your preferred cloud provider and region.

By default, MongoDB Atlas is completely locked down and has no external access.

You will need to set up a user and add your IP address to the list of allowed IP addresses.

Under "Database Access", create a new user and keep track of the username and password.

Next, under "Network Access", add your current IP address to allow access from your computer.

## Install MongoDB Shell (mongosh)

There are many ways to connect to your MongoDB database.

We will start by using the MongoDB Shell, mongosh.

Use the official instructions to install mongosh on your operating system.

To verify that it has been installed properly, open your terminal and type:

```ruby
mongosh --version
```

## Connect to the database

To connect to your database, you will need your database specific connection string.

In the MongoDB Atlas dashboard, under "Databases", click the "Connect" button for your Cluster.

Next, choose "Connect with the MongoDB Shell".

Copy your connection string.

Example
Your connection string should look similar to this:

```ruby
mongosh "mongodb+srv://cluster0.ex4ht.mongodb.net/myFirstDatabase" --apiVersion 1 --username YOUR_USER_NAME
Paste your connection string into your terminal and press enter.
```

You will be prompted to enter your database user password that you created earlier.

You are now connected to the database!



