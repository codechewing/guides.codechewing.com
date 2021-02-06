# How to remove nginx version from HTTP header

The default nginx installation includes its version number, as one of the HTTP response headers.
For example, your HTTP response headers might reveal something like this:

```
HTTP/1.1 200 OK
Server: nginx/1.13.0
Date: Thu, 18 May 2017 20:03:43 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 7740
Connection: keep-alive
```

In this case, note the `Server: nginx/1.13.0` header and value, exposing the version details of nginx.

## Update nginx config
 
Open up the main nginx config file, probably located at `/etc/nginx/nginx.conf`.
Locate the `http` section within this file, and add in the following directive:

```
http {
    # ...

    server_tokens off;

    # ...
}
```

Setting `server_tokens` to `off` will **prevent the nginx version number** from being
shown in the HTTP response headers.

Remember to restart nginx...

### Farewell

Security through obscurity is not a competent solution on its own, but in any case, it sometimes makes for
a valid enhancement.

The nginx **ngx_http_core_module** module is responsible for this particular configuration.
See the official docs for further info on the
[server_tokens](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_tokens)(rel="nofollow noopener noreferrer" target="_blank")
directive.