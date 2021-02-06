# Conditionally assign value to a variable in bash

Goal: A bash script that accepts one argument. If the argument isn't provided, then set a default value.

This is the kind of conditional assignment I wanted to *avoid*:

```
#!/bin/bash

name=default
if [ "$1" ]; then
  name="$1"
fi

echo $name
```

There's nothing bad with this, I just felt it was a more verbose outcome than what I had in mind. I wanted
something that more closely resembled a ternary operator, which leads us nicely onto the following variations...

## Variation #1

```
#!/bin/bash

[ "$1" ] && name="$1" || name=default

echo $name
```

## Variation #2

```
#!/bin/bash

name=$([ "$1" ] && echo "$1" || echo default)

echo $name
```

## Using the conditional assignment script

Save one of the variations into a bash script file, say `script.sh`.

Give it executable permissions:

```
$ chmod +x script.sh
```

Run with or without an argument:

```
$ ./script.sh Dave
Dave
$ ./script.sh
default
```

### Farewell

Here's a comprehensive reference of
[primary expressions](https://www.tldp.org/LDP/Bash-Beginners-Guide/html/Bash-Beginners-Guide.html#tab_07_01)(rel="nofollow noopener noreferrer" target="_blank").

I've used the following expression in my example:

```
[ -n STRING ] or [ STRING ]
True if the length of "STRING" is non-zero.
```

You can see that `-n` can be omitted, as it's the default.

In other words, I could have written the following instead:

```
[ -n "$1" ] && name="$1" || name=default
```

### Contributions

Special thanks goes to the comment from [BUFU](#comment-4227839548),
who helped me improve the expression choice here.
