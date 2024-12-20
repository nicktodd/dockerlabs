# Full stack application deployment - Part 3 : Automation and pipelines

## 1. Introduction
In this lab we will use Docker-compose to start and stop the container.

Next we will **define pipelines** to be used by the Jenkins build tool. 

We will **configure Jenkins** to monitor our git repositories.

Then we can test our CI/CD processes by commiting code changes.

**Before starting this lab ensure you have completed [part 2](fullstack_part2.md).** Your server should have a functioning front-end application exposed at port 8000.

## 2. Use Docker-compose

1. **Stop** and remove the current Docker containers with the following commands:

```
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

2. **Create a new** file called `docker-compose.yaml` in the home folder. (Navigate to the home folder with `cd ~`)

The content of the file should be:

```
networks:
  full-stack:

services:

  payments-ui:
    image: payments-ui
    networks:
      - full-stack
    ports:
      - 8000:3000
```

3. **Start** the Docker container with 

```
docker-compose up -d
```

4. **Watch the logs** to see the applications start up  using the following command.  

When ready to continue press `ctrl+c`

```
docker-compose logs -f 
```

5. **Visit the application** in your browser. You can test the application is running - you will need to use the IP address of your server - the url should be 

`http://xx.xx.xx.xx:8000`


## 3. Start using Jenkins

**Note: To keep this lab simple ensure your git repository is publically readble (not a private repo). **

1. **Visit the Jenkins web page** in your browser. You will need to use the IP address of your server - the url should be 

`http://xx.xx.xx.xx:8080`

2. **Log into Jenkins** using the username 'admin' and the password supplied by the instructor.

## 4. Create the front-end Jenkins pipeline

1. In the React application, **create a file** called `Jenkinsfile` in the root of the application with the following content. Substitute your git repository URL in the first section

```
pipeline {
    agent any
    environment {
            GIT_URL = "https://github.com/xxx"
            IMAGE_NAME = "payments-ui"
        }
    stages {
        stage('GetFromGithub') {
            steps {
                git branch: 'main', url: GIT_URL
            }
        }
        stage('Application build') {
            steps {
                nodejs(nodeJSInstallationName: 'NodeJS') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Docker image build') {
            steps {
                sh 'docker image build -t${IMAGE_NAME} .'
            }
        }

        stage('Update docker-compose to use the new version') {
            steps {
                sh 'sudo -u student /usr/local/bin/docker-compose -f /home/student/docker-compose.yaml up -d'
            }
        }
         
    }
}

```

2. **Visit the Jenkins web page** in your browser. 

3. Click on **Create a job**.

4. Give the job a **name** such as "payments front end", click on **Pipeline**, then click on **OK**.

5. Tick the box for **Github project** and paste the url of your back-end repository into the box provided.

6. Click on **Poll SCM** and enter the following in the schedule box - this will check your repository for changes every 10 minutes. 

```
H/10 * * * *
```

7. In the pipline section, change the definition to **Pipeline script from SCM**.

8. Set the **SCM** to **Git**

9. Paste the url of your back-end repository into the box provided.

10. Change the **Branches to build** box to **main**.

11. Ensure the script path says **Jenkinsfile**.

12. Click on **Save**.

13. Run the build process by clicking on **Build Now**.

14. **View the console output** to check that the build runs successfully. You should also montior the docker-compose logs on the server to see the application restart!

## 6. Test the CI/CD pipelines

1. Make some changes to the code and commit them to the git repositories. Wait for the Jenkins pipelines to rebuild the applications!

## Congratulations!

You have successfully created a CI/CD pipeline using Jenkins and docker-compose!
