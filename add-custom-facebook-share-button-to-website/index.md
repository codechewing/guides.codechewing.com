# Add your own custom facebook share button to your website

There are many third party solutions, that offer ways to quickly embed share menus and widgets
into your website. Services such as [AddThis](http://www.addthis.com)(rel="nofollow noopener noreferrer" target="_blank"), 
even go as far as providing you with analytics. But all this can be excessive (and may take a hit
on your page performance), if all you really want is for users to be able to easily share your article on their
chosen social media platform.

We're basically going to do the facebook part of this:

<img src="/assets/images/posts/facebook-share-button/facebook-share-button.png" alt="Share menu with Facebook button" width="60" class="img-thumbnail">

If you're interested in creating your own twitter sharer too, check out our
[twitter share](/add-custom-twitter-share-button-to-website) article as well.

## Sample facebook share window

From what I've perceived, the popular share plugins out there, simply open a new window when you click to share from one of the icons. We
can do this ourselves with the `window.open` JavaScript API. This is an example of what we'll generate ourselves:

<img src="/assets/images/posts/facebook-share-button/facebook-share-window.png" alt="Facebook share window" class="img-thumbnail">

By the way, Facebook document the usage of their newer
[share dialog](https://developers.facebook.com/docs/sharing/reference/share-dialog#redirect)(rel="nofollow noopener noreferrer" target="_blank")
API to share pages (which you need an app id for). However, we're going to use the established `sharer.php` endpoint,
still widely in use.

### Creating the custom facebook share button

There are [multiple options](http://stackoverflow.com/questions/20956229/has-facebook-sharer-php-changed-to-no-longer-accept-detailed-parameters#answer-40319441)(rel="nofollow noopener noreferrer" target="_blank")
to customise when crafting your facebook share content, and below are the ones we know about:

- `u` - URL
- `picture` - custom image
- `title` - custom title
- `quote` - custom quote
- `description` - custom description

If you don't provide some of the above, facebook will attempt to scrape the page you're intending to share,
and auto-populate the fields it can.

In this article, we'll demonstrate usage with only some of these options.

Let's assume you've got the following HTML, with some nice styling going on to render an icon:

```
<a href="#" class="facebook-share">Share on facebook</a>
```

As mentioned earlier, we're going to use `window.open`. This method is well
[documented on MDN](https://developer.mozilla.org/en-US/docs/Web/API/Window/open)(rel="nofollow noopener noreferrer" target="_blank"),
and is definitely worth reading up on if you have a moment.

The first parameter of `window.open` expects the URL (`{String}`). The second, a name (`{String}`) for the window
(which can be used to reference it later). Lastly, a third parameter of window features/options (`{String}` of
comma separated values).

To generate the options, we'll create a helper method called `getWindowOptions`. Which goes like this:

```
var getWindowOptions = function() {
  var width = 500;
  var height = 450;
  var left = (window.innerWidth / 2) - (width / 2);
  var top = (window.innerHeight / 2) - (height / 2);

  return [
    'resizable,scrollbars,status',
    'height=' + height,
    'width=' + width,
    'left=' + left,
    'top=' + top,
  ].join();
};
```

We've chosen an arbitrary window size of `500x450` here. You might want to be smarter than this, based upon the
available screen size. 

We then centre the window by setting the `top` and `bottom` positions relative to the parent window size.

All our custom options get put together into a comma separated list, with some other self explanatory options:
`resizable,scrollbars,status`

Now to make this button open the facebook share window, with another sprinkle of JavaScript:

```
var fbBtn = document.querySelector('.facebook-share');
var title = encodeURIComponent('Hey everyone, come & see how good I look!');
var shareUrl = 'https://www.facebook.com/sharer/sharer.php?u=' + location.href + '&title=' + title;
fbBtn.href = shareUrl; // 1

fbBtn.addEventListener('click', function(e) {
  e.preventDefault();
  var win = window.open(shareUrl, 'ShareOnFb', getWindowOptions());
  win.opener = null; // 2
});
```

The above will listen for clicks on the facebook share button, and open up a new sharing window. The share content will
be populated with the URL of your page, and some text.

Two special pieces of the code that are worth further discussion:

1. We set the `href` of the anchor, so that it's in sync with the dynamic window that'll be lauched when
someone clicks the share button. If the popup gets blocked, this may provide a fallback mechanism.
2. We set `win.opener` to `null`. This results in the child window (third party, untrustworthy),
to no longer have a reference to our parent window. This severed relationship closes down a possible security
vulnerability. 

### Farewell

You've learnt that it's actually pretty straight forward to launch a facebook share window yourself. So if you don't
need some elaborate share bar, but want some selective control and performance gains, this
could be the better implementation choice.
