# How to disable or enable a stylesheet in JavaScript

You can programmatically **toggle CSS stylesheet visibility with JavaScript**.

One use case might be that you're offering a custom themed website or widget, that users can pick and choose from.
You could use this technique to toggle between some different stylesheets - the changes are immediately rendered.

For this example, let's say the website contains some CSS stylesheets in the `<head>`:

```
<!doctype html>
<html>
<head>
  <link rel="stylesheet" href="style.css" />
  <style>
    body {
      margin: 0;
      padding: 0;
    }
  </style>
```

All stylesheets on the web page can be found inside of the `document.styleSheets` collection.
Let's grab the first one to work with:

```
var stylesheet = document.styleSheets[0];
```

We've just stored `<link rel="stylesheet" href="style.css" />` into our `stylesheet` variable.

CSS stylesheets inside `document.styleSheets` also include any inline style blocks. In other words, both these
stylesheets will appear inside the `styleSheets` collection. So we can just as easily access the
`<style>` block with `document.styleSheets[1]` if we wanted to.

If you were to take a peek at your `stylesheet` variable in the dev console, you'd see something like the following:

<img
    src="/assets/images/posts/disable-enable-stylesheet-javascript/stylesheet-object.png"
    alt="CSS style sheet JavaScript object"
    class="img-thumbnail">

This is where it gets interesting, as the `CSSStyleSheet` object above is what we'll access to disable or
enable the stylesheet as we wish.

## Disable stylesheet

Now we've got hold of the stylesheet, we can disable it:

<pre><code class="js">stylesheet.disabled = true;</code></pre>

## Enable stylesheet

You guessed it:

<pre><code class="js">stylesheet.disabled = false;</code></pre>

The CSS change is immediately rendered on screen, and you're simply showing or hiding the CSS rules from being rendered.

### Toggling a matching stylesheet

In a rather contrived example, let's say that your website has three themes a user can choose from.
You load in all three stylesheets into the HTML:

```
<!doctype html>
<html>
<head>
  <link rel="stylesheet" href="theme-light.css" />
  <link rel="stylesheet" href="theme-dark.css" />
  <link rel="stylesheet" href="theme-default.css" />
```

On page load, `theme-default.css` wins the specificity battle, so that CSS is seen on screen.

Assume you've got some radio buttons where the user can select a different style. On selection, let's say you
pass the requested stylesheet to a function (i.e. 'dark'), which figures out which stylesheet to enable, whilst
disabling the rest:

```
var toggleTheme = function(themeName) {
  var stylesheets = document.styleSheets;
  var length = stylesheets.length;
  var i;

  for(i = 0; i < length; i++) {
    var ss = stylesheet[i];
    ss.disabled = (ss.href.indexOf(themeName) !== -1) ? false : true;
  }
};
```

Beware that inline style blocks (`<style>`) don't contain an `href` property, so if you have any on the page,
the above code will break. I know that the only stylesheets are `<link>` stylesheets on this page, but if you're
not sure, perform a defensive check against this in your own version.

### `document.styleSheets`, meh!

You might want more control over the stylesheets collection, perhaps you only want to select stylesheets embedded
into the HTML via `<link>`? We can use a normal DOM query to do just that:

```
var stylesheet = document.querySelector('[rel="stylesheet"]');
```

As it stands, `stylesheet` contains an ordinary HTML element. Meaning we can't access the `disabled` property quite yet.
We need to get to that `CSSStyleSheet` object as seen in earlier examples with `document.styleSheets`.

In most browsers, we can use `stylesheets.sheet`. But not Internet Explorer. Here's the cross browser solution:

```
var element = document.querySelector('[rel="stylesheet"]');
var stylesheet = element.sheet || element.styleSheet;
```

We can now disable or enable the stylesheet:

<pre><code class="js">stylesheet.disabled = true;
stylesheet.disabled = false;</code></pre>

### Farewell

Perhaps not a particularly common task on the web these days, but it could be useful to know how to programmatically
interrogate the CSS loaded in page, inline or external.
