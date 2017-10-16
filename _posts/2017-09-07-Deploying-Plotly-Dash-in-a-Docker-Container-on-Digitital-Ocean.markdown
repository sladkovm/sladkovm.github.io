---
layout: post
title:  "Deploying Dash/Flask application on Digital Ocean using Docker compose"
date:   2017-10-16 20:00:00 +0100
categories: webdev
comments: true
---

I am building the [Velometria.com](http://velometria.com/) using the *microservices* architecture and the easiest way to deploy such application is by using the [*Docker*](https://www.docker.com/) containers. In this article I will outline the deployment of [*FLask*](http://flask.pocoo.org/) based [*Plotly Dash*](https://plot.ly/dash/) application on a [*Digital Ocean*](https://www.digitalocean.com/) droplet.

This is not a step-by-step tutorial, but rather a descriptive guideline for the ready to use boiler plate codebase, that can be found in the [*Github* repository](https://github.com/sladkovm/docker-flask-gunicorn-nginx).

### Get the docker

To make use of *docker* on local machine one would need a [*Docker for Mac*](https://www.docker.com/docker-mac). *Docker* for other OS is also available so check [*their website for more details*](https://docs.docker.com/engine/installation/).

The *docker* infrastructure can be viewed as a layered system:

1. *docker* - a layer for a single service/container.
2. *docker-compose* - a layer for orchestrating multiple containers to bind services together.
3. *docker-machine* - a virtual host tool for easy deployment of multiple containers on local machine and on cloud server.

Detailed example for deploying application on the *Digital Ocean* can be found in the [*official docker  instructions*](https://docs.docker.com/machine/examples/ocean/).


### One service - one container

The dogma of *microservices* implies that the complex application must be broken into basic atomic parts. Examples are: data-source, machine learning model, back-end server, front-end client, proxy server etc.

Each of the services is residing in its own *Docker* container and can be launched, worked on and tested as a standalone application.

Containers are usually talking to each other using the REST API over the public internet, but also can make use of the internal network established by the *docker-compose* software.


### Basic setup of containers

For the purpose of deploying the *Plotly Dash* application we only need two containers. The one for the *Plotly Dash* application itself and the one for deploying the *Nginx* reverse proxy server. The latter will help to serve our application to the public internet using the [reversed proxy](https://stackoverflow.com/questions/20766684/what-benefit-is-added-by-using-gunicorn-nginx-flask).

In order to wrap a service into a *docker* container the special instructions file must be placed inside the service folder. The file is literally called a *Dockerfile*.

For the *Plotly Dash* app, the *Dockerfile* dictates that the container must be build from the official *Python 3* image and the app should be installed into the `/home/project/dash_app` folder in the container. The `RUN pip ...` is obviously making sure that all dependencies are also installed.

```
FROM python:3.6.2

RUN mkdir -p /home/project/dash_app
WORKDIR /home/project/dash_app
COPY requirements.txt /home/project/dash_app
RUN pip install --no-cache-dir -r requirements.txt

COPY . /home/project/dash_app
```

The *nginx* file is requesting an official *nginx* image and replaces the standard *.conf* files with our project specific ones.

```
FROM nginx:1.13.3

RUN rm /etc/nginx/nginx.conf
COPY nginx.conf /etc/nginx/

RUN rm /etc/nginx/conf.d/default.conf
COPY project.conf /etc/nginx/conf.d/
```

The official [*reference guide*](https://docs.docker.com/engine/reference/builder/#format) for *Dokerfile* is a good place to learn more tips and tricks about composing more elaborate setups.

### Orchestrated deployment of containers

Deployment options for multiple containers are specified in the  *docker-compose.yml* file. The [*official guide*](https://docs.docker.com/compose/) is a good place for browsing for more features, such as *volume sharing* between container and the host machine for example.

What our file specifies is that we have two services. The *dash_app* is served on the port `8000` and can be reached on the internal docker network at `http://dash_app:8000` (note the reference in the `./nginx/project.conf`). The *nginx* server depends on the `dash_app`, because it has to serve it on the port `80`

```
version: '3'

services:

  dash_app:
    container_name: dash_app
    restart: always
    build: ./dash_app
    ports:
      - "8000:8000"
    command: gunicorn -w 1 -b :8000 app:server


  nginx:
    container_name: nginx
    restart: always
    build: ./nginx
    ports:
      - "80:80"
    depends_on:
      - dash_app
```

### Run containers locally using the docker-machine

The purpose of the [*docker-machine*](https://docs.docker.com/machine/overview/) is to create a virtual host, where docker containers can be run. It is more awesome than that, but we will come to it later. For now, create the machine, activate it and note the IP address.

```
$ docker-machine create --driver "virtualbox" dash_app
$ eval $(docker-machine env dash_app)
$ docker-machine ls
```

The expected output:

```
NAME   ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
dash_app -    virtualbox   Running     tcp://192.168.99.100:2376        v17.09.0-ce   
```

Build all containers according to [*docker-compose*](https://docs.docker.com/compose/overview/) instructions and run them by executing a command:

```
$ docker-compose up --build -d
```

To verify that containers are running, execute `$ docker-compose ps` command, that should yield:

```
Name                Command               State           Ports         
--------------------------------------------------------------------------
dash_app   gunicorn -w 1 -b :8000 app ...   Up      0.0.0.0:8000->8000/tcp
nginx      nginx -g daemon off;             Up      0.0.0.0:80->80/tcp
```

The app should be available now on the IP address of the *docker-machine*. In this particular case - at `http://192.168.99.100`.


### Deployment on the Digital Ocean droplet with GitHub repository in the middle

In order to deploy the app on the *Digital Ocean* droplet I do the following:

1. Push the code base with all *docker* instruction files to the GitHub repository

2. Create a *Digital Ocean* droplet and install *docker-compose* following the  [*official instructions*](https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-ubuntu-16-04).

3. Pull the codebase from the GitHub repository to the *droplet*

4. SSH into the droplet and execute `docker-compose --build -d`

Now the app is running on the public IP of the droplet.


### Bonus: deployment on the Digital Ocean droplet using docker-machine

Remember the more awesomeness that *docker-machine* brings? One of those is deployment of the containers on remote server directly from the command line.

The trick is simple, rather than creating a docker-machine with `virtualbox` as a driver, we will create one with a `digitalocean` driver:

```
$ docker-machine create --driver digitalocean --digitalocean-access-token xxxxx dash_app_droplet
$ eval $(docker-machine env dash_app_droplet)
```

Now, if you will run the `$ docker-compose up --build -d` it will deploy the app directly from the local host to the droplet.

See [*official guide*](https://docs.docker.com/machine/examples/ocean/#step-5-use-machine-to-remove-the-droplet) for step-by-step instructions.
