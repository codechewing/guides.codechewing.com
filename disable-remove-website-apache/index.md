# Disable & remove virtual host website entry in Apache

If your Apache 2 server is no longer hosting up a particular website, you can disable a virtual host
entry on your server.

## Prep

SSH into your server:

```
$ ssh user@server
```

You can list the currently enabled websites if you want to check the name:

```
$ ls /etc/apache2/sites-enabled
000-default.conf  my-website.conf
```

## Disable virtual host entry

Let's assume we'd like to disable `my-website.conf`. To disable this virtual hosts entry, we issue the
following command to Apache (note the exclusion of the `.conf` extension from the virtual host name):

```
$ sudo a2dissite my-website
```

You can confirm this has disabled the website by checking that the site is no longer listed in
the enabled sites directory:

```
$ ls /etc/apache2/sites-enabled
000-default.conf
```

You'll then need to reload Apache for the changes to take effect:

```
$ sudo service apache2 reload
```

## Remove virtual host entry

Wanna go the whole nine yards?

If you no longer need the virtual host configuration file, remove that too. First double check what it's called
by listing out the **available** websites:

```
$ ls /etc/apache2/sites-available
000-default.conf  my-website.conf
```

Note that this is a different location to `/etc/apache2/sites-enabled`, from earlier.

Remove the config file you no longer need:

```
$ rm /etc/apache2/sites-available/my-website.conf
```

You may also want to backup then remove any website files residing in the `/var/www` directory, for example:

```
$ cd /var/www
$ tar czf my-website.tar.gz my-website
$ rm -r my-website
```

Finish up by copying the backup to somewhere safe and then go ahead and delete it from the server to
free up some space.

### Farewell

A bit of Apache spring cleaning can free up some server space and reduce some configuration overhead.
