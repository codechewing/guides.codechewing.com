# How to create & reuse a HTML template with Bash

Situation:

You're a web developer and the pages within the project you're working on require the same initial HTML or PHP
template for every file, and you're sick and tired of opening up an existing file and clicking the Save As
option from there. Maybe you have to delete some content as well before you can even get to your starting point? Pain.

Instead, let's be smarter and utilise a neat Bash script to do the hard work for us. All it requires is a straight
forward Bash script along with some linux commands from within the terminal. Then all you've have to do in the future
is type this into the terminal and you've have a new custom file with the template contents inside of it:

<pre><code class="shell">$ ./html_template > new-file-with-template.html</code></pre>

## Setting up the bash script

First, open your IDE of choice. We're going to create the HTML template in
Bash. It's not scary, honest! Give it a try.

Here's a very basic HTML template for this example, which we'll type into the IDE (You can go nuts from here,
but it's just a basic example):

```
#!/bin/bash

# Simple automated HTML template

cat << _EOF_
<!doctype html>
<html>
<head>
    <title>HTML Template example</title>
</head>

<body>

</body>

</html>
_EOF_
```

We've just wrote a Bash script! Some bits explained:

- `#!/bin/bash` - This is **not a comment**, this tells the shell where Bash lives so it can successfully
interpret the file.
- As you may have expected, everything inside of `cat << _EOF_` and `_EOF_` is outputted. It acts as an echo.
You can actually name the <kbd>EOF</kbd> anything you want, but `EOF` (End Of File) is the convention.
- Save this file inside your root website directory and name it `html_template.sh`

Now it's time for some cool linux commands inside the terminal! Open up terminal and cd into the same website
directory as your new Bash script. For me, it's here:

```
$ cd Documents/website
```

Now we're inside, check your file is there, using the list files with `-l` flag to provide a detailed view:

```
$ ls -l
drwxrwsr-x  62 pjl  _appstore   2108  6 Aug 17:55 .
drwxrwsr-x   6 pjl  _appstore    204  6 Aug 15:49 ..
-rw-r--r--   1 pjl  _appstore   4186 19 Jul 20:53 html_template.sh
```

Here you can see our Bash script - `html_template.sh`. The problem is, the file permissions do not allow for it to be
executed; `-rw-r--r--`. We need `-rwxr-x-r-x` for it to executable.

Let's change this using the `chmod` command, where we pass `755` to get the permissions we require:
```
$ chmod 755 html_template.sh
```

Check your file permissions have been updated by running the `ls -l` command for a detailed view again:

```
$ ls -l
drwxrwsr-x  62 pjl  _appstore   2108  6 Aug 17:55 .
drwxrwsr-x   6 pjl  _appstore    204  6 Aug 15:49 ..
-rwxr-xr-x   1 pjl  _appstore   4186 19 Jul 20:53 html_template.sh
```

As you can see - our file permissions have successfully been updated! Now the script can run! Excellent.

### Running the script

It's time to run the script. We need to trigger the script and pass it a file name that it creates, or overwrites
(with the HTML template) upon execution. It's simple - my new file will be called `example.html`. So here goes:

```
$ ./html_template.sh > example.html
```

Now let's check `example.html` has been created:

```
$ ls -l
drwxrwsr-x  62 pjl  _appstore   2108  6 Aug 17:55 .
drwxrwsr-x   6 pjl  _appstore    204  6 Aug 15:49 ..
-rw-r--r--   1 pjl  _appstore   3906 19 Jul 20:53 example.html
-rwxr-xr-x   1 pjl  _appstore   4186 19 Jul 20:53 html_template.sh
```

And there it is! `example.html` just got created!

### Farewell

The new file has adopted our HTML template! Awesome! And that's how you easily create HTML templates and trigger
them with Bash! If your template ever needs updating, you can just go into the bash script and update it there.
Then any new files created via this method will include the updated template.
