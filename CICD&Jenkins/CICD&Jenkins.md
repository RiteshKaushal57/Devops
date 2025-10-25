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


We have a Git repository where our application’s source code is stored. Let’s say I’m talking about a Java application. As soon as a developer raises a pull request to this Git repository, we have configured webhooks to trigger the Jenkins pipeline automatically.

We’re using **declarative Jenkins pipelines** because they are easier to write, maintain, and collaborate on. As part of this declarative Jenkins pipeline, we run multiple stages.

The first stage is the **build stage**. Using Maven as our build tool, we build the application and execute unit tests. If the build stage is successful, the next stage is **static code analysis**. This step ensures the application isn’t exposed to any code quality or security issues.

After that, we use **SAST and DAST tools** to verify the application’s security — to check whether any new changes introduced by the developer cause security vulnerabilities. If any of these checks fail, Jenkins sends **email or Slack notifications** that we’ve configured.

If all the stages are successful, we move forward to create a **Docker image**. We use simple shell commands to build the Docker image from the Dockerfile stored in the Git repository. Once the Docker image is created, we again use shell commands to push it to the **container registry**.

This completes our **Continuous Integration (CI)** process, which is handled entirely through Jenkins.

For the **Continuous Delivery (CD)** process, once the Docker image is pushed to the container registry, we have a **Kubernetes cluster** where two continuous delivery tools are deployed — **Argo Image Updater** and **Argo CD**. Both of these are Kubernetes controllers running inside the cluster.

Here’s how they work together:

* **Argo Image Updater** continuously monitors the container registry. As soon as a new image is available, it picks up the new image tag and updates another Git repository that contains our **Helm charts and Kubernetes manifests**.
* Once this Git repository is updated with the new image, **Argo CD** automatically detects the change, pulls the updated manifests, and deploys the new version of the application to the Kubernetes cluster.

This is our **Continuous Delivery process**, and together, this is how we’ve implemented **Continuous Integration and Continuous Delivery (CI/CD)** in our organization.









I/O: What type of Jenkins file are using.? Declarative Jenkins file.
I/O: What type of agents are you using in Jenkins? Docker agent 