# Build and deploy a Java application to Amazon EKS using a DevSecOps CI/CD pipeline

### Overview:
Create a continuous integration and continuous delivery (CI/CD) pipeline that automatically builds and deploys a Java application to an Amazon Elastic Kubernetes Service (Amazon EKS) cluster on the Amazon Web Services (AWS) Cloud. This pattern uses a greeting application developed with a Spring Boot Java framework and that uses Apache Maven.

This solution will be useful to build the code for a Java application, package the application artifacts as a Docker image, security scan the image, and upload the image as a workload container on Amazon EKS and can be also used as a reference to migrate from a tightly coupled monolithic architecture to a microservices architecture. 

It also emphasizes on how to monitor and manage the entire lifecycle of a Java application, which ensures a higher level of automation and helps avoid errors or bugs and has been implemented with best DevSecOps Pipeline practices.

### High Level Architecture:

![Alt text](./aws-devsecops.gif?raw=true "Architecture")

1. Developer will update the Java application code in the base branch of the AWS CodeCommit repository, creating a Pull Reqeust (PR).

2. Amazon CodeGuru Reviewer automatically reviews the code as soon as a PR is submitted and does a analysis of java code as per the best practices and gives recommendations to users.

3. Once the PR is merged to base branch, a AWS CloudWatch event is created.

4. This AWS CloudWatch event triggers the AWS CodePipeline.

5. CodePipeline runs the security scan stage (continuous security).

6. CodeBuild first starts the security scan process in which Dockerfile, Kubernetes deployment Helm files are scanned using Checkov and application source code is scanned using AWS CodeGuru CLI based on incremental code changes.

7. Next, if the security scan stage is successful, the build stage(continuous integration) is triggered.

8. In the Build Stage, CodeBuild builds the artifact, packages the artifact to a Docker image, scans the image for security vulnerabilities by using Aqua Security Trivy, and stores the image in Amazon Elastic Container Registry (Amazon ECR).

9. The vulnerabilities detected from step6 are uploaded to AWS Security Hub for further analysis by users or developers, which provides overview, recommendations, remediation steps for the vulnerabilties.

10. Emails Notifications of various phases within the AWS CodePipeline are sent to the users via Amazon SNS.

11. After the continuous integration phases are complete, CodePipeline enters the deployment phase (continuous delivery).

12. The Docker image is deployed to Amazon EKS as a container workload (pod) using Helm charts. 

13. The application pod is configured with Amazon CodeGuru Profiler Agent which will send the profiling data of the application (CPU, Heap usage, Latency) to Amazon CodeGuru Profiler which is useful for developers to understand the behaviour of the application.

