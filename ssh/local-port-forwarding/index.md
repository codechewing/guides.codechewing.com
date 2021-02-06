# Encrypt HTTP traffic with SSH local port forwarding

If you find yourself authenticating sessions on a website **without HTTPS** enabled (using HTTP instead), you may
not be aware that you're doing this insecurely, over an **unencrypted** connection. Or you probably are, else
you wouldn't be reading this! :D

By browsing via unencrypted channels, you can, for example, expose your username and password upon login, among
many other things to any network traffic snooping devices and software. Including perhaps, your corporate firewall
at work.

If you have SSH login access to a web server, that has access to the public internet (very likely), you can use
a powerful OpenSSH feature called **local port forwarding** to encrypt your HTTP requests & responses.

Note: It's not limited to just HTTP, but it makes for a good example.

## Checklist

- SSH access to a server with public internet access
- sudo/administrative privileges on your local machine (client)

For me, my Mac is my client, and I'll be using Terminal to connect via SSH to a Linux web server of my own.
If you're on Windows, you'll be using Putty or similar to SSH into your server of choice.

## Setup local port forwarding

In my scenario, I want to access `http://www.codechewing.com` (port 80) securely, without using HTTPS. The
idea is to get my web server to make an encrypted request and forward it securely to my Mac, via an open
SSH session. I want to login to my website's CMS and perform administrative duties, whilst having my network
traffic protected with encryption.

This has other potentially beneficial side effects; if you're behind a firewall (corporate or otherwise), the
firewall and/or network traffic sniffing will only be able to detect an SSH connection coming from the client,
concealing the website being requested and encrypting all communication.

Firstly, open your client's host file and have all requests for `www.codechewing.com` to resolve to localhost
(`127.0.0.1`). On Mac, that's `/etc/hosts`, and on Windows, it's
`C:\Windows\System32\drivers\etc\hosts`. I'm sure you'll already know this. Here's my hosts entry:

<pre><code class="ini">127.0.0.1    www.codechewing.com</code></pre>

If you try and access `http://www.codechewing.com` inside your browser, it shouldn't load. This is because
it's now pointing to your localhost, which doesn't know what to do with it, not yet anyway. This proves
you've captured HTTP requests on port 80 for this particular website. 

Now things start to get much more interesting, as the next step is to connect to your web server,
**enabling the local port forwarding**. When we SSH into the server, we'll need to pass some special options
with the command:

<pre><code class="shell">$ sudo ssh -L 127.0.0.1:80:www.codechewing.com:80 user@12.34.56.789</code></pre>

Replace `user` with your username, and the last IP address (`12.34.56.789`) with your server's actual IP address.

## Local port forwarding SSH command breakdown

1. `-L` flag - which means to activate local forwarding
2. `127.0.0.1` - the local IP of the client
3. `80` - the port from the client you want to forward
4. `www.codechewing.com` - the website you wish the server to reach out to
5. `80` - the port on the server which the request uses

Upon executing the command and entering your client admin password & SSH password when prompted, the SSH login
should proceed normally, and you'll be logged into the web server. It's important to keep this session open,
else the local port forwarding will stop working.

If you now navigate to `http://www.codechewing.com` in the browser, it'll load! Your website is being accessed
via the local port forwarding. You've just encrypted your traffic to the website!

### Farewell

You can omit the IP address on the SSH client, and it'll automatically attach to `127.0.0.1` anyway. So the
previously used SSH command can be shortened to:

<pre><code class="shell">$ sudo ssh -L 80:www.codechewing.com:80 user@12.34.56.789</code></pre>
