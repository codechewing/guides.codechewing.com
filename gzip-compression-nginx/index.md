# How to add gzip file compression on nginx

Adding gzip file compression can speed up the response time for your website. As with
many optimisations, it's not a silver bullet, as compressing files on the server consumes
CPU effort. However, the download size of transferred files is reduced for your end users.

This guide will show you how to setup gzip compression for the nginx server.

## Enable gzip compression

Open up the main nginx config file, probably located at `/etc/nginx/nginx.conf`.
Locate the `http` section within this file, and add in the following gzip
directives & values:

```
http {
    # ...

    gzip on;
    gzip_comp_level 4;
    gzip_types text/plain text/css application/javascript image/png;

    # ...
}
```

1. `gzip on` enables compression. It's actually off by default.
2. `gzip_comp_level` sets the level of compression. 1-9. 1 being low, 9 high.
Inevitably, the higher compression level, the more CPU cost.
3. `gzip_types` lists the file/mime types to apply compression for. `text/html` is 
already enabled by default, so there's no need to explicitly add it to the list here.
In fact, nginx will warn you if you do this.

There's a useful mime type reference within the nginx installation, located at
`/etc/nginx/mime.types`.

Now just restart or reload nginx for the changes to take effect.

### Testing gzip is enabled

On future HTTP requests to the nginx server, seek out the response header named `Content-Encoding`.
Hopefully you'll observe something like `Content-Encoding: gzip`.

In Chrome dev tools, it might look something like this:

<img src="/assets/images/posts/gzip-compression-nginx/gzip-content-encoding.png" alt="gzip content encoding header" class="img-thumbnail">

### Farewell

The nginx **ngx_http_gzip_module** module is responsible for the gzip compression
support. To find out what other options are available, check out the official
[documentation](http://nginx.org/en/docs/http/ngx_http_gzip_module.html)(rel="nofollow noopener noreferrer" target="_blank").

Enjoy your gzipped compression. Your users certainly will. 
