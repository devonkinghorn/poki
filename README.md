# node-api-server-tutorial
Simple introduction to creating a REST service and api with node

We will show you how to take an existing application and put it into the express structure
First create a new express server with
<pre>
express poki
cd poki
npm install
</pre>

Then checkout this repository
<pre>
git clone https://github.com/mjcleme/node-api-server-tutorial.git
</pre>

Now copy the files into the correct places in the template
<pre>
cp node-api-server-tutorial/src/index.html poki/public
cp node-api-server-tutorial/src/style.css  poki/public/stylesheets/
cp node-api-server-tutorial/src/app.js  poki/public/javascripts/
</pre>

Now create a route to the index.html by editing routes/index.js to be
<pre>
router.get('/', function(req, res) {
  res.sendFile('index.html', { root: 'public' });
});
</pre>

## Running the server

Make sure the server is running correctly by running 
<pre>
npm start
</pre>
and accessing the URL 'http://yourserver:3000'

You should see that you havent set up the '/pokemon' route

First add the route and make sure it works when you access 'http://yourserver:3000/pokemon'
<pre>
router.get('/pokemon', function(req, res) {
  console.log("In Pokemon");
});
</pre>

Then add the pokemon array from node-api-server-tutorial/api-server.js
```javascript
var pokemon = [
  {
    name: 'Pikachu',
    avatarUrl: 'http://rs795.pbsrc.com/albums/yy232/PixKaruumi/Pokemon%20Pixels/Pikachu_Icon__free__by_Aminako.gif~c200'
  },
  {
    name: 'Charmander',
    avatarUrl: 'http://24.media.tumblr.com/tumblr_ma0tijLFPg1rfjowdo1_500.gif'

  },
  {
    name: 'Mew',
    avatarUrl: 'http://media3.giphy.com/media/J5JrPT8r1xGda/giphy.gif'
  },
  {
    name: 'Cubone',
    avatarUrl: 'http://rs1169.pbsrc.com/albums/r511/nthndo/tumblr_ljsx6dPMNm1qii50go1_400.gif~c200'
  },
  {
    name: 'Cleffa',
    avatarUrl: 'http://media1.giphy.com/media/pTh2K2xTJ1nag/giphy.gif'
  },
  {
    name: 'Gengar',
    avatarUrl: 'https://s-media-cache-ak0.pinimg.com/originals/7e/3b/67/7e3b67c53469cc4302035be70a7f2d60.gif'
  }
];
```

Now, send back the array in your route
<pre>
res.send(pokemon);
</pre>

Some of you may want to access a REST service that doesnt have the CORS headers for your project.  Lets go through an example of how to do this

Lets say we want to get information about candidates from 'https://zlzlap7j50.execute-api.us-east-1.amazonaws.com/prod'

If we put the URL into our angular controller  at public/javascripts/app.js with something like
```javascript
function pokemonFetcher ($http) {
  
  var API_ROOT = 'pokemon'
  return {
    get: function () {
      return $http
        .get(API_ROOT)
        .then(function (resp) {
          return resp.data
        })
    },
    tryit: function() {
      var politics = "https://zlzlap7j50.execute-api.us-east-1.amazonaws.com/prod";
      return $http
        .get(politics)
        .then(function (resp) {
          console.log("Get Worked");
          console.log(resp.data);
          return resp.data
        })
    }
  }

}

function mainCtrl ($scope, pokemonFetcher) {

  $scope.pokemon = []

  pokemonFetcher.get()
    .then(function (data) {
      $scope.pokemon = data
    })

  pokemonFetcher.tryit()
    .then(function (data) {
      console.log("tryit");
      console.log(data);
    })
}
```
You will get a CORS error on the console of your browser.

So, lets make a proxy for this route in routes/index.js

First add the request module to the top of your routes/index.js file
<pre>
var request = require('request')
</pre>
Then install the module
<pre>
npm install request --save
</pre>

Then use 'request' to pipe the output from the real URL back through the node server to your browser.
```javascript
var politics = "https://zlzlap7j50.execute-api.us-east-1.amazonaws.com/prod";
router.get('/politics', function(req,res) {
  console.log("In politics");
  request(politics).pipe(res);
});
```

Test the route by accessing the URL 'http://yourserver:3000/politics'

And change our angular call to point to this route in public/javascripts/app.js
<pre>
var politics = "/politics";
</pre>
