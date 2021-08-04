# Use Jenkins to Deploy an Application to OpenShift

In this exercise you will use Jenkins to build a Spring Boot application and then deploy it to an OpenShift Server.

## Prerequisites

In order to complete this exercise, you must already have the following

* A Java SpringBoot application in a Git repository
* A Dockerfile that can be used to successfully package up the application in Docker. If this cannot be demonstrated on your local computer, then it will not work when you try and deploy it via Jenkins
* The Docker image should have successfully been run on OpenShift before

If you can tick all of those boxes, then you are good to go!

## Part 1 Create the Jenkinsfile

1. Open your Java SpringBoot project in your preferred IDE.
2. Your project requires a Jenkinsfile that is typically within the root of the project folder structure, so create a blank Jenkinsfile in your project.
3. Within the Jenkinsfile add the following:

```
def projectName = 'demospring'
def version = "0.0.${currentBuild.number}"
def dockerImageTag = "${projectName}:${version}"

pipeline {
  agent any

  stages {
     stage('Build docker image') {
          // this stage also builds and tests the Java project using Maven
          steps {
            sh "docker build -t ${dockerImageTag} ."
          }
      }
    stage('Deploy Container To Openshift') {
      steps {
        sh "oc login https://localhost:8443 --username admin --password admin --insecure-skip-tls-verify=true"
        sh "oc project ${projectName} || oc new-project ${projectName}"
        sh "oc delete all --selector app=${projectName} || echo 'Unable to delete all previous openshift resources'"
        sh "oc new-app ${dockerImageTag} -l version=${version}"
        sh "oc expose svc/${projectName}"
      }
    }
  }
}


```

4. Commit and push your changes to your Git repository.


## Part 2 Create the Jenkins build
You have been provided with a Linux machine that already has both Jenkins and Openshift installed on it. You will use this machine to complete the exercise.

1. Using a browser, visit your Jenkins server URL, it will be something like: http://MACHINENAME:8080

2. At the Jenkins interface, login using the credentials provided by your instructor.

![Jenkins New Item](img/jenkins-new-item.png)

3. In the Jenkins console, click **New Item**.

4. At the **Enter an Item Name** field, enter `MySpringProject` and then select **Pipeline** and click **OK**.

5. At the **General** page, scroll down to the **Pipeline** section and at the **Definition** field, select **Pipeline script from SCM**.

6. For the **SCM**, select **Git**, and in the **Repository URL** enter your Git repository location. If it not public, add the Credentials.

7. At the **Branches to build** section, change the branch name from `*/master` to `*/main`.

![Jenkins Git Configuration](img/jenkins-git.png)

8. Deselect the **Lightweight checkout** option.

9.  Now click **Save**.

## Part 3 Run the Jenkins build

1. At the **Pipeline** screen, you can now click **Build Now**.

2. You will see a series of (hopefully) three green squares as it completes the three stages of the Jenkins build.

![Running a build](img/jenkins-build.png)



