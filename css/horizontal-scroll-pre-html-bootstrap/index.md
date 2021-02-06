# Horizontal scroll fix for `pre` HTML with Bootstrap CSS

When using Bootstrap CSS and `<pre>`/`<code>` blocks for sample code, I disappointingly discovered
the the code block wasn't horizontally scrolling, and I ended up with a big mess of wrapped code for
smaller screen sizes.

For example, you might have some wide spanning HTML like the following snippet:

```
<pre><code>$ docker-compose ps
   Name                Command                State                    Ports                   
----------------------------------------------------------------------------------------------------------
app_mysql_1   docker-entrypoint.sh mysqld      Up      3306/tcp                                 
app_site_1    docker-php-entrypoint apac ...   Up      0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp 
...</code></pre>
```

And it rendered like this:

<img
    src="/assets/images/posts/css/horizontal-scroll-pre-html-bootstrap/bootstrap-css-pre-wrap-horizontal-scroll.png"
    alt="pre html wrap fix - bootstrap css"
    class="img-thumbnail maxw-300px">
    
Definitely not great for mobile viewers!

## CSS fix

Adding the following CSS fixed the text wrapping issue:

```
pre code {
  white-space: pre;
  overflow-x: auto;
  display: inline-block;
  min-width: 100%;
}
```

The text stays on the line it's supposed to, without wrapping, and the code block 
becomes horizontally scrollable too.

## Fix for highlight js (hljs)

To throw a spanner in the works when I was using highlight JS to style the Bootstrap CSS code
blocks, I found that the above fix wasn't working.

To get around this problem - applying most of the CSS styles above
to the `.hljs` selector, for some specificity battle, triumphed:

```
.hljs {
  overflow-x: auto;
  display: inline-block;
  min-width: 100%;
}
```

### Farewell

Your mobile users will appreciate the non-mangled code, and instead be at peace with the reliable
horizontal scroll, and no wrapping text.
