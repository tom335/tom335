# Nostr NIP-05 Setup - HAProxy and Lighttpd

My web server setup includes a HAProxy instance and a Lighttpd server for static files, then I've found some issues when doing my NIP-05
to verify my nostr username.

First, there's this [guide](https://orangepill.dev/nostr-guides/guide-to-verify-nostr-profile-nip05-identifier-with-your-domain/)
to get starded. It's a good introduction.

However, in order to enable my HAProxy to accept CORS requests, I needed what is explained [here](https://www.haproxy.com/blog/enabling-cors-in-haproxy).
I've downloaded the Lua script from this [link](https://raw.githubusercontent.com/haproxytech/haproxy-lua-cors/master/lib/cors.lua) into my `/etc/haproxy` folder.

My `haproxy.cfg` file now contains the following, in the respective sections:

```
global
  .
  .
  lua-load /etc/haproxy/cors.lua


frontend default
  .
  .
  http-request lua.cors "GET" "*" "*"
  http-response lua.cors
```

I'm not completely convinced that Lighttpd also need this in my `conf-enabled/10-simple-vhost.conf`, but for the sake of
peace of mind, just add the module `setenv` (moving the `conf-available/ 05-setenv.conf` to `conf-enabled` folder,
and configure it as follows (either in simple-vhost or in the setenv conf:

```
setenv.add-response-header = (
  "Access-Control-Allow-Origin" => "*",
  "Access-Control-Allow-Methods" => "HEAD, GET, OPTIONS",
  "Access-Control-Expose-Headers" => "Content-Range, Date, Etag, Cache-Control, Last-Modified",
  "Access-Control-Allow-Headers" => "Content-Type, Origin, Accept, Range, Cache-Control",
  "Access-Control-Max-Age" => "600",
  "Timing-Allow-Origin" => "*"
)

```

Also, don't forget to create the `.well-known/` folder in your server root where you're going to serve the `nostr.json` file.

Another tip: your npub key MUST be in HEX format, so you can easily convert it with this [site](https://nostrcheck.me/converter/) or
any other tool.

You can use the tool [Test Cors](https://www.test-cors.org/), providing the full URL of your site and JSON file.

Feel free to provide your feedback if this article was useful to you, or if you're facing any challenge related.

Good luck!
