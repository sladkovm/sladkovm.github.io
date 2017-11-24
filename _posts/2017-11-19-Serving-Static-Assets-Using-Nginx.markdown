---
layout: post
title:  "Serving Static Assets Using Nginx"
date:   2017-11-24 20:00:00 +0100
categories: webdev
comments: true
---

The client part of the [velometria.com](http://velometria.com) is build using [Plotly Dash](https://plot.ly/products/dash/). It is a web framework that helps creating beautiful web interfaces in pure *Python* without ever touching *Javascript* code. Currently the framework does not have support for static assets. In practice this means that images, *google analytics* scripts, css files, etc. must be served from elsewhere to be used by the *Plotly Dash* application.

One solution for this problem is to utilize the *Nginx* server. It is already [acting as a *reverse proxy* server for our application](https://sladkovm.github.io/webdev/2017/10/16/Deploying-Plotly-Dash-in-a-Docker-Container-on-Digitital-Ocean.html), why not use it also for serving static assets.

The final results can be found on [GitHub](https://github.com/sladkovm/docker-flask-gunicorn-nginx/tree/static_assets).

### Brief recap of the project structure

The final app consist of two containers - one for the *Plotly Dash* application *dash_app*, another one for the *Nginx* server. Deployment of containers is orchestrated by *Docker Compose* in combination with *Docker Machine*. This results in the following project structure:

```
|-- docker-compose.yml
|-- dash_app
|   |-- Dockerfile
|   |-- app.py
|   `-- requirements.txt
|-- nginx
|   |-- Dockerfile
|   |-- nginx.conf
|   |-- project.conf
|   `-- static
|       |-- api_logo_pwrdBy_strava_horiz_light.png
|       |-- api_logo_pwrdBy_strava_stack_light.png
|       |-- btn_strava_connectwith_orange.png
|       `-- btn_strava_connectwith_orange@2x.png
```

### Copy static assets to the nginx container

I've added the new directory *static* to the *nginx* container and filled it with some images.

This folder must be copied into the *nginx* container and this step is accomplished in the *Dockerfile*:

```
FROM nginx:1.13.3

RUN rm /etc/nginx/nginx.conf
COPY nginx.conf /etc/nginx/

RUN rm /etc/nginx/conf.d/default.conf
COPY project.conf /etc/nginx/conf.d/

COPY ./static ./static
```

### Configure the Nginx to serve static assets

The *nginx.conf* contains the baseline *Nginx* configuration while *project.conf* is used for adding project specific functionality.

```
# project.conf

server {

    listen 80;
    server_name docker_flask_gunicorn_nginx;

    location / {
        proxy_pass http://dash_app:8000;

        # Do not change this
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    location /static {
        root /static;
    }
}
```

The last *location /static {}* block was added with a purpose to serve the static assets at the address *http://192.168.99.100/static/api_logo_pwrdBy_strava_horiz_light.png*, where *192.168.99.100* is the address of the server in this case.

As a result of these manipulations we can now add a *Strava* log to the *bar* chart.

![Seving Static Assets Using Nginx]({{site_url}}/assets/2017-11-24-Serving-Static-Assets-With-Nginx/serving_static_assets_with_nginx.png)
