# Customise your modernizr build using npm

Using the **modernizr CLI**, combined with some npm action, you can craft a **custom modernizr bundle**, serving a
reduced download size for your users, by only including the specific feature detects you actually need.

## Setup modernizr bundle config

Grab a copy of modernizr, via npm:

```
npm i modernizr --save
```

Create the following `modernizr.json` file:

```
{
  "feature-detects": [
    "css/flexbox",
    "css/flexboxlegacy",
  ]
}
```

Replace the above config with whatever settings you require. Check out the
[full list of options](https://github.com/Modernizr/Modernizr/blob/master/lib/config-all.json)(rel="nofollow noopener noreferrer" target="_blank")
over on modernizr's github repo.

### Modernizr CLI

Run the following command to build your optimised modernizr bundle:

```
./node_modules/.bin/modernizr -uc ./modernizr.json -d ./modernizr.min.js
```

What's that now?

1. `-uc` - The `u` is for uglify, and the `c` is to specify a custom config file (`./modernizr.json`). 
3. `-d` - The destination for the compiled bundle (`./modernizr.min.js`).

### Farewell

With a real simple build step to your project, you can save on download size, and code complexity, by reducing your
modernizr bundle size. Totally worth it.
