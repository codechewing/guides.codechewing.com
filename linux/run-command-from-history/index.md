# How to run a command from history - Linux

Sometimes `!!` to execute the last command (albeit brilliantly convenient), isn't what you want. Instead, this is
how to list all your recent history commands and cherry picking the command you want to execute again:

```
$ history
  369  git pull origin master
  370  sudo docker-compose build
  371  sudo docker-compose stop
  372  sudo docker-compose up -d
  373  sudo docker-compose ps
  374  sudo docker-compose stop
  375  sudo docker-compose up -d
  376  exit
```

Suppose I want to run the command located at `370` again:

```
$ !370
  sudo docker-compose build
  ...
```

`!` followed by the number in history. Easy.

## History pagination

Your linux command history can sometimes get overlong, and throwing this all into the terminal will be illegible.
There's a neat trick, whereby you pipe the standard output into `less`, and then you can page through the history
listing with ease:

```
$ history | less
```

### Farewell

There's also `Ctrl` + `r` as well, to perform a text search on your history - you have an opportunity to edit
the command or run it verbatim. 
