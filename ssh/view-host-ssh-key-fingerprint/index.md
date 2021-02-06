# How to view the host SSH key fingerprint & check authenticity

It might be necessary for you to identify a server's host key fingerprint when you SSH into the server for the first
time, as it may not have been saved into your known hosts file yet.

As a security measure, you should know what the host's key fingerprint is ahead of time, **before you accept and login**,
otherwise you could inadvertently be vulnerable to a
[man in the middle attack](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)(rel="nofollow noopener noreferrer" target="_blank").

As an admin user of the host/server, you can find out all the key fingerprints for each available public key and
send/show it to whoever needs to login beforehand.

As the client may use any of the available encryption (i.e.. RSA, ECDSA or DSA), gather all the key fingerprints
together for future reference:

```
$ ssh-keygen -lf /etc/ssh/ssh_host_rsa_key.pub > $HOME/key_fingerprints.txt
$ ssh-keygen -lf /etc/ssh/ssh_host_dsa_key.pub >> $HOME/key_fingerprints.txt
$ ssh-keygen -lf /etc/ssh/ssh_host_ecdsa_key.pub >> $HOME/key_fingerprints.txt
```

- `l` prints out the key fingerprint
- `f` specifies the file

Inside of this user's home directory will now be a text file `key_fingerprints.txt` containing all the key
fingerprints which you can safely distribute it your users.

It'll look something like this:

<pre><code class="shell">2048 9a:f9:24:59:5a:17:c6:ca:64:99:89:82:3a:05:98:7d  root@codechewing.com (RSA)
1024 bf:ee:a8:2b:d5:62:e9:1b:65:99:e3:83:e7:81:3f:5c  root@codechewing.com (DSA)
256 9e:46:11:90:c2:6h:17:1b:00:ce:57:6e:43:3e:e8:64  root@codechewing.com (ECDSA)</code></pre>

### Farewell

It's vigilant to check these kind of details before connecting to a new server for the first time. Although it's
obviously an inconvenience, it could help keep your online activity more secure.
