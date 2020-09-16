# Using Docker Compose
In short set of steps you will:

1. Clone a Git Repo
2. cd into the directory containing our project
3. Run docker compose which will build and launch your containers


## Check out the Repository

1. On a machine with Docker installed, run the following git clone command:
   

```git clone https://github.com/nicktodd/spring-course.git```

2. Now cd into the relevant directory using the following command:

```cd spring-course/Solutions/workspace/CompactDiscMongoRestBootGradle```

3. Now run the following command:

``` docker-compose up```

4. When it is all complete, visit your machine via a browser on port 8080. Eg. if you are running this on your own computer, use:

http://localhost:8080


Using these Web pages you can add, view and delete CDs.

So what just happened!!?

## Review the Dockerfile and Docker Compose Files

1. Review the Dockerfile at https://github.com/nicktodd/spring-course/blob/master/Solutions/workspace/CompactDiscMongoRestBootGradle/Dockerfile and notice that it is an example of using an intermediate image to do the gradle build for us. It creates a new image that builds the code, and then that build jar is placed into another image which can then be launched.

2. Now review the docker-compose.yml file at https://github.com/nicktodd/spring-course/blob/master/Solutions/workspace/CompactDiscMongoRestBootGradle/docker-compose.yaml. This file essentially contains configuration that would achieve the following commands:

```
docker build -t cd/cd-service .
docker run -d --name mymongo mongo
docker run -d --link mymongo:mymongo -p 8080:8080 cd/cd-service
```

As you can see, completing these tasks in Docker compose is much easier than manually running the commands one by one.

