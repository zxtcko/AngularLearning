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

#####Install library
input npm install in terminal

```
npm install
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

#####Setting up index.html

```html

//index.html
<html>
  <head>
    <meta charset="utf-8">
    <title>IO Chat</title>

    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">
    <script src="https://code.jquery.com/jquery-latest.min.js"></script>
    <script src="/socket.io/socket.io.js"></script>

    <style>
        body{
          margin-top: 30px;
        }

        #messageArea{
          display: none;
        }
    </style>
  </head>
  <body>
    <div class="container">

      <div class="row" id="userFormArea">
        <div class="col-md-12">
          <form class="" id="userForm" action="index.html" method="post">
            <div class="form-group">
              <label>Enter Message</label>
              <textarea class="form-control" id="username"></textarea>
              <br />
              <input type="submit" class="btn btn-primary" value="Login" />
            </div>
          </form>
        </div>
      </div>

      <div class="row" id="messageArea">

        <div class="col-md-4">
          <div class="well">
            <h3>Online Users</h3>
            <ul class="list-group" id="users"></ul>
          </div>
        </div>

        <div class="col-md-8">
          
          <div class="chat" id="chat"></div>

          <form class="" id="messageForm" action="index.html" method="post">
            <div class="form-group">
              <label>Enter Message</label>
              <textarea class="form-control" id="message"></textarea>
              <br />
              <input type="submit" class="btn btn-primary" value="Send Message" />
            </div>
          </form>

        </div>

      </div>
    </div>

    <script>
      $(function(){
        var socket = io.connect();
        var $messageForm = $('#messageForm');
        var $message = $('#message');
        var $messageArea = $('#messageArea');
        var $chat = $('#chat');
        var $userForm = $('#userForm');
        var $userFormArea = $('#userFormArea');
        var $users = $('#users');
        var $username = $('#username');

        $messageForm.submit(function(e){
          e.preventDefault();
          socket.emit('send message', $message.val());
          $message.val('')
          console.log('Submitted', $message.val());
        });

        socket.on('new message', function(data){
          $chat.append('<div class="well"><strong>'+data.user+'</strong>: '+data.msg+'</div>')
        })

        $userForm.submit(function(e){
          e.preventDefault();
          socket.emit('new user', $username.val(), function(data){
            if (data) {
              $userFormArea.hide();
              $messageArea.show();
            }
          });
          $username.val('')
          console.log('Submitted', $username.val());
        });

        socket.on('get users', function(data){
          var html = '';
          for (var i = 0; i < data.length; i++) {
            html += '<li class="list-group-item">'+data[i]+'</li>';
          }
          $users.html(html);
        });
      });
    </script>
  </body>
</html>
```
#####Test
input node server in terminal

```
node server
```
open chrome and input localhost:3000

Have fun~