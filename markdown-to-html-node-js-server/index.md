# Markdown to HTML via node.js server

Here's our scenario to embark on:  
You've got some static markdown pages that you wish to convert into HTML. You've decided that you want to perform
this markdown to HTML conversion on the fly, so that when that particular URL request is made to your node.js server,
it returns not the markdown, but the HTML version instead. Sounds easy, right?

Let's assume some simple markdown:

```
Hello, **world**!
```

We've applied some bold to "world" using the asterisks.

Our directory structure is the following:

```
.
|--markdown
|    hello-world.md
```

We are going to use the markdown to HTML converter called
[Showdown](https://github.com/showdownjs/showdown)(rel="nofollow noopener noreferrer" target="_blank").
I'll use npm to install this package:

<kbd class="terminal">npm install showdown</kbd>

In fact, as we've also decided to serve our static markdown pages via node.js, I'll go ahead and install
[express](https://github.com/expressjs/express)(rel="nofollow noopener noreferrer" target="_blank") via npm as well:

<kbd class="terminal">npm install express</kbd>

Next up is to code an express server to intercept this request for our markdown page. Create a file named `server.js`
at the project root with the following:

```
var fs = require('fs');

var express = require('express');
var app = express();

var showdown = require('showdown');
var converter = new showdown.Converter();

app.get('/', function(req, res) {
  fs.readFile(__dirname + '/markdown/hello-world.md', 'utf-8', function(err, data) {
    if (err) throw err;
    res.send(converter.makeHtml(data));
  });
});

app.listen(3000, function() {
  console.log('Server is running...');
});
```

To quickly demonstrate the markdown to HTML conversion, the express driven node.js server listen on the route `/`
and reads the file contents of `hello-world.md` using the node.js core module, `fs`. If the contents of the markdown
file was read successfully, the `data` is passed into the Showdown's converter method, `converter.makeHtml`. This
uses the capability of Showdown to transform the markdown to HTML for us.

Our directory structure right now:

```
.
|--markdown
|    hello-world.md
|  server.js
```

Boot up the node.js server from the CLI:

<kbd class="terminal">node server</kbd>

Open up [http://localhost:3000/](http://localhost:3000/)(rel="nofollow noopener noreferrer" target="_blank")
in a browser and see your markdown transformed into HTML!

The response should look like this:

```
<p>Hello, <strong>world</strong>!</p>
```

You've gone and converted your markdown to HTML, using node.js via a server for on the fly transformation.
