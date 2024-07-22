# Full stack application deployment - Part 2 : Front-end application deployment

## 1. Introduction
In this lab we will first **prepare our front-end application to be placed onto a live server**. We will be creating a production configuration to define the URL of the back-end Rest server.

Next we will **create a Dockerfile** to specify how our application will be run on the server. 

We will make these changes to our project and ensure they are pushed to the repository. 

Then we can connect to our server where we will:
* clone the application repository 
* build the application
* run the application in a docker container. 

You can follow this lab with your own repository, or you can fork the sample git repository at:
https://github.com/vppmatt/fullstack-payments-ui

**Before starting this lab ensure you have completed [part 1](fullstack_part1.md).** Your server should have a functioning rest application exposed at port 8100.

## 2. Create a production configuration for React application

1. **create a new** file called `.env.production` in the `src/main/resources` folder. 

The content of the file should be:

```
VITE_SERVER_URL="http://xxx.xxx.xxx.xxx:8100"
```

You will need to substitute the ip address of your server.

## 3. Create an nginx config file

We will be using nginx to serve the built React application - this requires a configuration file to work. 

1. **Create a file** in the root of the project folder called `nginx.conf` with the following content:

```
events {
  worker_connections  4096;  ## Default: 1024
}

http {
   map $http_upgrade $connection_upgrade {
       default upgrade;
      '' close;
   }

   include /etc/nginx/mime.types;

   server {
      listen 3000;

      location / {
         root /usr/share/nginx/html;
         try_files $uri $uri/ /index.html;
      }
    }
}
```


## 4. Define the Dockerfile

1. Create a file called `Dockerfile` in the root folder of the application with the following content:

```
FROM nginx:alpine

RUN rm -rf /var/cache/apk/*
RUN rm -rf /usr/share/nginx/html/*

COPY ./dist /usr/share/nginx/html

COPY nginx.conf /etc/nginx/

EXPOSE 3000

CMD ["nginx", "-g", "daemon off;"]
```

This dockerfile:
-	Uses a base image containing the nginx web server
-   Removes unnecessary / default files from the image
-	Copies the built dist folder into the Docker image 
-   Copies the nginx configuration file we created into the Docker image
-	Exposes port 3000 as the default port
-	When the container is started it runs the web server.

**Ensure all your code changes are pushed to your repository before continuing**
 

## 5. Create the front-end Docker container

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

4. **Build the project** - the following commands will first intall the latest version of node, then downlaod the node modules and then build the project.

```
nvm install node
npm install
npm run build
```

5. **Build the Docker image** - the following command will build the image using the Dockerfile in your repository. Note that we are mapping the default port of 3000 to port number 8000. 

```
docker image build -t payments-ui .
```

6. **Run the docker container** - the following command will run the container. The command maps the application's default port 3000 to local port 8000. 

```
docker run -d -p8000:3000 --network full-stack payments-ui
```

7. **Watch the logs** to see the application start up  using the following command. Substitute [xx] with the first two characters of the container id that was displayed when you ran the previous command. Look out for the phrase "Configuration complete; ready for start up".

When ready to continue press `ctrl+c`

```
docker logs -f [xx] 
```

8. **Visit the application** in your browser. You can test the application is running - you will need to use the IP address of your server - the url should be 

`http://xx.xx.xx.xx:8000`


## Congratulations!

You have successfully deployed a React application with a database to a server using Docker!

Now continue to [part 3](fullstack_part3.md) of the lab to automate Docker and create CI/CD pipelines.