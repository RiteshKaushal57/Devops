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

We have a Git repository where our application’s source code is stored. Let’s say I’m talking about a Java application. As soon as a developer raises a pull request to this Git repository, we have configured webhooks to trigger the Jenkins pipeline automatically.

We’re using **declarative Jenkins pipelines** because they are easier to write, maintain, and collaborate on. As part of this declarative Jenkins pipeline, we run multiple stages.

The first stage is the **build stage**. Using Maven as our build tool, we build the application and execute unit tests. Unit testing means running small tests that check individual pieces of your application’s code to make sure they work correctly before moving forward. It is done to catch bugs early, so broken code does not get packaged, deployed, or reach users, which saves time and avoids bigger failures later in the pipeline.

If the build stage is successful, the next stage is **static code analysis** and **security scanning**. For this we use tool such as SonarQube to ensure code quality and safety.

After that, we use **SAST and DAST tools** to verify the application’s security — to check whether any new changes introduced by the developer cause security vulnerabilities. If any of these checks fail, Jenkins sends **email or Slack notifications** that we’ve configured.

If all the stages are successful, we move forward to create a **Docker image**. We use simple shell commands to build the Docker image from the Dockerfile stored in the Git repository. Once the Docker image is created, we again use shell commands to push it to the **container registry**.

This completes our **Continuous Integration (CI)** process, which is handled entirely through Jenkins.

For the **Continuous Delivery (CD)** process, once the Docker image is pushed to the container registry, we have a **Kubernetes cluster** where two continuous delivery tools are deployed — **Argo Image Updater** and **Argo CD**. Both of these are Kubernetes controllers running inside the cluster.

Here’s how they work together:

* **Argo Image Updater** continuously monitors the container registry. As soon as a new image is available, it picks up the new image tag and updates another Git repository that contains our **Helm charts and Kubernetes manifests**.
* Once this Git repository is updated with the new image, **Argo CD** automatically detects the change, pulls the updated manifests, and deploys the new version of the application to the Kubernetes cluster.

This is our **Continuous Delivery process**, and together, this is how we’ve implemented **Continuous Integration and Continuous Delivery (CI/CD)** in our organization.

##  What is Jenkins, and why is it used in a DevOps environment?
Jenkins is an open-source automation tool used to build, test, and deploy applications automatically instead of doing these steps manually. In a DevOps environment, the main goal is to deliver software faster and more reliably, and Jenkins helps achieve that by continuously integrating code changes from developers, running tests on them, and deploying them if everything is fine. Without Jenkins, teams would have to manually build code, test it, and deploy it, which is slow and error-prone. Jenkins works by using pipelines where you define steps like pulling code from Git, building it, testing it, and deploying it, and it executes all of this automatically whenever there is a change in the code, ensuring consistency and reducing human mistakes.

## Explain the difference between Continuous Integration and Continuous Delivery (CI/CD).
Continuous Integration means developers regularly merge code and it is automatically built and tested to catch issues early, while Continuous Delivery ensures that after these tests, the application is always ready to be deployed to production at any time. The reason we use CI/CD is to improve code quality and enable faster, reliable releases. In Jenkins, this is achieved through pipelines where CI handles build and test stages, and CD prepares the application for deployment.

## What is a Jenkins pipeline, and can you describe its basic components?
A Jenkins pipeline in Jenkins is a way to define the entire build, test, and deployment process as code so that everything runs automatically in a fixed sequence instead of manually. It is used because it brings consistency, repeatability, and visibility into the software delivery process, meaning every time code changes, the same steps run without human error. A pipeline works by breaking the process into stages like build, test, and deploy, where each stage contains steps that perform specific tasks such as compiling code, running test scripts, or deploying to servers, and these stages run either sequentially or in parallel based on how you define them. Along with stages and steps, pipelines also use agents (where the code runs), and are usually written in a Jenkinsfile so that the entire workflow is version-controlled along with the application code.

## What are Jenkins jobs, and how do you create a simple one?
In Jenkins, a Jenkins job is basically a task or unit of work that Jenkins executes, such as building code, running tests, or deploying an application, and it acts as the basic building block of automation before pipelines became common. It is used to automate repetitive tasks so developers don’t have to manually perform them every time there is a change, which improves consistency and saves time. A simple job works by connecting to a source code repository, defining what needs to be done (like running a build command), and optionally triggering automatically when code changes occur. To create one, you go to the Jenkins dashboard, click on “New Item,” choose a job type like Freestyle project, give it a name, configure source code management (like Git), add build steps (such as executing shell commands), and then save and run the job to see it execute.

## What is a Jenkins build?
In Jenkins, a Jenkins build is a single run of a job or pipeline where the code is built, tested, and processed. It is used to track the result of code changes and ensure everything is working correctly. Each time the job runs, Jenkins executes the defined steps and marks the build as success or failure.

##  What are build parameters in Jenkins, and how are they used?
In Jenkins, build parameters are inputs that you can pass to a job or pipeline at runtime to make it more flexible instead of hardcoding values. They are used because in real scenarios you may want to run the same job with different configurations, like deploying to different environments (dev, staging, production) or choosing a specific branch or version. These parameters work by allowing you to define options like string, choice, boolean, or credentials in the job configuration or Jenkinsfile, and when you trigger the build, Jenkins asks for these values or uses defaults, then uses them inside the pipeline steps to control behavior, such as deciding which environment to deploy to or which commands to run.

## What is a Jenkins workspace?
In Jenkins, a Jenkins workspace is the directory or folder on the machine (agent or node) where the job actually runs and where all the files related to that build are stored. It is used because Jenkins needs a dedicated place to download the source code, run build commands, generate artifacts, and store temporary files during execution. The workspace works by creating a separate folder for each job (and sometimes each build), where Jenkins pulls the code from the repository, executes all the defined steps like build and test inside that folder, and keeps logs or outputs there, and after the build is complete, the workspace can either be reused for faster builds or cleaned up to avoid conflicts and save space.

## Explain the concept of triggers in Jenkins. How would you use them to automate jobs?
In Jenkins, triggers are mechanisms that automatically start a job or pipeline based on certain events instead of running it manually. They are used because in a real DevOps workflow you want automation—for example, whenever a developer pushes code, the build and test should run immediately without waiting for someone to click a button. Triggers work by listening to events like code changes (via webhooks from Git), scheduled times (cron jobs), or even completion of another job, and once the condition is met, Jenkins automatically starts the job and executes all defined stages. This way, you can fully automate your CI/CD pipeline, such as triggering a build on every code commit or running nightly builds to ensure system stability.

## Discuss strategies for securing Jenkins, like managing access control and credentials.
1. Enable authentication → Configure security in Jenkins (LDAP/OAuth/Jenkins DB)
2. Use RBAC → Manage permissions via roles (Role-Based Strategy plugin)
3. Disable anonymous access → Only authenticated users allowed
4. Use Credentials Manager → Store secrets securely (no hardcoding in pipelines)
5. Use credentials in pipeline → `withCredentials` block in Jenkinsfile
6. Restrict agent access → Use secured nodes and limit permissions
7. Enable HTTPS → Configure SSL for secure communication
8. Protect Jenkins UI → Change default port, restrict via firewall
9. Use secrets from vault → Integrate with tools like HashiCorp Vault
10. Keep plugins updated → Avoid vulnerabilities
11. Enable audit logs → Track user actions
12. Backup and secure configs → Regular backups of Jenkins home (`/var/lib/jenkins`)











