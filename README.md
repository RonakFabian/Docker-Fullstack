# Dockerizing a MERN Stack Web Application

### Using Docker Containers to build Web Application

![Image Source: [Google Images](https://sujaykundu.com/building-mern-apps-using-docker)](https://cdn-images-1.medium.com/max/2000/1*JJFt8gRBPBCjSwNYqhP3UA.png)

The MERN stack is becoming increasingly popular and could be a powerful stack to figure in. Therefore having the ability to build and deploy good MERN applications, greatly helps career prospects as a developer.

## What is a container?

> “A container is the standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another.”

This tool becomes very useful during the development phase. As many developers are involved in the process, it often becomes a hefty task of setting up the environment to run the project, as each project comes with their list of dependencies along with the specified versions.

A **Docker** container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings. **Container images** become **containers** at runtime and in the case of Docker containers — images become containers when they run on Docker Engine.

## Docker Hub

Docker Hub is a cloud-based repository service provided by Docker in which users create, test, store and distribute container images. Through Docker Hub, a user can access public, open-source image repositories, as well as use space to create their own private repositories, automated build functions, webhooks and workgroups.

## Docker File: Client

```yml
    # Dockerfile for React client

    # Build react client
    FROM node:10.16-alpine

    # Working directory be app
    WORKDIR /usr/src/app

    COPY package*.json ./

    ###  Installing dependencies

    RUN npm install --silent

    # copy local files to app folder
    COPY . .

    EXPOSE 3000

    CMD ["npm","start"]
```

We can simply build our Frontend with this command: `docker build -t react-app .`

To verify everything is fine, we run our newly built container using the command:`docker run -p 3000:3000 react-app` . This will run just the Frontend.

## Docker File: Server

```yml
 #  Dockerfile for Node Express Backend

    FROM node:10.16-alpine

    # Create App Directory
    RUN mkdir -p /usr/src/app
    WORKDIR /usr/src/app

    # Install Dependencies
    COPY package*.json ./

    RUN npm install --silent

    # Copy app source code
    COPY . .

    # Exports
    EXPOSE 5000

    CMD ["npm","start"]
```

We can simply build our Backend with this command: `docker build -t node-app .`

## Docker Compose

> “Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration.”

```yml
version: "3.7"

services:
  server:
    build:
      context: ./server
      dockerfile: Dockerfile
    image: myapp-server
    container_name: myapp-node-server
    command: /usr/src/app/node_modules/.bin/nodemon server.js
    volumes:
      - ./server/:/usr/src/app
      - /usr/src/app/node_modules
    ports:
      - "5000:5000"
    depends_on:
      - mongo
    env_file: ./server/.env
    environment:
      - NODE_ENV=development
    networks:
      - app-network
  mongo:
    image: mongo
    volumes:
      - data-volume:/data/db
    ports:
      - "27017:27017"
    networks:
      - app-network
  client:
    build:
      context: ./client
      dockerfile: Dockerfile
    image: myapp-client
    container_name: myapp-react-client
    command: npm start
    volumes:
      - ./client/:/usr/app
      - /usr/app/node_modules
    depends_on:
      - server
    ports:
      - "3000:3000"
    networks:
      - app-network

networks:
  app-network:
    driver: bridge

volumes:
  data-volume:
  node_modules:
  web-root:
    driver: local
```

## **Creating the Build**

To create the build for the entire application, we need to run the following command: `docker-compose build`

## **Starting the Services**

We can start the multi-container system using the following simple command: `docker-compose up`

At last, we can open `http://localhost:3000` to see our React Frontend.

The backend server is live on `http://localhost:5000`

And MongoDB is running on `http://localhost:27017`

## Maintenance & Inspection

Inspect running services using the `docker-compose ps` command.

## Stopping the containers

To stop all the services, we use `docker-compose stop`.

Using `docker-compose down --volumes` brings everything down, removing the containers entirely, with the data volume of the services.

**Finally, we have successfully dockerized our E-Commerce Web app.**
