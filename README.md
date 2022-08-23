# CI-CD-GCP-MAVEN-JENKINS-TOMCAT

Step: 1 Configure the Jenkins System:

- Installation on Java on Jenkins.
```
  sudo su - 
  apt update
  apt install openjdk-8-jdk -y
  # Or you can run 
  apt install openjdk-8-jre-headless
  # check java version
  java -version
```


- Configuring the maven on jenkins server:

```
  apt update
  cd /opt
  wget https://dlcdn.apache.org/maven/maven-3/3.8.6/binaries/apache-maven-3.8.6-bin.tar.gz 
  #replace with latest version from  this link: https://maven.apache.org/download.cgi
  
  tar -xvzf apache-maven-3.8.6-bin.tar.gz
  mv apache-maven-3.8.6-bin maven
  
  #set maven path of gcp ubuntu server for jenkins
  vi /etc/profile
  M2_HOME=/opt/maven
  M2=$M2_HOME/bin
  PATH=$PATH:$JAVA_HOME:$M2_HOME:$M2:$HOME/bin
  export PATH
  
  #for refreshing
  source /etc/profile
  echo $PATH
  echo $M2
  
```


- Installation of Jenkins on Jenkins Server
```
  # This is the Debian package repository of Jenkins to automate installation and upgrade. To use this repository, first add the key to your system:
    curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key |      sudo tee \
    /usr/share/keyrings/jenkins-keyring.asc > /dev/null
    
  # Then add a Jenkins apt repository entry:
    echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null
    
  # Update your local package index, then finally install Jenkins:
    sudo apt-get update
    sudo apt-get install fontconfig openjdk-11-jre
    sudo apt-get install jenkins
    
 # Give sudo permission to the jenkins user
   vi /etc/sudoers
   
 # add the following line beside root
   jenkins ALL=(ALL) NOPASSWD: ALL
   
 # restart jenkins
   systemctl restart jenkins
```

Step: 2 Jenkins Plugin Installation only selected plugins are required for this project:
  - Github
  - Maven Invoker
  - Maven Integration
  - Deploy to Container
  
Step: 3 Tomcat installation on tomcat server
  - Install Java 
 ```
    sudo su - 
    apt update
    apt install openjdk-8-jdk -y
    # Or you can run 
    apt install openjdk-8-jre-headless
    # check java version
    java -version
 ```
  - Install Tomcat
 ```
    cd /opt
    wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.65/bin/apache-tomcat-9.0.65.tar.gz #replace with latest
    tar -xvzf /opt/apache-tomcat-9.0.65.tar.gz
    mv apache-tomcat-9.0.65 tomcat
    cd tomcat/bin
    chmod +x startup.sh
    chmod +x shutdown.sh
    ./startup.sh
    
    # Run Tomcat
     http://<IP>:8080
```
     
   - Tomcat Configuration:
     - now application is accessible on port 8090. but tomcat application doesnt allow to login from browser. changing a default parameter in context.xml        does address this issue
     - find / -name in context.xml
     - comment down using <!--- ---> classname tag in the below files
 ``` 
        vi /opt/tomcat/webapps/host-manager/META-INF/context.xml
        vi /opt/tomcat/webapps/manager/META-INF/context.xml
 ```
  - Restart Tomcat services:
    
 ```
    ./tomcat/bin/shutdown.sh
    ./tomcat/bin/startup.sh
```
   - Using unique ports for each application is a best practice in an environment. But tomcat and Jenkins runs on ports number 8080. Hence lets change        tomcat port number to 8090. Change port number in conf/server.xml file under tomcat home.
 ```
    cd /opt/apache-tomcat-8.5.61/conf
    # update port number in the "connecter port" field in server.xml
    # restart tomcat after configuration update
    tomcatdown
    tomcatup
```

   -  Update users information in the tomcat-users.xml file goto tomcat home directory and Add below users to conf/tomcat-user.xml file.
   -  Add below users to ../tomcat/conf/tomcat-user.xml -
```
   vi tomcat-users.xml
```
  
```
   <role rolename="manager-gui"/>
   <role rolename="manager-script"/>
   <role rolename="manager-jmx"/>
   <role rolename="manager-status"/>
   <user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
   <user username="deployer" password="deployer" roles="manager-script"/>
   <user username="tomcat" password="s3cret" roles="manager-gui"/>
```
  - After that restart tomcat services to effect these changes. 
```
   tomcatdown
   tomcatup
 ```
 - Run tomcat and login with "tomcat" and password "s3cret"  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
