# Full stack application deployment - Part 3 : Automation and pipelines

## 1. Introduction
In this lab we will use Docker-compose to bring together our 3 containers into a single **docker-compose configuration**. This will allow us to start and stop all 3 with a single command.

Next we will **define pipelines** to be used by the Jenkins build tool. 

We will **configure Jenkins** to monitor our git repositories.

Then we can test our CI/CD processes by commiting code changes.

**Before starting this lab ensure you have completed [part 1](fullstack_part1.md) and [part 2](fullstack_part2.md).** Your server should have a functioning front-end application exposed at port 8000.

## 2. Use Docker-compose

1. **Stop** and remove the current Docker containers with the following commands:

```
docker stop $(docker stop -a -q)
docker rm $(docker stop -a -q)
```

2. **Create a new** file called `docker-compose.yaml` in the home folder. (Navigate to the home folder with `cd ~`)

The content of the file should be:

```
networks:
  full-stack:

services:

  db:
    image: db
    networks:
      - full-stack
    ports:
      - 3306:3306
    healthcheck:
      test: ["CMD", "mysqladmin", "-u${DBUSER}", "-p${DBPASSWORD}",  "ping", "-h", "localhost"]

  payments:
    image: payments
    networks:
      - full-stack
    ports:
      - 8100:8080
    depends_on:
      - db

  payments-ui:
    image: payments-ui
    networks:
      - full-stack
    ports:
      - 8000:3000
```

3. **Create a new file** in the same folder as the docker-compose.yaml file called `.env` - this file will contain the environment variables and will be ready by docker compose. Give this file the following content:

```
DBUSER=root
DBPASSWORD=pass123
```


Note that the healthcheck command defined in the database together with the depends_on in the payments application will ensure the database is running before starting the payments back-end.

3. **Start** all the Docker containers with 

```
docker-compose up -d
```

4. **Watch the logs** to see the applications start up  using the following command.  

When ready to continue press `ctrl+c`

```
docker-compose logs -f 
```

**Note: The healthcheck/depends_on option is unreliable in docker-compose. If the application fails to start, simply re-run the docker compose command to re-start any failed containers***

5. **Visit the application** in your browser. You can test the application is running - you will need to use the IP address of your server - the url should be 

`http://xx.xx.xx.xx:8000`


## 3. Configure credentials in Jenkins

Before we create the Jenkins pipeline, we will configure the database credentials in Jenkins so that these can be passed to the build script.

**Note: To keep this lab simple ensure your git repository is publically readble (not a private repo). **

1. **Visit the Jenkins web page** in your browser. You will need to use the IP address of your server - the url should be 

`http://xx.xx.xx.xx:8080`

2. **Log into Jenkins** using the username 'admin' and the password supplied by the instructor.

3. From the Jenkins home page, click on **Manage Jenkins**.

4. In the **Security** section click on **Configure credentials**

5. Under the **Domain** column click on **global**.

6. Click on the **+ Add Credentials** button.

7. Set the **Kind** to **Secret text**

8. Enter the database username (root) in the **Secret** and set the **ID** to DBUSER.

9. **Repeat the process** to store the DBPASSWORD.


## 4. Create the back-end Jenkins pipeline

1. In the Spring Boot application, **create a file** called `Jenkinsfile` in the root of the application with the following content. Substitute your git repository URL in the first section

```
pipeline {
    agent any
    environment {
            GIT_URL = "https://github.com/xxx"
            IMAGE_NAME = "payments"
            DBUSER = credentials('DBUSER')
            DBPASSWORD = credentials('DBPASSWORD')
        }
    stages {
        stage('GetFromGithub') {
            steps {
                git branch: 'main', url: GIT_URL
            }
        }
        stage('Ensure Maven is runnable') {
            steps {
                sh 'chmod a+x mvnw'
            }
        }
        stage('Maven Unit tests') {
            steps {
                sh './mvnw test'
            }
        }
        stage('Maven build') {
            steps {
                sh './mvnw clean package'
            }
        }

        stage('Docker image build') {
            steps {
                sh 'docker image build -t${IMAGE_NAME} --build-arg DBUSER=$DBUSER --build-arg DBPASSWORD=$DBPASSWORD .'
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

3. Click on **Create a job**

4. Give the job a **name** such as "payments back end", click on **Pipeline**, then click on **OK**.

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


## 5. Create the front-end Jenkins pipeline

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