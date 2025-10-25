## What is CI/CD?
CI/CD stands for Continuous Integration and Continuous Delivery/Deployment.
CI automatically builds and tests code whenever developers make changes, while CD automates the deployment process.
Together, they help deliver software faster, with fewer errors, and make the release process more consistent and reliable.

## Why CI/CD?
CI/CD is used because it automates the entire software delivery process, helping teams release updates faster and with fewer errors.
It’s important because, in traditional development, integrating code manually and deploying it by hand often causes delays, bugs, and inconsistencies between environments. CI/CD solves that by continuously building, testing, and deploying code through automation.
This means developers get quick feedback on their changes, issues are caught early, and users receive new features or fixes faster.

## CI/CD Process
When a developer commits code to GitHub, a webhook triggers the Jenkins pipeline, which acts as our CI/CD orchestrator.
The pipeline starts with a checkout stage to pull the latest code, followed by a build and unit testing stage using tools like Maven or pytest, depending on the language. Next, we run static code analysis and security scanning with tools such as SonarQube to ensure code quality and safety.

Once the tests and scans pass, Jenkins builds a container image using the Dockerfile, performs image vulnerability scanning, and pushes the image to a registry like Docker Hub or AWS ECR.

In the CD phase, Jenkins updates the Kubernetes manifests or Helm charts with the new image version and pushes them to a separate Git repository.
Finally, Argo CD continuously watches that repo and automatically deploys the new version to the Kubernetes cluster, completing the delivery process.
In short, this pipeline automates everything from code commit to deployment — ensuring fast, reliable, and secure releases.

## Jenkins Demo

1. Launch ec2 instance
2. ssh to ec2 instance
```
ssh -i ~/.ssh/*AWSKeyPair.pem ubuntu@PublicIPv4
```

So the developers store the code into the git repository and there is Jenkins which watches the commits or pull request onto this repository. So when a developer committed the change to the repository, Jenkins pipeline gets triggered using **webhooks**. So Jenkins will not watch your repository instead git will send notification to the Jenkins and Jenkins will trigger the pipeline. We configure webhook by getting to the Jenkins and get the **web hook url**. put this in github settings where we can define the action ( it can be triggered on commit, pull request, issues) the web hook has to be triggered. So now we will perform set of actions using Jenkins file on Jenkins. First action, we will use Maven to build application. As a part of build, unit test will run. After unit test successfull. you will perform some static code analysis and it is also successfull then we will go to next stage. But what if build state fails? In such cases we configure some alerts as email notifications or slack notifications using api's. But if build state passes then next stage is to integrate the Sonar Cube to verify what happened in previous stage. EX: If build was
successful but still you can verify did it reach a specific threshold of our organization, is it matching with the compliance of our organization, does it have errors less than x percentage or are there any security vulnerabilities inside the code that is written by the developer in the pull request. if there is any security vulnerability than what is that security vulnerability and you can send the email notification. If everything thing is right then you can proceed to Docker Image creation and send this Docker Image to the  Docker registry.

I/O: What type of Jenkins file are using.? Declarative Jenkins file.
I/O: What type of agents are you using in Jenkins? Docker agent