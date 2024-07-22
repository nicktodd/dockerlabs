# Full stack application deployment with docker-compose and Jenkins Training Lab

## Introduction

In this set of labs, you will deploy a full-stack application to a Linux server, and set up a Jenkins CI/CD pipeline.

## Prerequisites

You can run this lab using your own Spring Boot and React applications, or you can fork the sample applications provided for each lab. 

You will need to have access to a Linux server, configured with Docker, docker-compose and Jenkins. Your instructor will provide you with the details of how to access this server.

You should be familiar with how to build and run Docker images - review labs 1 and 2 for further guidance if required.

The lab in split into 3 parts:

### Part 1 - Back-end server deployment

In this lab you will:
* Prepare your Spring Boot application for deployment to a live server by creating a production version of your application.properties file
* Create a Dockerfile to define how the application will be run on the server
* Set up a Docker network on the server
* Define the database secrets (the database username and password) as environment variables on the server
* Create a MySQL database Docker container instance 
* Create the application container instance
* Test the application by visiting the swagger page from your browser

[Start part 1](fullstack_part1.md)

### Part 2 - Front-end app deployment

In this lab you will:
* Prepare your React application for deployment to a live server by creating a production version of the .env file
* Create a Dockerfile to define how the applicaiton will be run on the server
* Create the application container instance
* Test the application by visiting the browser URL.

[Start part 2](fullstack_part2.md)

### Part 3 - Automation and pipelines

In this lab you will:
* Define a docker-compose.yaml file that brings up all 3 containers
* Define Jenkins files for the 2 applications containing a pipeline that specifies how each application is to be tested, re-built and re-deployed on the server when a code change happens
* Configure Jenkins to monitor the git repositories and run the pipelines when code changes take place
* Test the Jenkins pipelines are running correctly.

[Start part 3](fullstack_part3.md)