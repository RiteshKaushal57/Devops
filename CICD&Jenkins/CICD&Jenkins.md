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