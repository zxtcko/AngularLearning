# AngularLearning
Practice of basic web feature using Angular, including socket, authentication, mongodb connection etc

##iochat

A implementation of socket.io, long time, two way communication method library

#####Setup

First things first, init the node project. open terminal to the specfic folder, input the folloing code

``` 
npm init

This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg> --save` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
name: (AngualrLearning) app name
Sorry, name can only contain URL-friendly characters.
name: (AngualrLearning) appname
version: (1.0.0) 
description: 
entry point: (index.js) 
test command: 
git repository: (https://github.com/zxtcko/AngularLearning.git) 
keywords: 
author: Chris
license: (ISC) 
About to write to /Users/young/Documents/SelfDevelop/AngualrLearning/package.json:

{
  "name": "appname",
  "version": "1.0.0",
  "description": "Practice of basic web feature using Angular, including socket, authentication, mongodb connection etc",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/zxtcko/AngularLearning.git"
  },
  "author": "Chris",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/zxtcko/AngularLearning/issues"
  },
  "homepage": "https://github.com/zxtcko/AngularLearning#readme"
}


Is this ok? (yes) 
```

just follow the guide, after that you will get a `package.json` file. Add dependancy at the bottom to config socket io library

``` 
  "dependencies":{
    "socket.io":"*",
    "express":"*"
  }
```

#####Setting up the server.js 
server.js defines the function of socket, including the connection, username, message.

```javascript

//server.js

var express = require('express');
var app = express();
var server = require('http').createServer(app);
var io = require('socket.io').listen(server);

users = [];
connections = [];

server.listen(process.env.PORT || 3000);
console.log('Server Running...');

app.get('/', function(req, res){
  res.sendFile(__dirname + '/index.html')
});

io.sockets.on('connection', function(socket){
  connections.push(socket);
  console.log('Connected: %s sockets connected', connections.length);


  //disconnect
  socket.on('disconnect', function(data){
    // if (!socket.username) return;
    users.splice(users.indexOf(socket.username), 1);
    updateUsernames();

  });

  //send message
  socket.on('send message', function(data){
    console.log(data);
    io.sockets.emit('new message', {msg:data, user:socket.username});
  });

  //new user
  socket.on('new user', function(data, callback){
    callback(true);
    socket.username = data;
    users.push(socket.username);
    updateUsernames();
  });

  function updateUsernames(){
    io.sockets.emit('get users', users);
  }

});
```

