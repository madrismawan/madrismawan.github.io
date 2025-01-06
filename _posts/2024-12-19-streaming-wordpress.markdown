---
title: "Live Streaming in WordPress"
layout: post
date: 2024-12-19 10:10
tag: [wordpress, RTSP, CCTV, Streaming, ffmpeg  ]
headerImage: false
blog: true
hidden: false
description:  seamlessly integrate CCTV live streaming using RTSP (Real-Time Streaming Protocol) into WordPress websites
category: blog
author: maderismawan
externalLink: false
---

My friend and I worked on a project to develop a WordPress-based website designed to showcase artworks or exhibitions. The idea was  was created website to allow audiences from various locations to enjoy the artworks without attending in person. It includes an introduction to the exhibition and displays the artworks online, reaching a broader audience beyond visitors into to the location.

The initial stage of development begins with creating a system design. The first schema i create like this : 

![Screenshot]({{site.url}}/assets/images/blogs/streaming-wordpress/schema-1.png)

Due to several considerations and challenges, the final design I used for the website development is as follows:

![Screenshot]({{site.url}}/assets/images/blogs/streaming-wordpress/schema-final.png)

With the design already created, before proceeding, I prepared several tools to be used in building the website.

1. Raspberrypi
2. Internet modem
3. CCTVs (Support RTSP)
4. VM (Virtual Manchine)

<div class="side-by-side">
  <div class="toleft">
      <img style="height: 400px;" class="image" src="{{ site.url }}/assets/images/blogs/streaming-wordpress/prepare.png" alt="Alt Text">
      <figcaption class="caption">Several tools</figcaption>
  </div>
  <div class="toright">
      <img style="height: 400px;" class="image" src="{{ site.url }}/assets/images/blogs/streaming-wordpress/prepare-2.png" alt="Alt Text">
      <figcaption class="caption">Location Exhibition</figcaption>
  </div>
</div>

Here are some setups I implemented while creating the website:
## 1. Raspberrypi
On the Raspberry Pi, you can set up.
* [FFmpeg](https://phoenixnap.com/kb/install-ffmpeg-ubuntu)
* [Nginx](https://ubuntu.com/tutorials/install-and-configure-nginx#1-overview)
* [Autossh](https://blog.invgate.com/autossh)
* [Raspberry Pi Connect](https://www.raspberrypi.com/documentation/services/connect.html)

and set up nginx with configuration like this :

{% highlight raw %}
/etc/nginx/nginx.conf

http {
    include       mime.types;
    default_type  application/octet-stream;
    server {
        listen 80;
        server_name localhost;

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }

        # HLS streaming configuration
        location /hls/ {
            add_header Cache-Control no-cache;
            # Set proper MIME type for .m3u8 and .ts files
            types {
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }
            root /var/www/html/stream-cctv;
            access_log off;
            expire 24h;
        }
    }
}
{% endhighlight %}

## 2. VM (Virtual Machine)
On the Virtual Machine, you need to set up WordPress and MySQL. Here, I used Docker with my custom Docker Compose configuration, which is as follows:

{% highlight raw %}
~/docker-compose.yml

services:
  wordpress:
    image: wordpress:latest
    container_name: wordpress
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: ${DB_USER}
      WORDPRESS_DB_PASSWORD: ${DB_PASSWORD}
      WORDPRESS_DB_NAME: ${DB_NAME}
    volumes:
      - ./wp-data:/var/www/html
    networks:
      - percakapanperahu-network
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.wordpress.rule=Host(`percakapanperahu.com`,`www.percakapanperahu.com`)"
      - "traefik.http.routers.wordpress.entrypoints=websecure"
      - "traefik.http.routers.wordpress.tls.certresolver=myresolver"

  db:
    image: mysql:8.1
    container_name: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_PASSWORD}
      MYSQL_DATABASE: ${DB_NAME}
      MYSQL_USER: ${DB_USER}
      MYSQL_PASSWORD: ${DB_PASSWORD}
    volumes:
      - ./db-data:/var/lib/mysql
    networks:
      - percakapanperahu-network

  traefik:
    image: traefik:v2.10
    container_name: traefik
    command:
      - "--api.insecure=true"
      - "--providers.docker=true"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.websecure.address=:443"
      - "--certificatesresolvers.myresolver.acme.tlschallenge=true"
      - "--certificatesresolvers.myresolver.acme.email=ceritaperahu@gmail.com"
      - "--certificatesresolvers.myresolver.acme.storage=/letsencrypt/acme.json"
    ports:
      - "80:80"
      - "443:443"
      - "8080:8080" 
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
      - "./letsencrypt:/letsencrypt"
    networks:
      - percakapanperahu-network
    labels:
      - "traefik.enable=true"
      # Redirect www.percakapanperahu.com to https://percakapanperahu.com
      - "traefik.http.routers.www-redirect.rule=Host(`www.percakapanperahu.com`)"
      - "traefik.http.routers.www-redirect.entrypoints=web"
      - "traefik.http.middlewares.redirect-www-to-non-www.redirectregex.regex=^https?://www\\.(.*)"
      - "traefik.http.middlewares.redirect-www-to-non-www.redirectregex.replacement=https://$1"
      - "traefik.http.middlewares.redirect-www-to-non-www.redirectregex.permanent=true"
      - "traefik.http.routers.www-redirect.middlewares=redirect-www-to-non-www"
      # Redirect all HTTP to HTTPS
      - "traefik.http.routers.http-catchall.entrypoints=web"
      - "traefik.http.routers.http-catchall.rule=Host(`percakapanperahu.com`)"
      - "traefik.http.middlewares.redirect-to-https.redirectScheme.scheme=https"
      - "traefik.http.routers.http-catchall.middlewares=redirect-to-https"

networks:
  percakapanperahu-network:
    external: false

{% endhighlight %}

# System Workflow Explanation

### 1. Streaming CCTVs
On the Raspberry Pi, you can run a file for streaming in the background using the script I created, as follows:

{% highlight raw %}
#!/bin/bash
~/stream-cctv-x.

OUTPUT="/stream-cctv"

while true; do
    echo "Starting FFmpeg..."
    ffmpeg -rtsp_transport tcp \
    -i rtsp://admin:admin@localhost:xxxx/Streaming/Channels/101 \
    -c:v libx264 -preset veryfast -crf 23 -b:v 800k -maxrate 800k -bufsize 1600k \
    -vf "scale=640:-1" -c:a aac -b:a 128k -ac 1 \
    -f hls -hls_time 2 -hls_list_size 6 -hls_flags delete_segments+append_list \
    -hls_segment_filename "$OUTPUT"/camera1_%03d.ts \
    "$OUTPUT"/camera-x.m3u8 > camera-x.log 2>&1
    
    echo "FFmpeg crashed. Restarting in 5 seconds..."
    sleep 5
done
{% endhighlight %}


### 2. Tunneling to VM
After the previous process is successfully completed, create an SSH tunneling connection to the VPS. Generate a public key on the Raspberry Pi and save the public key from the Raspberry Pi into the VM so that the connection can be established via SSH.

{% highlight raw %}
autossh -M 0 -f -N -R port-access-on-vm:ip-public-raspberryp:port #host-vps
{% endhighlight %} 

### 3. Viewer Access
In WordPress, you can use the FV Player extension to display HLS on the Raspberry Pi with the URL.

{% highlight raw %}
https://localhost:1000/stream/cam1/stream.my12
{% endhighlight %}


Here are some attachments from the event.

The performance was held at W Bali from November 18 to 27, 2024 with the result venue.
![Screenshot]({{site.url}}/assets/images/blogs/streaming-wordpress/exhibition-venue.jpg)
<figcaption>Exhibition Venue</figcaption>

![Screenshot]({{site.url}}/assets/images/blogs/streaming-wordpress/attch-1.png)
<figcaption>Website</figcaption>




