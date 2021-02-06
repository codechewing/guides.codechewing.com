# How to run a command inside a docker container

If you've built and launched a docker image, you might be left wondering what's
actually going on within the container? What does the file system look like?
Does that package exist?

Perhaps you want to check some configuration setting, or check to see if a module
or particular service is enabled and running... well there's a way.

## Get the docker container id

If you don't know the docker image name or container id, begin by listing the
running docker containers:

```
$ docker ps
CONTAINER ID  IMAGE         COMMAND         CREATED         STATUS         PORTS                         NAMES
7015544736d9  fa21f0188248  "docker-ph..."  11 seconds ago  Up 10 seconds  80/tcp, 0.0.0.0:443->443/tcp  codechewing
```

## Get into the container

With the container name or id at hand, we can now get into the container, using the bash shell.

```
$ docker exec -it 7015544736d9 bash
root@7015544736d9:/var/www#
```

You're in! It feels just like SSH'ing into a server...

We provided the `exec` command with two options:

- `t` - instructs docker to use a pseudo tty/terminal
- `i` - interactive, to keep STDIN open

Read more at the 
[exec docs](https://docs.docker.com/engine/reference/commandline/exec)(rel="nofollow noopener noreferrer" target="_blank").

### Farewell

Now you can explore within the running docker container, to check what's installed,
where files live, and more!
