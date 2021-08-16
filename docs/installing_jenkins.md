# Installing Jenkins on Windows

1. [https://www.jenkins.io/download/](https://www.jenkins.io/download/)
2. Select Windows LTS release
3. At Service **Logon Credentials** , select **Run Service as Local System (Not recommended)**
4. VERY IMPORTANT: At **Port Selection** , enter **8081**.
5. At the JDK reference, leave it as C:\Program Files\RedHat\java-11-openjdk-11.0.8-2\.
6. At the **Custom Setup** , click **Next**.
7. Click **Next** , and click **Install**.
8. When the browser loads at the **Getting started** screen, you need to open the file with the password in. One way to do that is to copy the presented filepath to the unlock password to the clipboard, and then paste the filepath into the path bar in Windows Explorer and press enter. Select to open it with Notepad. When notepad opens,
9. Paste the copied password into the field on the web page and click **Continue**.
10. At Customize Jenkins, select **Install Suggested Plugins**. Now be patient while it installs the plugins.
11. Now create your admin user. For now we recommend using admin/admin for the username / password. Enter your email and password. Click to continue.
12. At the Jenkins URL click **Save and Finish** and then **Start using Jenkins**.
13. Before we can build a Maven project, we need to let Jenkins know where Maven is installed, so at the Jenkins homepage, click **Manage Jenkins** and then click **Global Tools Configuration**.
14. Scroll down to **Maven** , and click **Add Maven**.
15. Enter the name **Maven3.6.3** , and **de** select **Install Automatically**.
16. For the **MAVEN\_HOME** , enter **C:\Java\apache-maven-3.6.3**.
17. Click **Save**.

# Creating a Basic Maven Build

1. If you have a Maven project on your machine you can work with that. If not, clone the following Git Repository:

[https://github.com/nicktodd/basicspringapp](https://github.com/nicktodd/basicspringapp)

2. In **Jenkins** , in the menu on the left, select **New Item**.
3. At the name field, enter **MyMavenProject**.
4. Then select **Freestyle Project** and click **OK**.
5. This next screen is the configuration for our project, which will for now be minimal. Scroll down and click **Add build step**. Select &quot; **Invoke top level Maven targets**&quot;.
6. For the **Maven version** , select your **Maven3.6.3** version.
7. For the **Goals** , enter the word &#39; **package**&#39;.
8. Click **Advanced** and for the POM, enter the full path to a pom.xml file in the project you would like to have built, eg. C:\GitRepos\basicspringapp\pom.xml. YOUR PATH WILL NOT BE IDENTICAL, so check in Windows Explorer where the pom.xml file is for the project you would like to build.
9. Click **Save**.
10. Now click **Build Now**. It will say build scheduled, and you will see your new build appear at the bottom with #1 next to it. If it goes red, it failed, if it goes green, it worked! To see your build log, click on the **#1** link and then click **Console output** and you can see the logs for your build.