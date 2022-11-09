---
title: Using Docker Compose
date: "2022-11-09"
tags: ["docker", "docker-compose"]
---

### Preamble
Nowadays, everyone's favorite buzzword is "__microservices__", and that's very logical. In a microservices architecture it's easy to scale up separate services as need be. It's a proven architecture pioneered by companies as [Netflix](https://www.youtube.com/watch?v=CZ3wIuvmHeM&ab_channel=InfoQ). However, sometimes it can be tedious to take care of the separate services. That is why __Docker__ was invented.

### What is Docker?
Docker is a way of _containerizing_ applications. A container is a way of "packing up" a piece of code along with its dependencies. This way the application can be started quickly and it can be reliably run across many sorts of computers with many sorts of different architectures.

A Docker container is created with a _Dockerfile_. A Dockerfile is a way to tell Docker how to build your container. In a Dockerfile you can specify the OS you want your application to run on (most commonly used are Ubuntu, Alpine or Fedora), where your application is and how to run it.

An example of a Dockerfile, to run a NodeJS application:

```dockerfile
# syntax=docker/dockerfile:1
FROM node:12-alpine
RUN apk add --no-cache python2 g++ make
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
```

To get more extensive documentation, you can use the Dockerfle [Reference](https://docs.docker.com/reference/)

Then to build the container:
```bash
docker build .
```

And to run the container:
```bash
docker run -it your_image
```
### What is Docker-compose?
It is however sometimes tedious to keep track of building images and the images you have built. Therefore docker-compose was created.

Docker-compose is a tool that multiple docker containers can be build from a single YAML file. This massively simplifies the maintaince of your application. Note: Dockerfiles are still necessary.
### How to use docker-compose
Here we create a single container called "web" that builds our Dockerfile and maps port 3000 to our host port of 8000.
```yaml
version: "3.9"
services:
  web:
    build: .
    ports:
      - "8000:3000"
```

To create the container run: `docker-compose up --build`.

Now navigate in your browser to localhost:8000 and you should see your application.

Another big advantage of docker-compose is that you can use already existing container through [Dockerhub](https://hub.docker.com/). Lets create a Redis database service, using redis' own container:
```yaml
version: "3.9"
services:
  redis-database:
    image: redis
    ports:
      - "8000:6379"
```
This starts a Redis database that maps our host port of 8000 to the port 6379 in the container.

#### Environment variables
Docker-compose makes it really easy to maintain environment variables. As outlined above, we clearly stated on which port our application should run. This can become quite cumbersome on the command line. With docker-compose, we can use even more environment variables:

To set an environment variable:
```yaml
version: "3.9"
services:
  redis-database:
    image: redis
    ports:
      - "8000:6379"
    environment:
      - REDIS-PASSWORD=my_password 
```
This sets the password for the redis CLI in your container.

This is not only limited to the redis container. Consult the reference of your container you want to use to see all the environment variables you can set.

#### Networks
With docker-compose you can also specify a network. This is an internal network which can be used by the containers to talk to each other. By default docker-compose creates a network.

You can join two (or more) containers together like this:
```yaml
version: "3.9"
services:
  web:
    build: .
    links:
      - "db:database"
  db:
    image: postgres
```
Now, within your web application, you can talk to your database; and reference it by the name "database".

### Conclusion
Docker is easier to use than ever. And with docker-compose, its even easier to containerize your application and maintain it.