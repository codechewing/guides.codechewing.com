# Enable SFTP access only to server for particular user or group

It's worth being strict about SSH access to your server, and it's often not necessary to give full access to
every type of user you may have on the server.

For example, let's say you want to allow your clients that have websites on your server, to be able to use SFTP
to access their website files, but no more. This is the scenario we'll work with throughout this short tutorial.

## Modifying the OpenSSH config

OpenSSH provides SFTP connection capabilities through the normal SSH port (22 as standard). This means you can use
OpenSSH to enable SFTP access without installing any other package, such as ProFTP.

SSH into the server as an admin user, and open up the SSH server config file for editing:

```
$ vi /etc/ssh/sshd_config
```

Search for the `Subsystem` directive, and replace the content with the following line:

```
Subsystem sftp internal-sftp
```

Note: Subsystem may have had a similar value before, such as `Subsystem sftp /usr/lib/openssh/sftp-server`.
This is normally enough for unrestricted needs, as it enables the OpenSSH SFTP server. However, we're after
more defined control.

At the bottom of the config file, where you may already have some allow, deny or match rules, add a rule for
the group `sftponly` (which we'll shortly create):

<pre><code class="ini">Match Group sftponly
  ChrootDirectory %h
  X11Forwarding no
  AllowTcpForwarding no
  ForceCommand internal-sftp</code></pre>

Finish off this part by restarting SSH:

```
$ service ssh restart
```

Note: If you already have an `AllowGroups` or `AllowUsers` directive as well as the match group rule, you'll
need to also add the `sftponly` group to the directive, else they won't even be able to SSH in, regardless
of your new match rule being present.

## Managing the SFTP only group &amp; users

Create the **sftponly** group:

```
$ groupadd sftponly
```

Add the desired user (`bob`) to this group:

```
$ usermod -a -G sftponly bob
```

## Update permissions

This part is just as important as the rest. Change the ownership of the user's home directory to root:

```
$ chown root:root /home/bob
```

Optionally update the permissions of the directory - disallowing them to create directories in their home location:

```
$ chmod 755 /home/bob
```

## Connecting

To connect via SFTP through an FTP application, such as FileZilla, use the following details:

- Host: `<server IP>`
- Protocol: SFTP
- Port: 22
- username/password

### Farewell

As the user is jailed inside their home directory, if you've got a website say in `/var/www` they need access to,
you'll need to create a directory inside their home location and mount the website into it.
