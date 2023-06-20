# Lab 2 - Working with Docker Images
In this exercise, you will now see how to work with Docker images. Specifically you will see how to manage images that are already on your machine and also how to create and then work with your own images from Dockerfiles. We will take a sample Spring Boot application and get it into an image so we can run it as a container. A bit like the container you launched in the previous exercise.

## Before you Begin
You will need to be logged into a machine that has Docker installed, or you will need to be logged into the Docker playground.

## Part 1: Clone the Git Repository containing our application and Review the Code
The first thing you will need to do is to get hold of the source code for our simple Spring Boot application. We can do this with a git clone commmand.


1. At your terminal where you are logged into your VM, run the following git clone command:

```git clone https://github.com/vppmatt/paymentgateway-standalone.git```

This will download the Git repository containing the source code for our application. 

2. The application code will be placed in a new folder - navigate into that folder with 

```cd paymentgateway-standalone```

3. Now run the ls command. You will see a standard Java Maven project, ie. a pom.xml file and an src folder. 

```ls```

4. Let's take a look at the code itself, you can do this from your terminal using a command like cat, or alternatively, you can visit the repository in the Web browser and look at the code directly on Github.

5. Either using the terminal, or the Web browser, review the following Java file:
  
<https://github.com/vppmatt/paymentgateway-standalone/blob/main/src/main/java/com/multicode/payments/control/CountriesController.java>

Note that the controller here is going to be handling GET requests to `/api/country`. If you are unfamiliar with Java REST APIs, you will see the line at the top of the file:

```@RequestMapping("/api/country")```

This is the line that control the URL pattern. There is then a method a little lower down structured as follows:

```
 @GetMapping(...)
 public List<String> getAllCountries() {
     return service.getAllCountries();
 }
```
6. To be able to run the application from the command line (and in a Docker container) we first need to build a jar file from it. 

You can do this from the command line. First make sure that the mvnw command is runnable with 

```chmod a+x mvnw```

Then create the jar file with

```./mvnw clean package```

This will take a few minutes to run as it will need to download from the internet the dependencies for the application. It will eventually say "BUILD SUCCESS".

7. You can check that the jar file has been created - it will be in a folder called target and will be called `payments-0.0.1-SNAPSHOT.jar`.

## Part 2 Create the Dockerfile

1. From the terminal, ensure that you are in the root `paymentgateway-standalone` folder of the project, so you are in the folder with the pom.xml and the src directory.

You will now create and edit a Dockerfile. The instructions will use the nano editor, but you can use something else if you prefer. 

2. Now to create your own Dockerfile, run the following command (note that this assumes you are going to use nano. Feel free to create the file some other way if you prefer):

```nano Dockerfile```

You will be presented with the editor view. 
   
3. Now we can begin to edit our Dockerfile. The first thing we need to specify is the parent image. For this we will use a standard Citi image that already includes the JRE. So add the following line to your Dockerfile.

```FROM openjdk:8```

4. Now we need to get the JAR file copied into the image, so we can use an ADD statement to do that. Add in the following line:

```ADD target/payments-0.0.1-SNAPSHOT.jar app.jar```

This will copy the JAR over from the target folder into the image with the name app.jar.

5. To specify which port will need to be exposed upon launch, we can add an EXPOSE line, so add the following line to your Dockerfile:

```EXPOSE 8080```

Remember, this does not actually do anything with the ports, but it will help anyone using the image to know which port to open, and as you will see later, OpenShift also uses this information.

6. Finally, we need to specify what happens when the container launches, which is to run the Jar, so we can add this as either an ENTRYPOINT or as a CMD. We will use an ENTRYPOINT, so add the following line:

```ENTRYPOINT [ "sh", "-c", "java  -jar /app.jar" ]```

This line specifies to run a shell with the java command, specifying the fact it is a jar with -jar and then specifying the name of the Jar file. 

7. That's it. The Dockerfile is complete. It should appear as shown below. Just verify your file contents:

```
FROM openjdk:8
ADD target/payments-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 8080
ENTRYPOINT [ "sh", "-c", "java  -jar /app.jar" ]
```

8. To save the file, press **Ctrl+O**, then press **Enter**. To exit the editor, press **Ctrl+x**. 
    
## Part 3 Build the Image and Run the Container

1. If you are working within a corporate environment, you would ordinarily need to login to the registry containing the parent image. However, since we are using the public Internet, Docker will assume that you wish to use the public images held on the Dockerhub registry. This is the type of command you would need to run if you were within a corporate envrionment. You DO NOT need to run this now though:

```docker login  location.of.corporate.docker.registry.do.not.run.this.now```

2. To build the image, we can use **docker build**. Remember we must specify a tag and then also the location of the Dockerfile. Since we are in the folder of the Dockerfile you can simply run the following:

```docker build -t payments .```

This will download the parent image that we specified at the top of the Dockerfile, the along with all of its layers. Once that is complete it will then build the image for you.

The output will look something like the below:

![docker build](img/docker-build.png)

3. Now the image is built, we can test it by running it as a container. To run it we can use an almost identical command to the one we used in Module 2. Test it with the following:

```docker run -p 8080:8080 -d payments```

The terminal will respond with the id of the container. 

4. You can view the logs from the container by running the command 

```docker logs [2 digits]```

5. After a short while the application should be running. To test it in the browser, now visit the following URL:

```http://[hostname]:8080/api/country```

You can also visit the root url for the application to view the API documentation.

```http://[hostname]:8080```


## Part 4 Manage the Images
Finally, let's see how you can manage the images you now have on your machine.

1. Run the docker images command to see what images are currently downloaded on your VM.

```docker images```

2. You will see the OpenJDK image and the payments images. You may also see the images from earlier exercises.

3. Optionally, you can remove your payments image. Try removing it now with the following command:

```docker rmi payments```

4. It will not remove it because you have a running container using the image. We can force it, but let's terminate the container and then remove the image. You will need to find out your container ID (use docker ps)  and then run the following three commands:

```docker stop [2 digits]``` - this will stop the container

```docker rm [2 digits]``` - this will delete the container

```docker rmi compactdiscs``` - this will remove the image

Now it will have successfully removed your image. You can test this using `docker images`, and the payments image will no longer be there.

5. If you are using docker on a machine that you are regularly using docker on, then you will often want to do this kind of thing to prevent your hard drive filling up with unused images. You can run this command to remove all unused images:

```docker image prune -a```

 Or this command will remove all unused images and all stopped containers:

```docker system prune -a```

Try running this last command now to clean up the workspace.

## Review
Congratulations. You have successfully created a Dockerfile and then used that file to create a docker image. You then launched that image as a container, and was able to view it running via a Web Browser. 

You also saw how you can manage images using **docker images**.

Finally, you may be wondering why we didn't push our image to the registry. It can be done with docker push, but we would need the relevant permissions to do that. 

In the next module, you will see how we can get this image running in OpenShift instead of using Docker standalone.