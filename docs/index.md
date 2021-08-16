# Welcome to the Docker and OpenShift Training Labs

## Introduction

In this set of exercises, you will be introduced to Docker and OpenShift. You will see how containerisation can be used to effectively build, deploy, monitor and maintain enterprise applications when running in containers.

The exercises take a basic Spring Boot application and containerise it with Docker, then run it on OpenShift.


## The Labs
### Lab 1 Working with Docker
In this exercise you will see the basics of running docker containers from images and how to manage the container lifecycle. 

[Try Working with Docker](module2_docker.md)

### Lab 2 Working with Images
In this exercise you will create your own Docker image from a Dockerfile that you will create. You will then run a container based upon that image and access the application through a Web browser. 

[Try Working with Images](module3_images.md)

### Lab 3 Working wih OpenShift
In this exercise, you will get your application running in an OpenShift environment.

[Try Working with OpenShift](module5_openshift.md)

### Appendix A Linking Containers Together
In this exercise, you will 
1. Run MongoDB in a container
2. Compile and build a C# .NET Core application using a container.
3. Run a container that has the .NET core application within it that uses the MongoDB database. 
   
You will have to link the .NET Core container to the MongoDB container.

[Try Linking Containers](appendix_linking_containers.md)

### Appendix B Running a Spring Boot Application on Docker and OpenShift 4 using Fabric8   
In this exercise, you will use Fabric8 which is a Maven plugin that can build a Docker image for a Spring Boot application without a Dockerfile at all! You can try it out here:
[Try Using Fabric8](https://bitbucket.org/lightspeed/dockertraining/src/master/docs/)

### Appendix C Linking Containers using Docker Compose
In this simple exercise you will see how Docker compose can be used to build and deploy an application that involves two containers.
[Try Using Docker Compose](docker-docker-compose.md)

# Jenkins

## Lab 1 Installing Jenkins and Running a Basic Project
In this exercise, you will install Jenkins on a Windows VM, and then install and build a basic Maven project on it.

[Installing Jenkins](installing_jenkins.md)

## Lab 2 Using Jenkins Remotely with a Jenkinsfile
In this exercise, you will not be using your local Jenkins, but rather a remote pre-configured Jenkins environment. It will be used to build and deploy a Java SpringBoot application to OpenShift.

[Using Jenkins to Deploy to OpenShift](modulexx_using_jenkins.md)

