## **EXPERIENCE CONTINUOUS INTEGRATION WITH JENKINS | ANSIBLE | ARTIFACTORY | SONARQUBE | PHP**

</br>

A-Install Jenkins

1 connect vscode to remote ec2 instance

![remote-ssh-config](./images-project14/remote-ssh-congig.PNG)
![]

2 install git

3 clone the repository

** `install Jenkins`**

https://www.jenkins.io/doc/book/installing

`sudo su`

```
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo

sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key

# Add required dependencies for the jenkins package EPEL release and Remirepo

sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

sudo yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

sudo yum upgrade

## install JAVA

sudo yum install java-11-openjdk-devel -y

##### open the bash profile 

vi .bash_profile 

##### paste the below in the bash profile

export JAVA_HOME=$(dirname $(dirname $(readlink $(readlink $(which javac)))))
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar

##### reload the bash profile

source ~/.bash_profile

### install jenkins

sudo yum install jenkins

sudo systemctl start jenkins

sudo systemctl enable jenkins

sudo systemctl daemon-reload

```

sudo systemctl status jenkins

![jenkins-status](./images-project14/jenkins-satus.PNG)

`open port 8080`

http://44.213.28.233:8080/

![jenkins-browser](./images-project14/jenkins-browser.PNG)


For initial jenkins admin password
 
 `sudo cat /var/lib/jenkins/secrets/initialAdminPassword` 

 install suggested plugings

 B- **Configuring Ansible For JenkinsDeployment**

1-  Navigate to Jenkins URL

2- Install & Open Blue Ocean Jenkins Plugin

![bleue-ocean](./images-project14/blue-ocea-install.PNG)

3- Create a new pipeline

![create-pipline](./images-project14/create-pipline.PNG)

Select GitHub

Connect Jenkins with GitHub

![connect-github](./images-project14/connect-github.PNG)

Login to GitHub & Generate an Access Tokenhttps://www.darey.io/wp-content/uploads/2021/07/Jenkins-Create-Access-Token-To-Github.png


Copy Access Token

Paste the token and connect

![token](./images-project14/token.PNG)

Create a new Pipeline

![pipline](./images-project14/pipline.PNG)

 Click on `Administration` to exit the Blue Ocean console.


Here is our newly created pipeline. It takes the name of your GitHub repository.

![new-pipline](./images-project14/new-pipline.PNG)

**Let us create our `Jenkinsfile`**

 Inside the Ansible project, create a new directory `deploy` and start a new file `Jenkinsfile` inside the directory.

Add the code snippet below to start building the Jenkinsfile gradually. This pipeline currently has just one stage called Build and the only thing we are doing is using the shell script module to echo Building Stage


```
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }
    }
}

```

![Jenkinsfile](./images-project14/Jenkinsfile.PNG)


Now go back into the Ansible pipeline in Jenkins, and select `configure`

Scroll down to `Build Configuration` section and specify the location of the Jenkinsfile at `deploy/Jenkinsfile`

![build-config](./images-project14/build-config.PNG)


Back to the pipeline again, this time click `"Build now"`

This will trigger a build and you will be able to see the effect of our basic Jenkinsfile configuration by going through the console output of the build.

To really appreciate and feel the difference of Cloud Blue UI, it is recommended to try triggering the build again from `Blue Ocean interface`.

Click on `Blue Ocean`

Select your project
Click on the `play button` against the branch

![buid-blueocean](./images-project14/build-blueocean.PNG)

Notice that this pipeline is a `multibranch` one. This means, if there were more than one branch in `GitHub`, `Jenkins` would have scanned the repository to discover them all and we would have been able to trigger a `build` for each `branch`.

Let us see this in action.

Create a new git branch and name it feature/jenkinspipeline-stages
Currently we only have the Build stage. Let us add another stage called Test. Paste the code snippet below and push the new changes to GitHub.

```
   pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
    }
}

```
To make your new branch show up in Jenkins, we need to tell Jenkins to scan the repository.

Click on the "Administration" button

Navigate to the Ansible project and click on `"Scan repository now"`

Refresh the page and both branches will start building automatically. You can go into Blue Ocean and see both branches there too.

![branches](./images-project14/futur-branch.PNG)

In Blue Ocean, you can now see how the Jenkinsfile has caused a new step in the pipeline launch build for the new branch.

![build-Test](./images-project14/build-Test.PNG)

A QUICK TASK FOR YOU!
1. Create a pull request to merge the latest code into the main branch
2. After merging the PR, go back into your terminal and switch into the main branch.
3. Pull the latest change.
4. Create a new branch, add more stages into the Jenkins file to simulate below phases. (Just add an echo command like we have in build and test stages)
   1. Package 
   2. Deploy 
   3. Clean up
5. Verify in Blue Ocean that all the stages are working, then merge your feature branch to the main branch
6. Eventually, your main branch should have a successful pipeline like this in blue ocean

![stages](./images-project14/blue-ocea-stages.PNG)

![cleanup](./images-project14/branch-cleanup.PNG)

**C-RUNNING ANSIBLE PLAYBOOK FROM JENKINS**

Now that you have a broad overview of a typical Jenkins pipeline. Let us get the actual Ansible deployment to work by:

1. Installing Ansible on Jenkins

2. Installing Ansible plugin in Jenkins UI

3. Creating Jenkinsfile from scratch. (Delete all you currently have in there and start all over to get Ansible to run successfully)

Note: Ensure that Ansible runs against the Dev environment successfully.

**Install ansible**

`sudo yum install ansible -y`

![ansible-v](./images-project14/ansible-v.PNG)

**Install Ansible dependencies**

```
 yum install python3 python3-pip wget unzip git -y

  python3 -m pip install --upgrade setuptools

  python3 -m pip install --upgrade pip

  python3 -m pip install PyMySQL

  python3 -m pip install mysql-connector-python

  python3 -m pip install psycopg2==2.7.5 --ignore-installed
```

### Ansible dependencies to install

**Install ansible community lib**

  **For Mysql Database**

 `ansible-galaxy collection install community.mysql`

* For Postgresql Database
  
`ansible-galaxy collection install community.postgresql`

Go to Jenkins ans install ansible plugin


