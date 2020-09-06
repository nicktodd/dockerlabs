# Appendix: Linking Containers
In this exercise, you will 
1. Run MongoDB in a container
2. Compile and build a C# .NET Core application using a container.
3. Run a container that has the .NET core application within it that uses the MongoDB database. 
   
You will have to link the .NET Core container to the MongoDB container.


## Before you Begin
Any installation of Docker can be used for this exercise. If you don't have Docker installed anywhere, You can use the Docker Playground

To use the playground, open a Web browser, and visit https://labs.play-with-docker.com/ and create an account. 

Once you have a terminal available on a machine that is running Docker you can proceed.

You will also need Git installed or you will need a mechanism to download the contents of a Git repository.

## Part 1: Check your Docker Installation

1. To check that Docker is installed, run the following docker CLI command:

```docker --version ```

2. You wil see a version number returned. If you see Command Not Found, check that you do indeed have Docker installed!
   
## Part 2: Retrieve the Source Code

You will be using a Docker container to compile and build this application, so let's first retrieve it from Git.

1. Run the following Git clone command:

```git clone https://github.com/nicktodd/net-enterprise-course.git```

2. Now run the following command to change directory into the one containing the Dockerfile that will be used to do the build:

```cd net-enterprise-course/labs/NetCoreWebMongo```

3. If you wish you may take a look at the Dockerfile. This file was generated using Visual Studio, and it has a number of components to it that facilitate the building of the application in one container and then using that built application in creating another image. To view the file you can run the following command:

```cat Dockerfile```

## Part 3 Build the .NET Core Application Image

1. To build the image, run the following command:

```docker build -t netcore .```

This will create a new image called netcore on your machne. It might take a while as it has to download some Microsoft images.

## Run the MonggoDB and .NET Core Containers

1. First, pull the Mongo DB docker image. This is not strictly speaking necessary as a separate step as it will happen anyway, but let's pull in manually:

```docker pull mongo```

2. Launch the container by running the following command. Note the use of a name attribute. This is very important as it will be used when we launch the .NET Core application.

```docker run --name mymongo -d mongo```

3. Finally, run the .NET Core application. Note the use of --link. This links a name used within the .NET Core application to the container running with the name **mymongo**.

```docker run -d --link mymongo:mymongo -p 8080:80 netcore```


4. That's it! You have now launched two containers, and one is linked to the other. If you would like to verify that it is working, try visiting the following URL in the browser:

http://YOURDOCKERDNSNAME:8080/api/compactdisc


You will see some empty square brackets. If you would like to post some data into the database, using your preferred REST client, post the following JSON data to the same URL:

```
{
	"cdId": 2,
	"title": "Spice World",
	"artist": "Spice Girls",
	"price": 9.99
}
```


