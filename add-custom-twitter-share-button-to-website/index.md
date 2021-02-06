# Add your own custom twitter share button to your website

There are many third party solutions, that offer ways to quickly embed share menus and widgets
into your website. Services such as [AddThis](http://www.addthis.com)(rel="nofollow noopener noreferrer" target="_blank"), 
even go as far as providing you with analytics. But all this can be extravagant (and may take a hit
on your page performance), if all you really want is for users to be able to easily share your article on their
chosen social media platform.

We're basically going to do the twitter part of this:

<img src="/assets/images/posts/twitter-share-button/twitter-share-button.png" alt="Share menu with Twitter button" width="60" class="img-thumbnail">

If you're interested in creating your own facebook sharer too, check out our
[facebook share](/add-custom-facebook-share-button-to-website) article as well.

## Sample twitter share window

From what I've observed, the popular share plugins out there, simply open a new window when you click to share from one of the icons. We
can do this ourselves with `window.open`. This is an example of what we'll generate ourselves:

<img src="/assets/images/posts/twitter-share-button/twitter-share-window.png" alt="Twitter share window" class="img-thumbnail">

You'll find that if you use Twitter's own 
[widget JS](https://dev.twitter.com/web/javascript/loading)(rel="nofollow noopener noreferrer" target="_blank"),
it'll dynamically add multiple `iframes` to your HTML, one of which contains the tweet button, the other, some tracking
of sorts, I suspect, that you probably don't need...

### Creating the custom twitter share button

There are [multiple options](https://dev.twitter.com/web/tweet-button#tweet-text-components)(rel="nofollow noopener noreferrer" target="_blank")
when crafting your tweet content, but we'll just demonstrate pre-loading the tweet
with the URL and some custom text.

Let's assume you've got the following HTML, with some nice styling going on to render an icon:

```
<a href="#" class="twitter-share">Tweet</a>
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
  var height = 350;
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

We've chosen an arbitrary window size of `500x350` here. You might want to be smarter than this, based upon the
available screen size. 

We then centre the window by setting the `top` and `bottom` positions relative to the parent window size.

All our custom options get put together into a comma separated list, with some other self explanatory options:
`resizable,scrollbars,status`

Now to make this button open the twitter share window, with another sprinkle of JavaScript:

```
var twitterBtn = document.querySelector('.twitter-share');
var text = encodeURIComponent('Hey everyone, come & see how good I look!');
var shareUrl = 'https://twitter.com/intent/tweet?url=' + location.href + '&text=' + text;
twitterBtn.href = shareUrl; // 1

twitterBtn.addEventListener('click', function(e) {
  e.preventDefault();
  var win = window.open(shareUrl, 'ShareOnTwitter', getWindowOptions());
  win.opener = null; // 2
});
```

The above will listen for clicks on the twitter share button, and open up a new sharing window. The tweet will
be populated with the URL of your page, and some text.

Two special pieces of the code that are worth further discussion:

1. We set the `href` of the anchor, so that it's in sync with the dynamic window that'll be lauched when
someone clicks the share button. If the popup gets blocked, this may provide a fallback mechanism.
2. We set `win.opener` to `null`. This results in the child window (third party, untrustworthy),
to no longer have a reference to our parent window. This severed relationship closes down a possible security
vulnerability. 

### Farewell

You've learnt that it's actually pretty straight forward to launch a twitter share window yourself. So if you don't
need some elaborate share bar, but want some selective control and performance gains, this could be the better
implementation choice.
