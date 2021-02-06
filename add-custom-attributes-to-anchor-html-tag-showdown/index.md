# Add custom HTML attributes to anchor tag with Showdown extension

Whilst converting markdown to HTML, using the
[Showdown](https://github.com/showdownjs/showdown)(rel="nofollow noopener noreferrer" target="_blank")
JavaScript library, there inevitably comes a moment where the standard syntax falls short of a few additional tricks
up its sleeve. 

Once you've hit this proverbial wall, Showdown offers a way to easily extend its converter to fulfill extra requirements.
And that's what we're going to explore in this article. 

## The standard ways to compose links in markdown

Here are the inline style links:

```
[Example](https://example.org)
-> <a href="https://example.org">Example</a>

[Example with title](https://example.org "A title")
-> <a href="https://example.org" title="A title">Example</a>
```

There's also what's called a reference style link. This type provides you with an opportunity to declare
links ahead of time, say at the top of the markdown file:

```
[link1]: https://example.org  "A title"
[link2]: https://example.org  'A title'
[link3]: https://example.org  (A title)
[link4]: https://example.org

[Link 1][link1]
-> <a href="https://example.org" title="A title">Link 1</a>

[Link 2][link2]
-> <a href="https://example.org" title="A title">Link 2</a>

[Link 3][link3]
-> <a href="https://example.org" title="A title">Link 3</a>

[Link 4] [link4]
-> <a href="https://example.org">Link 4</a>
```

Also, notice the space between the square brackets on the link 4 render declaration (`[Link 4] [link4]`). This is valid
syntax for this reference style link. Something to keep in mind.

However, for the purpose of this article, we'll only account for **inline style** markdown links, as it becomes
more complicated adding custom HTML attributes to reference style markdown links. Perhaps something for another time.

Read more about the
[standard link syntax](http://daringfireball.net/projects/markdown/syntax#link)(rel="nofollow noopener noreferrer" target="_blank")
if I've whet your appetite.

## Create custom attribute extension

With the default standards recognised, it's time to create a Showdown extension that can enable us to add custom
attributes to an inline style link. Such as extending an anchor tag with an attributes such as `rel="nofollow"` and/or
`target="_blank"`. 

Here's the proposed syntax with desired outcomes:

```
[Example](https://example.org)(rel="nofollow")
-> <a href="https://example.org" rel="nofollow">Example</a>

[Example with title](https://example.org "A title")(rel="nofollow")
-> <a href="https://example.org" title="A title" rel="nofollow">Example</a>

[Example](https://example.org)(rel="nofollow" class="btn btn-primary")
-> <a href="https://example.org" rel="nofollow" class="btn btn-primary">Example</a>
```

To do this with Showdown, we need to register a custom extension with the library.

With node.js or in browser, this implementation should be the same. Install Showdown by whatever means and include
it before the following JavaScript:

<pre><code class="javascript">// Some sample text for us to work with
var text = `
# Example inline style link with custom href attributes

[No title or custom attributes](https://example.org)

[Title & no custom attribute](https://example.org "A title")

[No title but custom attribute](https://example.org)(rel="nofollow")

[Title & custom attributes](https://example.org "A title")(rel="nofollow" class="btn btn-primary")
`;

// Our custom extension
var anchorAttributes = {
  type: 'output',
  regex: /(<a.+<\/a>)\((.+=".+" ?)+\)/g,
  replace: (match, $1, $2) => {
    return $1.replace('">', `" ${$2}>`);
  }
};

showdown.extension('anchorAttributes', anchorAttributes);
var converter = new showdown.Converter({ extensions: ['anchorAttributes'] });

console.log(converter.makeHtml(text));</code></pre>

### Breaking down the above extension code

With ES6 template literals, we create the sample text to work with.

The Showdown extension object contains three properties; `type`, `regex` & `replace`. At the time of writing, 
Showdown offer two sub-extension types, namely `lang` and `output`. 

The `lang` sub-extension adds new markdown syntax. To source the text verbatim from Showdown's documentation:

<blockquote>
  <p>
    For example, say you wanted ^^youtube http://www.youtube.com/watch?v=abc123 to automatically
    render as an embedded YouTube video, that would be a language extension.
  </p>
  <footer>
    <cite>
      <a href="https://github.com/showdownjs/showdown/wiki/Extensions#type-propertyrequired" rel="nofollow noopener noreferrer" target="_blank">
        Showdown documentation
      </a>
    </cite>
  </footer>
</blockquote>

The `output` sub-extension is an opportunity to modify the converted HTML provided by Showdown. This is the
sub-extension type that we use. We want to analyse the converted HTML and adjust it to include our custom
anchor attributes.

The `regex` property contains the regular expression to match our proffered pattern.

When Showdown converts:
```
[No title but custom attribute](https://example.org)(rel="nofollow")
```

...it leaves us with:
```
<a href="https://example.org">No title but custom attribute</a>(rel="nofollow")
```
When our **output type** extension is run, this is the state of the code that we'll analyse against our
regular expression.

Our regex `/(<a.+<\/a>)\((.+=".+" ?)+\)/g` matches the above output. 

The first capture group matches the anchor
link itself (`<a href="https://example.org">No title but custom attribute</a>`), and the second capture group matches
our custom syntax (`rel="nofollow"`), excluding the surrounding parentheses. It's designed to allow more than one
custom attribute, separated by a space. In other words, it'll also pick up multiple attributes, such as
`(rel="nofollow" target="_blank")`, etc.

Superb. We've matched the anchor and the 'extras' we'd like to associate with the link, now what?

The `replace` property accepts a string or function. A string replacement is great for a straight forward
substitution. The function approach has the same signature as the JavaScript
[string replace](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace)(rel="nofollow noopener noreferrer" target="_blank")
function. 

```
replace: (match, $1, $2) => {
  return $1.replace('">', `" ${$2}>`);
}
```
Above, we replace the closing part of the captured anchor link with our custom attributes, then close the anchor
tag ourselves. We've used ES6 template literals again here. 

```
showdown.extension('anchorAttributes', anchorAttributes);
var converter = new showdown.Converter({ extensions: ['anchorAttributes'] });
```

Above, we register our `anchorAttributes` extension with Showdown, then we can convert any text as we normally
would.

### Output from our markdown

```
<h1 id="exampleinlinestylelinkwithcustomhrefattributes">
  Example inline style link with custom href attributes
</h1>
<p>
  <a href="https://example.org">
    No title or custom attributes
  </a>
</p>
<p>
  <a href="https://example.org" title="A title">
    Title &amp; no custom attribute
  </a>
</p>
<p>
  <a href="https://example.org" rel="nofollow">
    No title but custom attribute
  </a>
</p>
<p>
  <a href="https://example.org" title="A title" rel="nofollow" class="btn btn-primary">
    Title &amp; custom attributes
  </a>
</p>
```

As you can see above, our markdown has been successfully converted to contain the custom HTML attributes
specified.

### Farewell

With markdown extensions via the Showdown library, you've seen how expressive markdown can become, with just a little
bit of creativity. Adding custom HTML attributes to an inline style link in markdown was no problem.

It's worth noting that markdown does support HTML, as is. Meaning, if you want custom HTML attributes for your inline
links, you can just as easily include the following directly in the markdown:

```
<a href="https://example.org" title="A title" rel="nofollow" class="btn btn-primary">
  Title &amp; custom attributes
</a>
```

The choice is yours!
