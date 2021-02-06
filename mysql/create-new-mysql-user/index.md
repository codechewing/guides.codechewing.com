# Create a new MySQL user with all permissions

It's good practice to disable root access to your databases, and instead add a new user who contains the
necessary permissions and privileges required for their usage.

We'll also ensure that this new user has **grant privileges** too - which means they will be able to add new
users and grant those users with specific privileges.

You can run these commands via the **command line**, if you have the MySQL command line tool. Or if you're using
phpMyAdmin, you can run them in the SQL command box.

Here's the SQL command to first add the new user:

```
CREATE USER 'username'@'localhost' IDENTIFIED BY 'somePassword';
```

Now add all the privileges to this new user, including the ability to grant access:

```
GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost' WITH GRANT OPTION;
```

`*.*` means we're granting these privileges to any database and any table on any database. Be sure this is
definitely what you want to do.

Finally, we must reset the privileges for it to work:

```
FLUSH PRIVILEGES;
```

## Database specific MySQL user

If you want a user to have permission for only one database, in order to lock them down for security measures
in case of a compromise, you could do this:

```
CREATE USER 'username2'@'localhost' IDENTIFIED BY 'somePassword';
GRANT ALL PRIVILEGES ON database_name.* TO 'username2'@'localhost';
FLUSH PRIVILEGES;
```

This user will have all privileges for the `database_name` database only.

### Farewell

If access to the database is publicly accessible, it's worth removing the default `root` user, as mentioned at the
start of this guide, and replacing them with an equally privileged user. This at least removes a well-known username,
which an attacker could try and perform brute force access with.
