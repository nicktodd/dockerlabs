# Welcome to the Docker and OpenShift Training Labs

## Introduction

In this set of exercises, you will be introduced to Docker and OpenShift. You will see how containerisation can be used to effectively build, deploy, monitor and maintain enterprise applications when running in containers.

The exercises take a basic Spring Boot application and containerise it with Docker, then run it on OpenShift. We also run a second React based application in Openshift and link the two together.

## The Labs
### Lab 1: Working with Docker
In this exercise you will see the basics of running docker containers from images and how to manage the container lifecycle. 

[Try Working with Docker](module2_docker.md)

### Lab 2: Working with Images
In this exercise you will create your own Docker image from a Dockerfile that you will create. You will then run a container based upon that image and access the application through a Web browser. 

[Try Working with Images](module3_images.md)

### Lab 3: Working with OpenShift
In this exercise, you will get your application running in an OpenShift environment.

[Try Working with OpenShift](module5_openshift4.md)

### Lab 4: Deploying with pipelines
In this exercise, you will deploy two applications to the OpenShift cluster, with a pipeline to automate the build and deployment of code changes. To undertake this lab, you need to have your own account with either Github or Bitbucket - choose the version of the lab.

[Try Deploying with pipelines (Github version)](module6_openshift4_pipelines.md)

[Try Deploying with pipelines (Bitbucket version)](module6_openshift4_pipelines_bitbucket.md)


### Introduction to Openshift 4 Lab
This is a cut-down version of lab 4 which is designed to be used for half-day Clould/Openshift workshops, where the students are not completing any other labs. Do not attempt this lab if you are working through the above!

[Try Introduction to Openshift 4](openshift_intro_lab.md)

### Lab 5: Full stack application deployment with docker-compose and Jenkins

This is a lab which will guide you through deploying a full stack application (MySql database, Spring Boot back-end and React front-end) onto a Linux server. The Applications run with docker-compose and a CI/CD pipeline is created using Jenkins. 

This lab should be compelted after labs 1 & 2.

[Try Full stack application deployment with docker-compose and Jenkins](fullstack_intro.md)