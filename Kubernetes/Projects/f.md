1. Create t2 large instance
2. Login to the instance using terminal.
```
ssh -i ~/.ssh/<AWSKeyPair> ubuntu@<IP Address>
```
3. Install Java Because Java is prerequisite because Jenkins is a Java application
```
sudo apt update
sudo apt install openjdk-17-jre
```

4. Verify Java is installed
```
java -version
```

5. Install Jenkins
```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

6. Jenkins will not be accessible due to inbound traffic rules of AWS. So, go tu instance Security group and add All traffic in inbound Traffic rules.

7. Check if server Jenkins is running
```
ps -ef | grep jenkins
```

8. Run this command on browser
```
http://<public ip address of instance>:8080/
```

9. Run this command to copy the password
```
/var/lib/jenkins/secrets/initialAdminPassword
```
4c36831658b54de8ae8ed778885cefc0

Now, the Jenkins dashboard is open and start creating a pipeline.
The Jenkins file be in any location? yes
can the Jenkins file have a different name? yes
Why to choose docker as an agent for a pipeline?

*the purpose of Jenkins is to execute all the other tasks of the continuous integration*

10. Install `docker pipeline, SonarQube scanner` plugin 
11. Install Sonar server on ec2
```
 sudo su -
 adduser sonarqube

sudo apt update && sudo apt install unzip -y
adduser sonarqube
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.4.1.88267.zip
unzip *
sudo chown -R sonarqube:sonarqube /opt/sonarqube
sudo chmod -R 775 /opt/sonarqube
cd /opt/sonarqube/bin/linux-x86-64
./sonar.sh start
```
12. Now create token on sonarqube to connect with jenkins

squ_87b6b8accca140b125416a1dd115107a1592c41b

13. Now install docker
```
sudo su -
sudo apt install docker.io
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
```
14. Now restart the jenkins as it is good practice to do when you install any plugins 

Now this is CD part

15. make minikube 
```
minikube start --memory=4098 --driver=docker
```

16. Install on Kubernetes
1. Install Operator Lifecycle Manager (OLM), a tool to help manage the Operators running on your cluster.
```
curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.36.0/install.sh | bash -s v0.36.0
```
2. Create a separate namespace (like argocd) and then apply:
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

3. 


 what is this uh stages in Jenkins Pipeline?
 stages are the different blocks we are trying to build in jenkins pipeline. I'm trying to build the
application this is one block, runnin the static code analysis different block, artifactory push the image artifactory is different block, shell script which updates the Manifest repo is another block.

what is the difference between MVN clean package and MVN clean install?

17. connect docker with jenkins
18. chnge sonar url in jenkinsfile