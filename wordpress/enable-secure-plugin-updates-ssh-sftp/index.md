# How to enable secure WP plugin updates via SSH & SFTP

Since moving away from shared hosting, WP has required FTP authentication whenever I wanted to update or install
plugins. But I've moved away from using the insecure FTP protocol, and FTPS wasn't set up on my server (which was
the only other option). So in the meantime I resulted to manual updates whenever I needed to (this is arduous).

## Using a WP plugin

Turns out, there's a secure way to do it from the WP admin panel. If you've already got SFTP setup on the server
with a user that has SSH access on port 22, you can install a WordPress plugin called
[SSH SFTP Updater Support](https://wordpress.org/plugins/ssh-sftp-updater-support/)(rel="nofollow noopener noreferrer" target="_blank")

Once the plugin is installed, when you're asked to enter your authentication details during an update, enter the server
host IP, SSH username, SSH password and select the ssh2 radio button for connection type. It should just work.

If you want this authentication to become automatic when updating, add these lines to `wp-config.php`
(based on what's above):

```
// SFTP (SSH) details for automatic updates
define( 'FTP_USER', 'user1' );
define( 'FTP_PASS', 'yourPassword' );
define( 'FTP_HOST', '127.0.0.1:22' );
define( 'FS_METHOD', 'ssh2' );
```

I've found that including the `FS_METHOD` has caused me problems in the past. Omitting the `FS_METHOD` works for me
all the time. However, by omitting `FS_METHOD`, the secure `ssh2` method may not get utilised, as WordPress may
default to the `direct` method instead, which won't have the security benefits of SSH. You can read a bit more about it
[here](https://codex.wordpress.org/Editing_wp-config.php#WordPress_Upgrade_Constants)(rel="nofollow noopener noreferrer" target="_blank")

## WP plugin + SSH key access

If you'd prefer to use SSH keys to authenticate your SFTP user, rather than exposing your password inside of
`wp-config.php`, that can be done too. Follow these further instructions to do just that.

You will need:

- SSH access to the server
- `sudo` privileges

SSH into your server:

```
$ ssh user@server
$ Password:
```

Generate your key pair:

```
$ ssh-keygen -t rsa -b 4096
```

When asked where to store your keys, put them inside your home directory. i.e. `/home/user/.ssh/wp_rsa`. Avoid entering
a passphrase (just press enter on both password & confirm).

To keep the keys more secure, ensure the private and public key are owned by user, and have group ownership of
`www-data`, and set secure permissions:

```
$ sudo chown user:www-data /home/user/.ssh/wp_rsa*
$ sudo chmod 640 /home/user/.ssh/wp_rsa*
$ sudo chown user:user /home/user/.ssh
$ sudo chmod 700 /home/user/.ssh
```

Copy across the public key into `authorized_keys`:

```
$ sudo cp /home/user/.ssh/wp_rsa.pub /home/user/.ssh/authorized_keys
```

Update the permissions and ownership of this file to keep it more secure:

```
$ sudo chown user:user /home/user/.ssh/authorized_keys
$ sudo chmod 644 /home/user/.ssh/authorized_keys
```

As these keys will only ever be used internally on the server from within the WP admin panel, we can restrict
the key usage to the local system. Open up the `authorized_keys` file and add `from="127.0.0.1"` to the beginning
of the file:

```
$ sudo vi /home/user/.ssh/authorized_keys
```

So it reads something like:

```
from="127.0.0.1" ssh-rsa AABG43...
```

Once this is done, open up `wp-config.php` and enter your SFTP details:

```
// SFTP (SSH) details for automatic updates
define( 'FTP_PUBKEY', '/home/user/.ssh/wp_rsa.pub' );
define( 'FTP_PRIKEY', '/home/user/.ssh/wp_rsa' );
define( 'FTP_USER', 'user' );
define( 'FTP_PASS', '' );
define( 'FTP_HOST', '127.0.0.1:22' );
define( 'FS_METHOD', 'ssh2' );
```
