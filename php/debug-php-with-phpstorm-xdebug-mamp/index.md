# How to debug PHP with PHPStorm & MAMP

I struggled immensely one evening to find a tutorial out there that worked for me, on how to **debug**
PHP applications running over **MAMP**, using the **PHP Storm** IDE. After absorbing bits and pieces from other
guides, what really made it click for me, was reading the official
[xdebug documentation](https://xdebug.org/docs/remote#communication)(rel="nofollow noopener noreferrer" target="_blank"),
where they provide a diagram of how the debugging components communicate, and what some of the xdebug settings mean.

To point out - it doesn't really matter that the PHP server in this scenario is MAMP, this configuration will work for
any 'remote' PHP server. I put *remote* within inverted commas, because the server is actually running on my local
machine, but is considered remote for the implementation.

What I had going on as my dev environment (but don't let the exact details put you off, as the flow should be the same):

- MAMP 3.5, free version, running PHP 5.6.10 (did someone say PHP 7 was out?)
- Mac 10.10.5 (OS X Yosemite)
- PHPStorm (tested on 7.1.3 and 8.0.3)

Without further ado, let's begin this winning guide, which by the way, was surprisingly straight forward to get working.

## Setting MAMP up with xdebug

Xdebug is the life and soul of this debugging party - it's a Zend Engine PHP extension that enables the debugging of
PHP, as well as PHP profiling and other useful things.

Luckily for us, MAMP already comes with the xdebug extension out of the box, so we don't need to bother installing it
ourselves, we just need to make sure it's enabled. We also need to ensure some xdebug settings are configured correctly.
Let's do both of these tasks at the same time!

Find out what version of PHP MAMP is running (<kbd>Preferences > PHP</kbd>):

<img
    src="/assets/images/posts/php/debug-php-with-phpstorm-xdebug-mamp/mamp-php-version.png"
    alt="mamp php version"
    class="img-thumbnail">

As mentioned before, that's version 5.6.10 for me. It's important that we know what version is running, because we need
to edit the active `php.ini` config file. The PHP config file I'll be editing is at
`/Applications/MAMP/bin/php/php5.6.10/conf/php.ini`.

Open up `php.ini` for editing, and search for `xdebug`. There should be a setting that includes the xdebug Zend
Extension, but it may be commented out. Ensure it's uncommented, and also add some xdebug settings, giving you
something that looks like this:

<pre><code class="ini">[xdebug]
zend_extension="/Applications/MAMP/bin/php/php5.6.10/lib/php/extensions/no-debug-non-zts-20131226/xdebug.so"
xdebug.remote_enable=1
xdebug.remote_host=127.0.0.1
xdebug.remote_port=9000
xdebug.idekey=PHPSTORM</code></pre>

Breakdown of PHP config settings:

1. `zend_extension` - make sure this value is the file path that points to the relevant xdebug module running for
your PHP version in MAMP
2. `xdebug.remote_enable=1` - we are going to be setting up a remote debugging session, so make sure it's enabled
3. `xdebug.remote_host=127.0.0.1` - **Xdebug acts as the client**, and the
**IDE (PHPStorm) is going to act as the server**. This means that the remote host of the server is where the
IDE is running, which will (probably) be localhost for you, i.e. the same machine
4. `xdebug.remote_port=9000` - PHPStorm, i.e. the server, listens for xdebug debugging on **port 9000** as default,
so unless you've got a good reason to change it, keep it as 9000
5. `xdebug.idekey=PHPSTORM` - This sets our debugging session key to `PHPSTORM`. In theory, it can be named anything.
Xdebug remote debugging uses session cookies to identify that a debugging session is in play. I'll discuss this a
bit more later on

Some of these above settings may already be the default for MAMP. So you might not need to explicitly set them all.

Restart MAMP servers, so that the `php.ini` changes are applied, and visit the php info page
(<kbd>http://127.0.0.1:8888/MAMP/</kbd>) to check your new configuration is live. Here were some screenshots
of my applied settings:

<img
    src="/assets/images/posts/php/debug-php-with-phpstorm-xdebug-mamp/zend-engine-xdebug-extension.png"
    alt="zend engine xdebug extension"
    class="img-thumbnail">

<img
    src="/assets/images/posts/php/debug-php-with-phpstorm-xdebug-mamp/xdebug-config-settings.png"
    alt="xdebug config settings"
    class="img-thumbnail">
    
You can also check if the Zend Engine xdebug extension is installed via the command line:

```
$ /Applications/MAMP/bin/php/php5.6.10/bin/php -v
  PHP 5.6.10 (cli) (built: Jul  6 2015 14:28:54) 
  Copyright (c) 1997-2015 The PHP Group
  Zend Engine v2.6.0, Copyright (c) 1998-2015 Zend Technologies
  with Xdebug v2.2.7, Copyright (c) 2002-2015, by Derick Rethans
```

If you're satisfied that the MAMP configuration is correctly setup, move onto the next stage - setting up PHPStorm.

## Setting up PHPStorm

With MAMP out of the way, it's now time to configure some options in PHPStorm.

### Step 1

Remember that PHPStorm is acting as the server for xdebug, and we configured xdebug (the client) to knock on the door
of port 9000. Let's first of all make sure that PHPStorm is setup to listen for incoming connections on port 9000.

1. <kbd>File > Default Settings</kbd>
2. Use the search field to type in <kbd>debug</kbd>
3. Focus in on the menu <kbd>PHP > Debug</kbd>
4. Ensure that the Xdebug section is listening on port 9000

Here's a screenshot of my settings:

<img
    src="/assets/images/posts/php/debug-php-with-phpstorm-xdebug-mamp/phpstorm-xdebug-settings.png"
    alt="phpstorm xdebug settings"
    class="img-thumbnail">

### Step 2

Prepare PHPStorm, by telling it to begin listening for PHP debug connections. This is simply (but vitally) achieved by
pressing a button in the IDE. Make sure you've got <kbd>View > Toolbar</kbd> showing, and look for the button that resembles a
(proper old school) phone.

Here are some screenshots to guide you:

<img
    src="/assets/images/posts/php/debug-php-with-phpstorm-xdebug-mamp/phpstorm-debug-listen-off.png"
    alt="phpstorm debug listen off"
    class="img-thumbnail">

<img
    src="/assets/images/posts/php/debug-php-with-phpstorm-xdebug-mamp/phpstorm-debug-listen-tooltip.png"
    alt="phpstorm debug listen tooltip"
    class="img-thumbnail">

<img
    src="/assets/images/posts/php/debug-php-with-phpstorm-xdebug-mamp/phpstorm-debug-listen-on.png"
    alt="phpstorm debug listen on"
    class="img-thumbnail">

Thanks to some
[JetBrains documentation](https://confluence.jetbrains.com/display/PhpStorm/Zero-configuration+Web+Application+Debugging+with+Xdebug+and+PhpStorm)(rel="nofollow noopener noreferrer" target="_blank")
for that one.

### Step 3

Lastly, PHPStorm needs to be aware of our remote server (MAMP) that's actually interpreting the PHP scripts.
We're going to point PHPStorm in the right direction.

Here's a list of instructions, but see the screenshots below for further clarity.

1. Edit a debug configuration, <kbd>Run > Edit Configurations</kbd>
2. Press the plus icon
3. From the list, choose the option **PHP Remote Debug**
4. Optionally give it a name
5. Within the Configuration section, next to the Servers input, click on the ellipsis (&#8230;) to add a new server
6. When the new window pops up, click the plus icon to add a new server (see below screenshots for details)
7. Give it a name, such as <kbd>MAMP</kbd>. Set the host to <kbd>127.0.0.1</kbd>. Set the Port to <kdb>8888</kbd>
(or whatever your MAMP server is configured to listen to). Make sure Xdebug is selected from the menu. Click OK
8. Now the server window has closed, you're back to the PHP Remote Debug window.
9. Enter your IDE key, matching the same one as set inside of the `php.ini` config. That's <kbd>PHPSTORM</kbd> for us
10.  Click OK. You're done!

<img
    src="/assets/images/posts/php/debug-php-with-phpstorm-xdebug-mamp/phpstorm-remote-server-settings.png"
    alt="phpstorm remote server settings"
    class="img-thumbnail">

<img
    src="/assets/images/posts/php/debug-php-with-phpstorm-xdebug-mamp/phpstorm-mamp-server-settings.png"
    alt="phpstorm mamp server settings"
    class="img-thumbnail">

Thanks goes to more
[JetBrains documentation](https://confluence.jetbrains.com/display/PhpStorm/Debugging+PHP+Web+Applications+with+Run+Debug+Configurations)(rel="nofollow noopener noreferrer" target="_blank")
on this.

## Debugging PHP in PHPStorm

Finally, we're getting to the good stuff. Get your debug configuration running, by either making sure it's selected
in the drop menu next to the bug icon, and clicking on the bug icon to activate. Or from the menu -
<kbd>Run > Run</kbd>, then choose the debug configuration you created earlier.
Here's a screen grab of the former method:

<img
    src="/assets/images/posts/php/debug-php-with-phpstorm-xdebug-mamp/phpstorm-run-debug-configuration.png"
    alt="phpstorm run debug configuration"
    class="img-thumbnail">

Upon activation, a debug bar should appear at the bottom of PHPStorm, that says it's waiting for connections
(**Waiting for incoming connection with ide key &#8216;PHPSTORM'**):

<img
    src="/assets/images/posts/php/debug-php-with-phpstorm-xdebug-mamp/remote-debug-session-active.png"
    alt="phpstorm remote debug session active"
    class="img-thumbnail">

To test our example, we've got an `index.php` file sitting in the `htdocs` of our MAMP server,
with the following content:

```
echo 'Code Chewing';
```

Stick a break point on the executing line, like so:

<img
    src="/assets/images/posts/php/debug-php-with-phpstorm-xdebug-mamp/phpstorm-xdebug-breakpoint.png"
    alt="phpstorm xdebug breakpoint"
    class="img-thumbnail">

Open your browser, and navigate to <kbd>http://127.0.0.1:8888/?XDEBUG_SESSION_START=PHPSTORM</kbd>

Your breakpoint should get hit, automatically switching windows to PHPStorm. You'll see something like the following:

<img
    src="/assets/images/posts/php/debug-php-with-phpstorm-xdebug-mamp/phpstorm-xdebug-debugging-with-mamp.png"
    alt="phpstorm xdebug debugging with mamp"
    class="img-thumbnail">

You can see that I'm inspecting the `$_GET` variable, where I can see the `XDEBUG_SESSION_START` key.
This will also exist in `$_COOKIE`.

### What's `XDEBUG_SESSION_START` about?

Xdebug sets a cookie in the browser, which links up with the DBGp protocol, and the rest is beyond my understanding
in all honesty. But it essentially enables the debugging session to ride through the various components to provide
the debugging experience. Go knock yourself out with the
[docs](https://xdebug.org/docs/remote#browser_session)(rel="nofollow noopener noreferrer" target="_blank")
if you fancy it.

The cookie expires after an hour. And if you take a peek at the request headers, say through the browser dev tools,
you'll see a cookie being set:

```
Set-Cookie: XDEBUG_SESSION=PHPSTORM; expires=Fri, 08-Jul-2016 23:01:22 GMT; Max-Age=3600; path=/
```

This means that for the next hour, you don't need to pass the query `XDEBUG_SESSION_START=PHPSTORM` through to the
HTTP request if you don't want to, and the debugging will still be active. Yay for cookies! Meaning that you can hit
<kbd>http://127.0.0.1:8888/</kbd> instead of <kbd>http://127.0.0.1:8888/?XDEBUG_SESSION_START=PHPSTORM</kbd> for
subsequent requests.

<blockquote>
    "But query strings aren't cool!"
</blockquote>

Well, there are some bookmarklets that you can add to the browser which eliminates even the need for manually
entering the query string, and you can generate them here:

[https://www.jetbrains.com/phpstorm/marklets/](https://www.jetbrains.com/phpstorm/marklets/)(rel="nofollow noopener noreferrer" target="_blank")

### Farewell

I hope you've now got to grips on how to debug PHP using MAMP, via xdebug and PHPStorm.
