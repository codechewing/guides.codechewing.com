# Dynamically reference WordPress site URL in JavaScript

During my WordPress development, I often find myself in need of the WP home or blog URL from within the JavaScript code.
You can easily enough hardcode this to something like (assuming the WP site is at the root directory):

```
if (somethingIsTrue) {
  location.href = '/some-page';
}
```

Or maybe your WP site is within a sub-folder, so you replace `'/somePage'` with `'/wpsite/somePage'`.

This will be okay in a lot of scenarios. However, sometimes your WordPress site is not always situated at the same
directory across different development environments. Dev and production environments might not match URL structure.
This is a common pitfall when using the above technique.

In other words, this isn't going to be a robust solution at all. And we're better than that.

## Using `wp_localize_script`

Let's say that the very simple JavaScript code from above lives inside a script file, within a `js` directory of the
child theme, say here:

```
wp-content/themes/child-theme/js/script.js
```

Instead of hardcoding `'/some-page'` in the JavaScript, we're going to dynamically retrieve the site URL,
with a little help in PHP.

As good WP citizens, we're of course already loading our custom script into WordPress via the `wp_register_script`
and `wp_enqueue_script` methods. We're assuming that inside `functions.php`, you currently have the following
snippet of PHP:

```
wp_register_script(
  'codechewing_is_the_best',
  get_stylesheet_directory_uri() . '/js/script.js',
  array(), '1.0.0', true
);

wp_enqueue_script('codechewing_is_the_best');
```

We're going to use `wp_localize_script` to drop a global JavaScript object into the `window` that can be accessed
by our custom script. Let's add this aforementioned PHP code into `functions.php`, leaving us with the following:

```
wp_register_script(
  'codechewing_is_the_best',
  get_stylesheet_directory_uri() . '/js/script.js',
  array(), '1.0.0', true
);

wp_localize_script(
  'codechewing_is_the_best',
  'globalObject',
  array(
    'homeUrl' => esc_url(home_url())
  )
);

wp_enqueue_script('codechewing_is_the_best');
```

It's important to know that `wp_localize_script` must be called **after** the registration of the script with
`wp_register_script`.

`wp_localize_script` arguments breakdown:

1. `codechewing_is_the_best` is the handle. This is the same handle as the script registration. This is what
forges the relationship between the script and this JavaScript object that we're able to create
2. `globalObject` is the name of the object that will be popped onto the `window` object. In other words,
this will equate to `window.globalObject`
3. `array('url' => esc_url(home_url())` adds a property of `url` to `globalObject`. You'll end up with a
similar looking JavaScript object:

```
var globalObject = {
  url: 'http://www.codechewing.com/library'
};
```

This is output into the DOM within a separate script tag. It'll appear something like this (which gets added
**before** the `script.js` file):

<pre><code class="html"><script>
var globalObject = {
  url: 'http://www.codechewing.com/library'
};
</script>
</code></pre>

Within our JavaScript file, we can now reference the above object (which will be available before this script
in the page), to dynamically retrieve the WP home URL:

```
if (somethingIsTrue) {
  location.href = globalObject.url + '/some-page';
}
```

This will now work wherever your WordPress site resides. It's always relative to the WP home URL. This is far
more flexible, future proof, and straight forward to implement. A big improvement over hardcoding your site URL
into the JavaScript.

### Farewell

For more information on `wp_localize_script`, see the
[WP docs](https://codex.wordpress.org/Function_Reference/wp_localize_script)(rel="nofollow noopener noreferrer" target="_blank").

When outputting the URL to a page using PHP, always use the `esc_url` method as seen above. It sanitizes the string.
See the
[WP docs](https://codex.wordpress.org/Function_Reference/esc_url)(rel="nofollow noopener noreferrer" target="_blank").
