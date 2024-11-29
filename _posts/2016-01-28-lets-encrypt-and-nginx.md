---
layout: post
title: Let's Encrypt and Nginx
categories:
- tools
tags:
- letsencrypt
- nginx
---

I'm late to the game, but I finally gave [Let's Encrypt](https://letsencrypt.org/) a try
and I love it. The biggest advantage is the fact that SSL certificates
can be completely automated. No more remembering how to renew certificates
once a year.

These are mostly just notes for my future use, but maybe it will be useful for somebody.
This is how I use Let's Encrypt with Nginx.

Install the letsencrypt client:

```
cd /opt
git clone https://github.com/letsencrypt/letsencrypt
VENV_PATH=/opt/letsencrypt/env/ /opt/letsencrypt/letsencrypt-auto plugins
```

Create a directory for the client to use for authorization:

```
mkdir -p /srv/www/letsencrypt
```

Then I put this into my nginx site config:

```
vim /etc/nginx/sites-enabled/example.com
```

```
location /.well-known/acme-challenge {
    root /srv/www/letsencrypt;
}
```

```
service nginx reload
```

That allows the letsencrypt client to manage authorization files for my domain. And now I can generate the first certificate:

```
/opt/letsencrypt/env/bin/letsencrypt certonly --webroot -w /srv/www/letsencrypt/ -d example.com,www.example.com
```

Hopefully, that should generate a certificate and I can put them into the HTTPS section of my nginx config:

```
vim /etc/nginx/sites-enabled/example.com
```

```
ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
```

```
service nginx reload
```

And for the main benefit, I can now set up a cron job like this, that will make sure my certificates stay up to date:

```
10 20 * * * /opt/letsencrypt/env/bin/letsencrypt-renewer >/dev/null && service nginx reload >/dev/null
```
