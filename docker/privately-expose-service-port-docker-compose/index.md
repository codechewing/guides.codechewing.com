# Privately expose a service / container port with docker compose

When using docker compose, there are multiple ways to expose / publish a service / container port
so that another container can access the service. Depending on the service, you might only want
the published port to remain inside the private docker network, and **not** exposed to the internet.

For example, perhaps you've got a MySQL database running inside a container - you probably don't
want to expose the default port (3306) to the world wide web, right? But, you do want to expose
it to the other containers within the docker network. On the flip side of this, your web application
running the website will rightly want to expose itself to the <abbr title="World Wide Web">www</abbr>.

## Setting up the public container

Let's suppose you've already got your `Dockerfile` in place for your **public container** build... be it
some kind of web application... it doesn't really matter. Your docker compose file (`docker-compose.yml`)
would look something like this:

<pre><code class="yaml">version: '2'

networks:
  privatenetwork:

services:
  site:
    build: .
    ports:
      - "80:80"
      - "443:443"
    networks:
      - privatenetwork
</code></pre>

Notice that we use the `ports` property to define the `HOST:CONTAINER` port pairing, which will publicly
publish both port 80 and port 443 to the host machine, and thus the internet. Anyone can access these ports.

For the version 2 docs on `ports`, see
[here](https://docs.docker.com/compose/compose-file/compose-file-v2/#ports)(rel="nofollow noopener noreferrer" target="_blank")

## Setting up the private container

Again, let's suppose you've already got your `Dockerfile` in place for your **private container** build...
We'll extend the previous docker compose file (`docker-compose.yml`) so that it resembles something like this:

<pre><code class="yaml">version: '2'

networks:
  privatenetwork:

services:
  site:
    build: .
    ports:
      - "80:80"
      - "443:443"
    networks:
      - privatenetwork

  mysql:
    build: ./docker/mysql
    expose:
      - "3306"
    networks:
      - privatenetwork
</code></pre>

For the MySQL container, we **don't** use the `ports` property this time. Rather, we use the `expose` property.

The difference is inconspicuous, but paramount. By doing it this way, we don't publish the MySQL container port
to the host machine. This means that it'll reside within the private docker network, inaccessible from users
on the internet (subject to firewall settings of course...).

For the version 2 docs on `expose`, see
[here](https://docs.docker.com/compose/compose-file/compose-file-v2/#expose)(rel="nofollow noopener noreferrer" target="_blank")

## Port binding proof

If you're interested in observing the port bindings, you can run `docker-compose ps`:

```
$ docker-compose ps
   Name                Command                State                    Ports                   
----------------------------------------------------------------------------------------------------------
app_mysql_1   docker-entrypoint.sh mysqld      Up      3306/tcp                                 
app_site_1    docker-php-entrypoint apac ...   Up      0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp 
```

In the above, you can see that port `443` & `80` have bound to the host interface (`0.0.0.0`).
Whereas, the MySQL container does not have a public binding to the host. It is bound to a private,
random port that the docker network has reference to, enabling the docker compose magic which links
containers / services together.

### Farewell

Don't accidentally publicly surface a private docker container, you might end up getting unintentionally hacked!
You now know the fundamental differences between publicly exposing a docker container port with `ports`, and
privately publishing a docker container port with `expose`.
