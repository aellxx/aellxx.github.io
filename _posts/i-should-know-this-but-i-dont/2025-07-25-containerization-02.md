---
title: "Containerization Pt. 2"
layout: post
date: 2025-07-25
categories: [I-Should-Know-This-But-I-Don't]
---

# Intro

In my last post, I wrote about physical servers, virtual machines, and a brief intro to containers. Well, that wasn’t my initial plan.

You see, I was going to go straight into containers, write an entire post about it, and then call it a day. However, being me, I had to unpack everything I couldn’t wrap my head around over the past three years. Now that the foundations are in place, we can officially dive into the real work.

# Recap

Let’s start with a recap of where we left off in the last post: containers.

Here’s the definition I wrote in the TL;DR:

> A container is something you use to package and ship software applications along with all the application dependencies, libraries, and runtime necessary for the application to run.

If this doesn’t click yet, I think it’s much easier to understand if you forget about tech for just a second and imagine one of those Betty Crocker cake mixes. In general (under the assumptions you have a working oven and all the kitchen resources), if you have the box cake mix, 3 eggs, water, and oil, you can bake yourself a cake that tastes pretty much the same whether you use your kitchen or the kitchen at a 3-star Michelin restaurant. Most likely, you won’t have cases where the cake made in the bougie kitchen is edible and the cake made in your own is inedible (hopefully).

So with this in mind, let’s dig into what Docker is.

# Docker Disclaimer

The first time I had to deal with containers, I was introduced to Docker, and back then, people seemed to use “containers” and “Docker” interchangeably. This does make sense, but I want to clearly define what Docker is to prevent any confusion in this post.

Containerization is the general concept of isolating applications and their dependencies into portable well… Containers. Docker is the specific implementation that enables containerization – the platform that provides tooling for developing and running containerized applications. While it is possible to do containerization without Docker, it seems to be the monopoly platform. Since it is (probably) the most commonly used platform for containerization, I will note that the technical examples in this post (and probably my entire blog) will use Docker-specific implementations.

# Container Images

For the sake of simplicity, there’s something I didn’t include in my definition of a container, and that is the idea that a container is a process – something that actively runs. Getting nuanced here, containers are running instances of container images – you can think of images as cookie cutters and containers as the actual cookies.

If you have a tree-shaped cookie cutter, you’ll most likely end up with tree-shaped cookies. If you have a star-shaped cookie cutter, you should be getting star-shaped cookies. It doesn’t matter where you cut out the cookies; they’re going to end up with the same shape.

The same goes for container images and containers. If you use the same container image, say an image for a running PostgreSQL database, you are going to end up with the same kind of running database instance, whether you are on a Mac or Windows (no more debating sessions with your friend about which OS is superior[^1]).

## Image Layers

Now, the question that led me to another deep dive was: What exactly are these container images made of?

Images are made up of multiple layers, with each layer contributing to the final container image. For example, I built an image for an Angular web application that had the following layers on a pre-built container image containing Alpine Linux (a lightweight version of Linux) with Node.js installed:

- Layer 1: Creates a directory for copying over package.json files to define dependencies
- Layer 2: Creates a directory to handle secrets
- Layer 3: Securely copies the secrets into the container
- Layer 4: Runs npm install to download all Angular dependencies and development tools
- Layer 5: Copies the actual source code

If you haven’t noticed, each layer represents a change in the file system. If you read the bullet points again, you’ll see that they involve creating, copying, and downloading things in the container. In other words, each layer contributes to building up the file system required for a container to run.

The biggest advantage of this layered approach is that as you develop your application and need to rebuild your images, only the necessary layers will be rebuilt. Going back to my Angular web app example, let’s say I made some changes to a TypeScript file that fixed a bug. This would require an image rebuild, since the current version of the image includes the bug. Upon rebuilding the image, only the layer that copies the source code (Layer 4) will be rebuilt, since the previous layers remain identical.

## Dockerfile

At this point, we know that container images are blueprints for running containers, but you still might have a lingering question about how images are created. This is where `Dockerfile` comes in.

Think of `Dockerfile`s as a recipe for creating container images. Essentially, it’s just a text file that contains step-by-step commands for building your image one layer at a time.

Here’s a simplified version of the `Dockerfile` I wrote for my Angular web app (I intentionally left out some lines that didn’t result in file system changes):

```Dockerfile
FROM node:20.11.0-alpine

WORKDIR /app # this line does not create a layer

COPY package*.json ./

RUN npm install

RUN mkdir -p /root/secrets
RUN --mount=type=secret,id=ENVIRONMENT_SECRETS \
    cat /run/secrets/ENVIRONMENT_SECRETS | base64 -d > /root/secrets/secrets.env

COPY . .
```

### Optional: A Line-by-Line Walkthrough

Now it’s not going to be so Ael-like of me if I don’t nitpick over every line of code (or in this case, the Dockerfile), so here’s an explanation that will (hopefully) sound more like English:

- `FROM node:20.11.0-alpine`: This line specifies the base image for building the new image. It uses Node.js version 20.11.0 on top of the lightweight Alpine Linux distribution.
- `WORKDIR /app`: This sets the working directory inside the container to `/app` for any subsequent commands. It's similar to `cd`-ing into the directory where you want to "build the contents" of your container image.
- `COPY package*.json ./`: This command copies all `package.json`-like files from the current directory on the host machine into the `/app` directory in the container (notice the `\*` regular expression)
- `RUN npm install`: This command executes `npm install` inside the container, which downloads and installs all the dependencies listed in the `package.json` file(s).
- `RUN mkdir -p /root/secrets`: This creates a directory named `secrets` inside the `/root` directory within the container if it doesn't already exist. The `-p` flag ensures that parent directories are also created if necessary.
- `RUN --mount=type=secret,id=ENVIRONMENT_SECRETS \`: This line, along with the next, securely mounts a secret named `ENVIRONMENT_SECRETS` into the container.
- `cat /run/secrets/ENVIRONMENT_SECRETS | base64 -d > /root/secrets/secrets.env`: This command reads the content of the mounted secret, decodes it from base64, and then writes the decoded content into a file named `secrets.env` within the `/root/secrets` directory.
- `COPY . .`: This command copies all remaining files and directories from the current directory on my local machine into the `/app` directory in the container.

# Container Registry

One of the biggest advantages of containerization is the ability to share these cookie-cutter images, either with others or your future self. So, how are they stored and shared?

Images are stored in container registries, and the Docker Hub is a great example. If you look into Docker Hub, you’ll be greeted with hundreds of pre-built images you can use off-the-shelf.

# Multi-Container Applications with Docker Compose

Everything we’ve discussed to this point involved applications running on a single container. This can work out if you have something small, such as the web application I built for my senior project. However, most of us who have learned to code have probably at least heard some

variant of the principle that each piece of your program should have a single, focused responsibility – don't try to make one thing do everything.
This same principle applies to containerization. We want every container to be responsible for doing one thing, and to do it well. In the case of my senior project, I didn't go with a multi-container approach since we used Supabase, which took the burden off of writing custom backend logic and setting up a database server. However, hadn’t we used Supabase, we would’ve had the following three components:

1. Frontend Angular application
2. Backend API built with Node.js
3. PostgreSQL database

For context, I was responsible for writing the stored procedures in Supabase and the Angular service code that called them. Without Supabase, I would've been building the backend API and managing the PostgreSQL database myself.

Now, let’s say I was feeling extra rebellious on setup day and shoved all the components into a single container. In that case, my team would’ve faced some major inconveniences in the development process. We would’ve been exposed to a higher risk of stepping on each other’s toes since we'd all be working in the same container environment.
Imagine this. If I needed to restart the database to test a schema change, the entire application would go down – frontend, backend, you name it. My teammate(s) working on the Angular UI would suddenly lose their development server. If someone wanted to update a Node.js dependency, everyone would need to rebuild the same massive container.
Not to add to the chaos, we would expose ourselves to higher security risks since the API keys for authenticating API calls and database credentials are going to be accessible by every component of the application. That is, someone could just creep into the frontend application and fiddle with the API or (even worse) the database because the secrets are shared across the entire system instead of being restricted to the parts that need them.
Looking back, I feel like I would’ve learned more about designing and implementing APIs if we used AWS or Azure instead, but I probably would’ve suffered even more than I already did 😂.

## Docker Compose

Fortunately, we have Docker Compose that enables us to run multiple containers that work together as a single application. Instead of cramming everything into one container or trying to orchestrate multiple containers yourself, Docker Compose lets you describe your entire application stack in a simple YAML configuration file.
With Docker Compose, the Angular frontend, Node.js API implementation, and PostgreSQL database can spin up as separate containers with a single command, and they'll all be able to communicate with each other without me having to intervene in the middle. Each container will stay focused on its job, the team can work independently, and the secrets will stay properly isolated across the different components.

Let me show you what this actually looks like. Here's a sample docker-compose.yml file for our three-component application:

```yaml
version: "3.8"

services:
  # PostgreSQL Database
  database:
    image: postgres:15
    environment:
      POSTGRES_DB: senior_project
      POSTGRES_USER: app_user
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    secrets:
      - db_password

  # Node.js Backend API
  backend:
    build: ./backend
    environment:
      DATABASE_URL_FILE: /run/secrets/database_url
    ports:
      - "3000:3000"
    depends_on:
      - database
    secrets:
      - database_url

  # Angular Frontend
  frontend:
    build: ./frontend
    environment:
      API_URL: http://backend:3000
    ports:
      - "4200:4200"
    depends_on:
      - backend

  volumes:
    postgres_data:

  secrets:
    db_password:
      file: ./secrets/db_password.txt
    database_url:
      file: ./secrets/database_url.txt
```

### Another Optional Line-by-Line Walkthrough

Now this one's a long one, so feel free to skip over if you want.

- `version: '3.8'`: Specifies the Docker Compose file format version being use
- `services:`: Defines the different services, a.k.a the containers, that make up the application.
  - `database:`: The PostgreSQL database service.
    - `image: postgres:15`: Specifies that this service will use the `postgres` image with version `15`.
    - `environment:`: Sets environment variables for the database container.
      - `POSTGRES_DB: senior_project`: Sets the database name to `senior_project`.
      - `POSTGRES_USER: app_user`: Sets the database user to `app_user`.
      - `POSTGRES_PASSWORD_FILE: /run/secrets/db_password`: Specifies that the database password will be read from a file at this path, mounted from a Docker secret.
    - `ports:`: Maps ports between the host and the container.
      - `"5432:5432"`: Maps port `5432` on the host to port `5432` in the container, allowing access to the database.
    - `volumes:`: Defines named volumes for persistent data storage.
      - `postgres_data:/var/lib/postgresql/data`: Mounts the `postgres_data` named volume to the database's data directory inside the container.
    - `secrets:`: Specifies which Docker secrets this service needs access to, in this case, db_password that is specified in the secrets section at the bottom of the file.
  - `backend:`: This defines the Node.js backend API service.
    - `build: ./backend`: Instructs Docker Compose to build the image for this service from a Dockerfile located in the `./backend` directory.
    - `environment:`: Sets environment variables
      - `DATABASE_URL_FILE: /run/secrets/database_url`: Specifies that the database connection URL will be read from a file at this path, mounted from a Docker secret.
    - `ports:`: Maps ports between the host and the container.
      - `"3000:3000"`: Maps port `3000` on the host to port `3000` in the container.
    - `depends_on:`: Defines service dependencies, in this case, making sure the database service starts before the backend service.
    - `secrets:`: Specifies which Docker secrets this service needs access to. In this case, database_url.
  - `frontend:`: This defines the Angular frontend service.
    - `build: ./frontend`: Instructs Docker Compose to build the image for this service from a Dockerfile located in the `./frontend` directory.
    - `environment:`: Sets environment variables for the frontend container.
      - `API_URL: http://backend:3000`: Sets the URL for the backend API that the frontend will communicate with.
    - `ports:`: Maps ports between the host and the container.
      - `"4200:4200"`: Maps port `4200` on the host to port `4200` in the container.
    - `depends_on:`: Defines service dependencies. In this case, it ensures that the backend service starts before the `frontend` service.
  - `volumes:`: This section defines named volumes that can be used by services for persistent data.
    - `postgres_data:`: Declares a named volume called `postgres_data`.
  - `secrets:`: This section defines Docker secrets that can be used by services to securely store sensitive data.
    - `db_password:`: Defines a secret named `db_password` and specifies that the content of the secret will be loaded from the `db_password.txt` file in the `./secrets` directory.
    - `database_url:`: Defines a secret named `database_url` and specifies that the content of the secret will be loaded from the `database_url.txt` file in the `./secrets` directory.

# So… How do you use Docker?

So far, my blog posts haven’t been so practical in the sense that I have never dealt with how to apply the concepts I explained. While I do prioritize gaining an intuitive understanding of whatever it is I’m trying to learn, I still believe a technical implementation is what consolidates the intuition, and for me, it’s the moment when the long hours of trying to understand the concept really pay off.
Since this post isn’t exactly a step-by-step, “run this command and you’ll get xyz,” type of post, I took some commands from this Docker cheatsheet and added some extra info/explanation of what each command does. This definitely isn’t an exhaustive list, and I highly x10 recommend reading the official docs and going through some of the tutorials there 😉.

# Conclusion

In this second post on containers, we peeled back the layers on the technology, zeroing in on Docker as the go-to platform for containerization. We cleared up the whole "container vs. image" thing, using a relatable cookie-cutter analogy, and then dove into the structure of images, which are basically built from Dockerfiles—recipes for consistent application environments. The real magic, though, comes with Docker Compose, showing how it can help manage multiple containers into a cohesive application, without the headaches from using a monolithic structure for a more modular approach.

As always, I welcome any feedback on any of the content that I write – and a huge shoutout to those of you who made it to the end. Appreciate you all!
<br>
<br>
Until next time,
<br>
Ael

[^1]: This friend actually caved in and is a Mac user now -- I am so so proud of you
