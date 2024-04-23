---
title: "Creating multiple containers using Docker"
---


In this blog, I'm excited to share my journey of deploying my old project to Docker. Docker has become a fundamental tool for developers, allowing us to encapsulate our applications into containers that can run consistently across different environments.We'll start with an introduction to Docker and gradually move towards containerizing our application into multiple containers.

## Table of Contents

1. [What is Docker?](#introduction-to-docker)
2. [Why to Containerize?](#why-containerize)
3. [Requirements](#requirements)
4. [Getting Started](#getting-started)
5. [Containerizing a Full-Stack 3 tier Application](#containerizing-a-full-stack-application)
   - [The Frontend](#the-frontend)
   - [The Backend](#the-backend)
   - [The Database](#the-database)


## Introduction to Docker

Docker is a platform designed for developing, packaging, and running applications within containers. These containers are lightweight, portable, and self-contained environments that include everything necessary to run an application, such as code, runtime, system tools, and libraries. Docker ensures a consistent environment across various systems, simplifying the development and deployment of applications.

Docker Compose extends Docker's capabilities by simplifying the management of multi-container applications. It enables the definition and execution of multi-container Docker applications using a YAML configuration file (docker-compose.yml). This file allows you to specify the services, networks, and volumes required by your application in a single declarative format, enhancing the clarity and maintainability of your application's configuration.

A significant advantage of Docker Compose is its ability to orchestrate the startup and shutdown of multiple containers with a single command. By executing `docker-compose up`, Docker Compose reads the configuration file and starts all the containers defined within it, automatically configuring the necessary networks and volumes. Conversely, `docker-compose down` stops and removes all containers, networks, and volumes associated with the application.

Moreover, Docker Compose offers functionality for managing service dependencies, environment variables, and override files. You can define dependencies between services to ensure that they start in the correct order and can communicate effectively. Additionally, Docker Compose supports environment variables and override files, enabling you to customize your application's configuration for different environments (e.g., development, staging, production).

## Why Containerize?

Containerization provides numerous advantages for application development and deployment:

- **Uniformity** : Containers guarantee uniform environments throughout the development, testing, and production phases.
- **Segregation**: Each container operates autonomously, segregating applications from both one another and the underlying operating system.
- **Mobility**: Containers are deployable on any Docker-compatible system, facilitating effortless application migration across environments.

- **Flexibility**: Containers can be swiftly adjusted in scale to accommodate fluctuating workload requirements.

- **Efficiency**: Containers are lightweight and resource-efficient, enabling optimal resource utilization and cost-effectiveness.

- **Security**: Containers offer enhanced security by encapsulating applications and their dependencies, reducing the attack surface compared to traditional deployments.

## Requirements:

1.  In this demo, I am deploying my latest fullstack application with React(vite) in the frontend, Nodejs in backend and MongoDB as the database
2.  Docker engine or Desktop
3.  Text Editor or IDE 
4. Docker compose 


## Getting Started:

Clone the repository from [this repo](https://github.com/DarshanLaljani/coding-platform.git) ( **Note** You need to add docker files as per instructions below ). This was a project made by me and my friend [Aniket Kumar Suthar](https://github.com/Aniket-Suthar) for our Advance Web tech Project. Run the command "npm install" inside the Backend and Frontend Folder to install the dependencies. 

    npm install

Here is what the folder structure should look like for BACKEND - 

![My Image](../assets/images/Backend_struc.png){:height="350px" width="500px"}

Here is what the folder structure should look like for FRONTEND - 

![My Image](../assets/images/Frontend_struc.png){:height="350px" width="500px"}

## Containerizing a Full-Stack Application: 
After cloning the repo and installing the needed dependencies, we are going to start by building an image for the frontend and backend. 

1. #### The Frontend:

    Create a file named Dockerfile in the Frontend folder, and put the following content inside it.

        FROM node:latest
        WORKDIR /app
        COPY . .
        RUN npm install
        EXPOSE 8080
        CMD ["npm", "run", "dev","--","--host","0.0.0.0"]

    So lets have a look at this piece of code line by line - 

    1. FROM node:latest - it specifies that the base image will be built upon the latest version of node
    2. WORKDIR /app - It sets the /app within the container as the working directory. All the subsequent instructions below this will be executed within this directory. 
    3. COPY . . - it instructs docker to copy all the directory and files, in the directory dockerfile is present to the working directory in the container, which is /app. 
    4. RUN npm install - This command is run when the container is being built. This installs all the dependencies necessary to run the container successfully. 
    5. EXPOSE 8080 - It specifies that the container will listen on the specified port(s) at runtime. In our case it is 8080
    6. CMD ["npm", "run", "dev","--","--host","0.0.0.0"] - This command is run when the container starts. It specifies to start the vite server, which serves our frontend, and to listen on all available network interfaces. 


    After creating the dockerfile for frontend, it is now time to build the image. Open the terminal in your IDE and run the following commands - 

        cd  FRONTEND
        docker build -t darshanlaljani/21bcp232fend .
    
    Here you can replace darshanlaljani/21bcp232fend with any name you want. This will be the name of our image. Typically if you have created a docker account image of the name starts with {username}/{tag}. So it will be easier to push to remote repositories. After executing this commands, docker takes a minute to create the image. Run the following command to check if the docker image was created successfully - 

        docker images
    
    And you would be listed with all the docker images like so - 
    Now, to run the image in the terminal simply run the following code - 

        docker run -p 8080:8080 darshanlaljani/21bcp232fend
    
    Replace the name of image with yours. The p flag is used to expose the container port 8080 to the host system's 8080 port, so that we can access it outside the container. Try it out in your browser by typing "http://localhost:8080" and see the results!

    ![My Image](../assets/images/MyFrontend.png) 

    You wont be able to do anything on this app, as the backend server is not up yet. So lets do it, and using docker!!!

    Now lets stop the container for now. Run the following commands to view all running containers. 

        docker container ls

    Copy the container id of the container and run the following command to stop it

        docker container stop {cont_id}  
    
2. ### The Backend: 

    Containerizing of the backend is just same as that of the frontend. Create a docker file in the Server directory and put the following piece of code in it -

        FROM node:latest
        WORKDIR /app
        COPY . .
        RUN npm install
        EXPOSE 5001
        CMD ["npm", "run", "start","--","--host","0.0.0.0"]

    The only difference is in the port and the run command. The backend will be exposed on the 5001 port. 

    Now the build command is the same too, just run the same command as we did in frontend and you are good to go! But dont forget to change your present working directory to Server in the terminal before running the command. 

        docker build -t darshanlaljani/21bcp232bend .
    
    To verify that the image was built, run the command - 

        docker images
    

    Now start the container using the command as we did before. But this time it will be on port 5001 instead of 5000.

        docker run -p 5001:5001 darshanlaljani/21bcp232bend

    Now the server is not serving or rendering anything, so run the frontend container too, to see the overall result. Now, we would be able to login and do some stuff. 

    ![My Image](../assets/images/MyBackend.png) 

    Now still some features like adding reviews, reporting bugs would be locked as, we the database is inaccessible! Let us look at how we can containerize mongodb as well using docker compose. But first of all stop all the containers, as starting and stopping of containers will be handled by docker compose itself! Lets look at that in the next section

3. ### The database: 

    The good part is, we dont need to build an image for the database. We need to simply download the official image from remote repository. We dont even need to go to any site or do any git clone or anything like that to download it. All will be handled by docker itself! Let us look at how. 

    1. Create a docker-compose.yml in the main working directory of the project and put the following piece of code in it - 

            version: '3'

                services:
                frontend:
                    image: darshanlaljani/21bcp232fend
                    ports:
                    - "8080:8080"

                backend:
                    image: darshanlaljani/21bcp232bend
                    ports:
                    - "5000:5001"
                    depends_on:
                    - mongodb
                    environment:
                    - MONGO_URI=mongodb+srv://explorerdarshanlaljani:Darshan2001@cluster0.6caipl2.mongodb.net/test

                mongodb:
                    image: mongo
                    container_name: mongodb
                    ports:
                    - "27017:27017"
                    environment:
                    - MONGO_INITDB_DATABASE=test
                    - MONGO_INITDB_ROOT_USERNAME=user
                    - MONGO_INITDB_ROOT_PASSWORD=password

        In the services part we list all the images required with their configuration. We see that, we already include our backend and frontend images, with their exposed ports specified. Now, in backend we observe that there is an extra field 'depends_on'. What does it mean? Well when we start the multi-container which we will see in the future how, the dependencies of a service are started first. Thats it. It does not affect any form of inter-service communication.

        The third service, we have included is mongo. But we do not remember creating any mongo image or downloading it from any remote repo, right? Because docker will handle all that from us. First, it will search in our local repo, if an image named mongo exists, it will simply use that, or else it will download the image from the remote repository and run it in a container named mongodb, as specified in the 'container_name' field. Next we pass some environment variables,
        so we can connect to our mongodb cluster and database. Let us see how we can execute this docker-compose file
    
    
    2. Now to create a multi-container container, simply in your terminal change your present working directory to the directory where the docker-compose.yml is present and run the command-

            docker compose up
    
    ![My Image](../assets/images/Compose.png){:height="550px" width="600px"}


    Now run the following command to view all the running containers - 



            docker container ls
        
    We observe that there are 3 containers running - one for the backend, one for the frontend and one for the database!!!

    ![My Image](../assets/images/Lscommand.png){:height="550px" width="600px"}


4. Now visit 'http://localhost:8080/' in your browser, to access the whole applications, with all
    of its features unlocked!!

5. To stop the multi-container container simply run the command - 

        docker compose down

    ![My Image](../assets/images/Composedown.png){:height="550px" width="600px"}

6. We can also observe that docker has automatically removed all the 3 containers. You can verify it by running the command - docker container ls

    ![My Image](../assets/images/Lsoff.png){:height="550px" width="600px"}



###   CONGRATULATIONS YOU HAVE SUCCESSFULLY CONTAINERIZED A FULL-FLEDGED WEB-APP!!







    

