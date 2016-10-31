<h1 align="center">
  <br>
  <a href="https://rationalcoding.github.io/HyperHost/"><img src="https://s12.postimg.org/6asslh8hp/HH_logo.png" alt="HyperHost" width="200"></a>
  <br>
  HyperHost
  <br>
  <br>
</h1>
<h4 align="center">Peer-To-Peer Node Servers in the Browser.</h4>
<br>

**HyperHost** serves dynamic websites via WebRTC.

Go to https://rationalcoding.github.io/HyperHost/ and drag n' drop the folder of any *static* website containing at least an **index.html**. A link to your hosted site will appear after a few seconds. Then, anyone with a WebRTC enabled browser can see it from anywhere. It's that easy!

Your site will be available so long as your browser window is open. All resources are served via an encrypted P2P connection.

### You mentioned Node?
You can run a Node-like server alongside the HyperHost static server.  

Put your server's starting script in a file called **HH-server.js**, then drag n' drop into HyperHost like you would a static site.  
Put any additional server code in files with the **HH-** extension. We can **require** these.  
Put any modules you need to be downloaded from NPM in a file called **package.json**. These will be fetched by Browserify CDN.  

Example of a **HH-server.js**:
```javascript
var hyperhost = require('hyperhost'); //This module lets us handle P2P connections
var fs = require('browserify-fs'); //We can require ANY module that can be Browserified
var custom = require('custom'); //We can require our other server files.
var app = hyperhost.createApp(); //Creates our app

//Handles 'get requests' to the '/' route
app.get('/', function (req, res) {
    //We can use our modules
    
    custom.someFunction();
    fs.mkdir('/home');
    fs.writeFile('/home/hello-world.txt', "Virtual file system? Yes please!");
    
    //We can send any data over the connection (including files, blobs, anything)
    res.send({
        exampleData: "hello!",
        moreData: [1, 4, 2, 3],
        more: {
            anyData: {}
        }
    });
});

app.listen(); //This starts the virtual backend
```

Example of a **package.json**:
```javascript
{
  "name": "MyNodeApp",
  "dependencies": { 
    "browserify-fs": ">1.0.0", //These are modules that will be pulled from NPM
    "crypto-browserify" : "", //Don't specify a version number for the latest version
  }
}
```

Calls to this virtual server can only be made from the site being hosted.  
HyperHost defines the `HyperRequest` object, which is similar to the `XMLHttpRequest` object.
```javascript
//Here is an example request
var hyp = HyperRequest(); //Create a new HyperRequest
hyp.onload = function (response) { //Set the callback
    console.log(response);
    document.getElementById("output").innerHTML = JSON.stringify(response);
}
hyp.open("GET", "/"); //Set the method and route
hyp.send({ //Send arbitrary data to server
    message: "hello",
    moreData: [12, 42, 21, ],
    evenMore: {}
});
```

##Great for demos and hackathons!

**Upcoming Features:**  
- Distributed hosting - Allow clients to opt-in to helping you host the site via a mesh network. (Still deciding on a topology) 
- Rehost option - Store processed site in localstorage for fast redeployment. 
- More supported upload formats. (ZIP, individual files, non-webkit folders, ...)
- Search other websites that are hosted. 
- Full documentation and better examples. 
- More custom modules (WebSockets, MySQL, templating views)  

**Current Limitations:**  
- Any Node modules used must be Browserify-able.  
- The host must be running Chrome. The client can be [any browser supporting WebRTC](http://caniuse.com/#feat=rtcpeerconnection).
- Very large pages, can freeze up when clients attempt to load them. 
- Relative URLs pointing to hosted files inside **Javascript** files will not work. (JS cannot easily be refactored, an API for allowing this is forthcoming)
- The **require** method only supports module *names* and not *paths*. `require('custom')`, not `require('./custom')`. (Use Browserify before uploading if you need this behaviour)

**Notes:**  
You can host the files in this repo anywhere (even on a file:// domain!) if you don't want to use Github's servers for the initial resources. You can also use any PeerJS signalling server.

### [MULTIHACK](https://rationalcoding.github.io/multihack) is a web-based IDE that uses HyperHost to deploy your project even faster!
