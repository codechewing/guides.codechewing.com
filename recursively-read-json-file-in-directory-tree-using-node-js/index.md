# Recursively read JSON file in directory tree using node.js

Not exactly a catchy title, but something I wanted to explore myself.

The scenario is that we want to read the contents of a JSON file (doesn't have to be JSON of course...) in each
directory and pluck some information out of it to store some meta data about the directory structure we're walking.

Let's suppose we have the following directory structure:

```
.
└---food
│   │   meta.json
│   │   ...
│   │
│   └---sweets
│   │   │   meta.json
│   │   │   ...
│   │   │
│   │   └---lollipop
│   │       │   meta.json
│   │       │   ...
│   │       
│   └---vegetables 
│       │   meta.json
│       │   ...
│       │
│       └---cabbage
│           │   meta.json
│           │   ...
```

You can see that we've got 5 `meta.json` files at varying depth in the directory structure. Each one contains
the following JSON:

```
{
  "created": 1481926887046
}
```

Just pretend they've each got a different timestamp.

We'll imagine this timestamp gives some extra meaning to the directory where it resides - some information
that we're going to store into memory after the directory crawl has completed.

Of course, there are already libraries available that do directory walking, such as 
[node-dir](https://www.npmjs.com/package/node-dir)(rel="nofollow noopener noreferrer" target="_blank")
and [walk](https://www.npmjs.com/package/walk)(rel="nofollow noopener noreferrer" target="_blank").
But it's a good learning exercise to create the node.js script ourselves. The programming topics; asynchronous and
recursion, often come up in interview questions etc., so it's useful to wrap your head around. Both are also very
practical skills.

Our script is going to be called `get-directory-info.js`, and has the following annotated content:

```
const fs = require('fs');
const onComplete = dirs => console.log(dirs);
const onError = err => { throw err };

(function (error, complete) {
  let readdirCallbacks = 0;
  const dirs = [];
  const done = () => complete(dirs);

  (function traverse(path) {
    readdirCallbacks++;

    fs.readdir(path, 'utf-8', (err, files) => {
      if (err) return error(err);

      // If this directory has no items, decrement the
      // `readdirCallbacks` and check to see if this
      // is actually the last one.
      if (files.length === 0) {
        if (--readdirCallbacks === 0) return done();
      }

      let statCallbacks = files.length;

      // Read the JSON file, so that we can store some
      // information about the directory.
      fs.readFile(`${path}/meta.json`, 'utf-8', (err, content) => {
        if (err) return error(err);

        // Store the meta information about the directory.
        // Only the created property is available in
        // this example's JSON.
        dirs.push({
          dir: path,
          created: JSON.parse(content).created
        });

        // Loop over each item in this directory.
        files.forEach((file) => {
          fs.stat(`${path}/${file}`, (err, stats) => {
            if (err) return error(err);

            statCallbacks--;

            // If this item is a directory, begin recursion
            // of subdirectory.
            if (stats.isDirectory()) traverse(`${path}/${file}`);

            // If all the statCallbacks are complete for this
            // directory, decrement the `readdirCallbacks` and
            // check to see if this is actually the last one.
            if (statCallbacks === 0) {
              if (--readdirCallbacks === 0) return done();
            }
          });
        });
      });
    });
  }(`${__dirname}/food`));
}(onError, onComplete));

```

## Breaking down the above traversing code

Some of the code is already annotated, but some further explanation can help understand what's going on in greater
depth.

```
// ...

(function (error, complete) {
  let readdirCallbacks = 0;
  const dirs = [];
  const done = () => complete(dirs);

  (function traverse(path) {
    // ...
  }(`${__dirname}/food`));
```

Above, the reason for wrapping the recursive `traverse` function inside of a closure is so that it immediately executes.
This closure has been wrapped within another closure, so that some state can be shared across traversal cycles, such
as:
- `readdirCallacks` to register the number of asynchronous 'reading the filesystem' callbacks that are in progress
- `dirs` to store the meta information about each directory, which gets populated from inside the `traverse` method
- `done` to call the passed in `complete` method upon completion of the full directory tree walking

To give your brain some run for its money, there are three asynchronous by default methods that we're using within
`traverse`. Namely `readdir`, `readFile` and `stat`. The consequence is that we need a system of tracking when some of
the callbacks have completed, to know when the directories have been fully walked.

We track the `readdir` callbacks in the outer closure (`readdirCallbacks`), but track the `stat` callbacks within the
inner closure (`statCallbacks`). The `stat` callbacks that are nested inside the `traverse` closure need to be aware
of the `readdir` callbacks across any other traversal instances, which is why it's scoped in this manner.

```
// ... 

  (function traverse(path) {
    readdirCallbacks++;

    fs.readdir(path, 'utf-8', (err, files) => {
      // ...
```

Above, before we kick off another asynchronous `readdir` call, we increment `readdirCallbacks` accordingly.

```
// ...

let statCallbacks = files.length;

// ...
```

Above, at this point in the code, we know that calling the asynchronous method `stat` on every file that was
discovered by `readdir` is forthcoming, so we set the `statCallback` count accordingly.

Once we've asynchronously read the `meta.json` file with `readFile`, within that `readFile` callback we can now
proceed with calling `stat` on each of the files, as seen below:

```
// ...

files.forEach((file) => {
  fs.stat(`${path}/${file}`, (err, stats) => {
    if (err) return error(err);

    statCallbacks--;
    
    // ...
```

Above, once inside a `stat` callback, we know we can decrement `statCallbacks` accordingly. 

Once we know when all crawling has finished, we can do something like display or store all the accumulated
directory information. In this example, we call the `complete` method, which simply logs the data into the console.

### Running the script

Your directory structure should now look like this:

```
.
│   get-directory-info.js
└---food
│   │   meta.json
│   │   ...
│   │
│   └---sweets
│   │   │   meta.json
│   │   │   ...
│   │   │
│   │   └---lollipop
│   │       │   meta.json
│   │       │   ...
│   │       
│   └---vegetables 
│       │   meta.json
│       │   ...
│       │
│       └---cabbage
│           │   meta.json
│           │   ...
```

Open the console at this directory and run:

<kbd class="terminal">node get-directory-info</kbd>

If all goes to plan, you'll be presented with the `dirs` array of meta objects:

<pre class="with-sample">
<samp>
$ node get-directory-info 
[ { dir: '/path/to/food',
    created: 1482011736708 },
  { dir: '/path/to/food/vegetables',
    created: 1482020376708 },
  { dir: '/path/to/food/sweets',
    created: 1482037656708 },
  { dir: '/path/to/food/vegetables/cabbage',
    created: 1482029016708 },
  { dir: '/path/to/food/sweets/lollipop',
    created: 1482046296708 } ]
</samp>
</pre>

#### Farewell

You've seen how to recursively walk a directory structure, using asynchronous methods, and enabled a way to track
progress so that you can fire a done callback when all traversal has completed.
