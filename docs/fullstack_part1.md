# Full stack application deployment - Part 1 : Back-end server deployment

## 1. Introduction
When we build the application on the production server as a Docker container, we will be connecting to a database (that we will also deploy using Docker). 

For security, **we do not want to store the live database credentials in a git repository**, so these will be defined on the server as environment variables. 

In this lab we will first **prepare our back-end application to be placed onto a live server**. We will be creating a production configuration to define the database URL and read in the credentials.

Next we will **create a Dockerfile** to specify how our application will be run on the server. 

We will make these changes to our project and ensure they are pushed to the repository. 

Then we can connect to our server where we will:
* define the database credentials as environemnt variables
* create a Docker container running the database
* clone the application repository 
* build the application
* run the application in a docker container. 

You can follow this lab with your own repository, or you can fork the sample git repository at:
https://github.com/vppmatt/fullstack-payments

**Note: This lab assumes that you are using a database called payments and that your jar file is called payments-0.0.1-snapshot.jar - adjust the sample code below if you have different names for your project.**


## 2. Create a production configuration for the Spring Boot application

1. **create a new** file called `application-production.properties` in the `src/main/resources` folder. 

The content of the file should be:

```
spring.datasource.url=jdbc:mysql://db:3306/payments
spring.datasource.username=${DBUSER}
spring.datasource.password=${DBPASSWORD}
```

Note that in the production version of the file, we are obtaining the username and password for the database from environment variables – this means that we are not storing secrets in a git repository! The connection string uses a servername of `db` – this is the name of the container running the database that we will set up in Docker. 

## 3. Define the Dockerfile

1. Create a file called `Dockerfile` in the root folder of the application with the following content:

```
FROM amazoncorretto:17

ADD target/payments-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 8080

ARG DBUSER
ENV DBUSER=$DBUSER

ARG DBPASSWORD
ENV DBPASSWORD=$DBPASSWORD

CMD ["java","-jar","-Dspring.profiles.active=production", "app.jar"]
```

This dockerfile:
-	Uses a base image containing a Java 17 JVM
-	Copies the built jar file into the Docker image (change the name of the jar file if yours is different)
-	Exposes port 8080 as the default port
-	Defines 2 arguments that can be specified when the docker file is built (we’ll see this in the next step)
-  Uses those arguments to set up some environment variables within the Docker container
-	When the container is started it runs the jar file with the required command line settings

**Ensure all your code changes are pushed to your repository before continuing**

## 4. Create a Docker network

You will need to access the linux server at this point. Your instructor will provide you with the server details and how to access it.

1. Create a network in Docker by running the following command:

```
docker network create full-stack
```

## 5. Create a MySQL database Docker container

We will be creating a Docker container to run MySQL on the server. 

1. **Define the database credentials** as environment variables on the server. Run the following commands (substitute the password for a more secure password if you wish!)

```
export DBUSER="root"
export DBPASSWORD="pass123"
```

2. **Create a new folder** to store the database files, and navigate into that folder

```
mkdir database
cd database
```

3. **Create a new file** called `Dockerfile` with the following content.  

```
FROM mysql:5
ARG DBUSER
ENV DBUSER=$DBUSER

ARG DBPASSWORD
ENV MYSQL_ROOT_PASSWORD=$DBPASSWORD

ENV MYSQL_DATABASE=payments

EXPOSE 3306
```

Note that in a real production system you would adjust this file to map the data storage to a local drive for persistence between containers.

4. **Build the Dockerfile** with the following command. Note that this will pass into the Dockerfile the database credential environment variables. Be careful to ensure you have the final period, to specify it's the Dockerfile in the current folder. 

```
docker build -t db --build-arg DBUSER=$DBUSER --build-arg DBPASSWORD=$DBPASSWORD .
```

5. **Check the image is present** by running the following command:

```
docker image ls
```

6. **Start the database** container by running the following comamnd. The command attaches the container to our network and defines the name. It also exposes port 3306 so that it can be accessed locally - this is not strictly necessary but will be useful for testing. 

```
docker run -d -p3306:3306 --name db --network full-stack db 
```

7. **Watch the logs** to see the datbase start up using the following command. Substitute [xx] with the first two characters of the container id that was displayed when you ran the previous command. Look out for the phrase "mysqld: ready for connections".

 When ready to continue press `ctrl+c`

```
docker logs -f [xx] 
```

## 6. Create the back-end Docker container

1. **Navigate to your home folder**

```
cd ~
```

2. **Clone your git repo** (substitute [xxx] with the URL of your repo)

```
git clone [xxx]
```

3. **Navigate to the project folder** (substitute [xxx] with the folder name for your project)

```
cd [xxx]
```

4. **Build the project** - the following commands will first ensure we can execute the Maven, and will then create the jar file for your project. 

```
chmod a+x mvnw
./mvnw package
```

5. **Build the Docker image** - the following command will build the image using the Dockerfile in your repository. Note that as before we are passing in the database credentials to the Dockerfile.   

```
docker image build -t payments --build-arg DBUSER=$DBUSER --build-arg DBPASSWORD=$DBPASSWORD .
```

6. **Run the docker container** - the following command will run the container. The command attaches the container to our network (so that it can find the container called "db"). It maps the application's default port 8080 to local port 8100 - we are doing this as there is other server software already running on port 8080. 

```
docker run -d -p8100:8080 --network full-stack payments
```

7. **Watch the logs** to see the application start up  using the following command. Substitute [xx] with the first two characters of the container id that was displayed when you ran the previous command. Look out for the phrase " Started PaymentsApplication in xxx seconds".

When ready to continue press `ctrl+c`

```
docker logs -f [xx] 
```

8. **Visit the swagger page** in your browser. You can test the application is running - you will need to use the IP address of your server - the url should be 

`http://xxx.xxx.xxx.xxx:8100/swagger-ui/index.html`


## Congratulations!

You have successfully deployed a Spring boot application with a database to a server using Docker!

Now continue to [part 2](fullstack_part2.md) of the lab to deploy the front-end application.