# Deploying a Retail Banking Application using Terraform

October 13, 2023

By: Khalil Elkharbibi


## Purpose

> The primary objective of this deployment was to establish a robust, scalable, and secure infrastructure on AWS for hosting a web application. By leveraging the power of Terraform, an Infrastructure as Code (IaC) tool, the aim was to automate the provisioning of cloud resources, ensuring consistency, repeatability, and efficient infrastructure management.

> Several key goals underpinned this deployment:

> 1. **Automation**: By integrating Jenkins, a leading Continuous Integration/Continuous Deployment (CI/CD) tool, the deployment process aimed to automate the application's build, test, and deployment stages. This not only accelerates the deployment process but also minimizes human errors.

> 2. **Scalability**: The infrastructure was designed with scalability in mind. By setting up multiple Availability Zones (AZs) and considering future enhancements like load balancers and auto-scaling groups, the deployment ensures that the application can handle varying loads and can be scaled up or down based on demand.

> 3. **Security**: Security was a paramount concern. From setting up a Virtual Private Cloud (VPC) to ensure network isolation to configuring security groups for precise access control, every step was taken to safeguard the application and its data.

> 4. **High Availability**: By deploying resources across multiple AZs, the infrastructure aims to achieve high availability. This ensures that even if one AZ faces an outage, the application remains accessible, providing a seamless user experience.

> 5. **Maintainability**: With the entire infrastructure setup codified using Terraform and deployment processes defined in Jenkinsfiles, the deployment emphasizes maintainability. Any team member can review, modify, or replicate the infrastructure setup, ensuring transparency and collaboration.

> In essence, this deployment was not just about setting up an application on the cloud but about doing so in a way that aligns with best practices, ensuring that the application is resilient, secure, and primed for growth.

## Plan/System Design Diagram:

![Deployment 5 diagram drawio (1)](https://github.com/atlas-lion91/Deployment_5/assets/140761974/61a470e9-69b0-4b16-94d3-577cef85ae95)


### Infrastructure Setup with Terraform 

> **Why Terraform?** Terraform is an Infrastructure as Code (IaC) tool that allows users to define and provision data center infrastructure using a declarative configuration language. It's chosen for its ability to manage complex infrastructure setups and configurations in a repeatable manner.

 1. **Virtual Private Cloud (VPC) Configuration**:
  >  - Created a VPC.
  >   - **Why?** A VPC provides a private, isolated section of the AWS Cloud where resources can be launched in a defined virtual network. This ensures security and logical separation of the application.
  > - Configured 2 Availability Zones (AZs).
  >   - **Why?** Using multiple AZs ensures high availability and fault tolerance for applications. If one AZ fails, the application remains accessible via the other AZ.
  > - Set up 2 Public Subnets.
  >   - **Why?** Public subnets allow resources within them to communicate directly with the internet, given they have an Elastic IP or Public IP. This is essential for resources like the Jenkins server which needs to be accessed externally.
  > - Launched 2 EC2 instances.
  >   - **Why?** One instance is for Jenkins (CI/CD) and the other for the application. Separating these ensures that the CI/CD processes don't interfere with the running application.
  > - Created 1 Route Table.
  >   - **Why?** Route tables determine where network traffic is directed. Ensuring correct routing is essential for network communication within the VPC.
  > - Configured Security Group with the following ports open: 8080, 8000, 22.
  >   - **Why?** Security groups act as virtual firewalls. Ports 8080 and 8000 might be application-specific ports, while 22 is for SSH access. Only necessary ports should be opened to ensure security.

### Jenkins Configuration on First EC2 Instance

> **Why Jenkins?** Jenkins is a popular open-source tool to perform continuous integration and build automation. It's chosen for its versatility, wide range of plugins, and active community support.

1. **Installation**:
  > - Installed Jenkins on the first EC2 instance.
  >   - **Why?** Jenkins automates the non-human part of the software development process, with continuous integration and facilitating technical aspects of continuous delivery.
   
2. **User Setup**:
  > - Created a Jenkins user password.
  > - Logged into the Jenkins user.
  >   - **Why?** This is a security measure. Running Jenkins with a dedicated user minimizes potential risks.

3. **SSH Key Configuration**:
  > - Generated a public and private key pair.
  > - Copied the public key to the second EC2 instance.
  >   - **Why?** This allows the Jenkins server to securely communicate with the application server without needing a password.

4. **Software Installation**:
  > - Installed necessary software under the `ubuntu` user.
  >   - **Why?** These packages and repositories might be prerequisites for the application or other tools that will be used in the deployment process.

### Configuration on Second EC2 Instance

1. **Software Installation**:
  > - Installed necessary software.
  >   - **Why?** Just like on the first instance, these are essential prerequisites for the application or other deployment-related tools.

### Jenkins Pipeline Configuration

> **Why Pipelines?** Jenkins Pipelines provide an easy way to create and manage a series of automation steps. They are defined using a domain-specific language called "Pipeline DSL."

1. **Jenkinsfilev1**:
 >  - Defined stages for Build, Test, Deploy, and Reminder.
 >    - **Why?** This structure ensures a clear CI/CD flow: building the application, testing it, deploying it, and then sending reminders or notifications.


2.  **Creating the "dev" Branch**:
>   I created a new branch called "dev" to work on our changes separately from the main codebase.
   ```bash
   git checkout -b dev
   ```
3. **Modifying Jenkinsfilev2 in the "dev" Branch**

> With the "dev" branch active, we made necessary modifications to the 'Clean' & 'Deploy' stages in `Jenkinsfilev2`. After making the changes, I committed them to the "dev" branch:

```bash
git add Jenkinsfilev2
git commit -m "Modified Jenkinsfilev2 for enhanced deployment"
```

4. **Merged the "dev" Branch back to "main"**

> Once we were satisfied with the changes in the "dev" branch, we merged them back into the "main" branch. This ensures that our main codebase benefits from the enhancements we made in the "dev" branch:

> **Switch to the "main" Branch**:
   ```bash
   git checkout main
   ```

> **Merge the "dev" Branch**:
   ```bash
   git merge dev
   ```

> **Push Changes to Remote Repository**:
>   After merging, we pushed our changes to the remote repository to ensure it's updated with the latest code.
   ```bash
   git push origin main
   ```

 **Why Use Branching and Merging?**

> - **Isolation**: Branching allows developers to work on features, fixes, or experiments without affecting the main codebase. This isolation ensures that the main codebase remains stable.
  
> - **Collaboration**: Multiple developers can work on different branches simultaneously, ensuring smooth collaboration without overwriting each other's changes.
  
> - **Safe Integration**: Once the changes in a branch are tested and verified, they can be safely merged into the main branch, ensuring that only validated code gets integrated.




### Deployment Process

1. Created a Jenkins multibranch pipeline and executed Jenkinsfilev1.
  > - **Why?** Multibranch pipelines automatically recognize multiple branches in a repository, allowing for different Jenkinsfiles per branch. This is useful for feature-specific deployments or different deployment strategies.

2. Checked the application on the second EC2 instance.
  > - **Why?** It's essential to verify that the application is running as expected after deployment.
     ![Screenshot 2023-10-13 215720](https://github.com/atlas-lion91/Deployment_5/assets/140761974/7db859f4-dd4e-47cc-9595-1e454b8c9540)


3. Made changes to the HTML content.
  > - **Why?** This simulates a real-world scenario where application content or features might be updated frequently.

4. Executed the Jenkinsfilev2.
  > - **Why?** To deploy the updated content to the application.
     ![Screenshot 2023-10-13 223853](https://github.com/atlas-lion91/Deployment_5/assets/140761974/c8d91a42-f948-4904-8a0e-d39dc1d04c99)


### CloudWatch Monitoring Setup

> Amazon CloudWatch Alarms allow you to monitor metric values and set conditions to trigger notifications or automated actions. 

> - **Why Use CloudWatch Alarms?:**

> - **Proactive Monitoring**: Instead of reacting to issues after they've affected users, CloudWatch Alarms allow you to be proactive. By setting alarms, you can be notified of potential issues before they become critical.

> - **Resource Optimization**: Alarms can help ensure you're making the most of your AWS resources. For instance, if CPU utilization is consistently low, you might be over-provisioned and could save money by downscaling.

> - **Operational Awareness**: In a dynamic cloud environment, it's challenging to keep track of every resource. Alarms provide a way to automatically monitor and get insights into your application's operational health.

> - **Automated Actions**: Beyond just notifications, alarms can trigger automated actions. For example, if an application's traffic spikes, an alarm could automatically scale up the number of EC2 instances.


> 1. **Navigate to CloudWatch Console**:
>   - Open the CloudWatch console in AWS.
   
> 2. **Create Alarm**:
>   - In the navigation pane, click on "Alarms" and then "Create Alarm".
   
> 3. **Select Metric**:
>   - Choose the metric you want to monitor. This could be an EC2 metric like CPU utilization, network in/out, or any custom metric you've defined.
   
> 4. **Configure Conditions**:
>   - Define the threshold for the metric. For instance, you can set an alarm if the CPU utilization goes above 80% for 5 minutes.
   
> 5. **Configure Actions**:
>   - Specify what actions should be taken when the alarm state is triggered. This could be sending a notification through Amazon SNS, stopping or terminating an EC2 instance, or scaling an Auto Scaling group.
   
> 6. **Add a Description**:
>   - Provide a meaningful description for the alarm, so team members can understand its purpose at a glance.
   
> 7. **Review and Create**:
>   - Review your configurations and create the alarm.

> Once the alarm is set, CloudWatch will continuously evaluate the chosen metric. If the metric breaches the condition set, the specified action will be triggered.


### Considerations

> - **Decision to Run Jenkinsfilev2**: The decision to run Jenkinsfilev2 was based on the need to deploy the updated HTML content to the application.
  
> - **Instance Placement**: It's recommended to place the Jenkins instance in a public subnet and the application instance in a private subnet.
>   - **Why?** This ensures that the Jenkins UI is accessible externally, while the application remains secure. A load balancer can route traffic to the application in the private subnet, ensuring it's not directly exposed to the internet.


## Issues Experienced

During the deployment process, several challenges and issues were encountered:

1. **SSH Key Configuration**:
>   - **Issue**: Difficulty in copying the public key from the Jenkins server to the application server.
     ![Screenshot 2023-10-14 073555](https://github.com/atlas-lion91/Deployment_5/assets/140761974/b4af6204-7d59-4417-a103-2c07f0a8368f)

 >  - **Resolution**: Ensured that the correct permissions were set for the `authorized_keys` file and the `.ssh` directory on the application server.
     ![Screenshot 2023-10-14 073655](https://github.com/atlas-lion91/Deployment_5/assets/140761974/a62817e4-749f-4795-9f75-a46218c5495f)


2. **Jenkins Plugin Compatibility**:
 >  - **Issue**: Some Jenkins plugins were not compatible with the Jenkins version installed.
 >  - **Resolution**: Updated Jenkins to the latest version and ensured all plugins were compatible before installation.
     ![Screenshot 2023-10-14 073828](https://github.com/atlas-lion91/Deployment_5/assets/140761974/96c612e5-7949-4471-97cd-e1067b14ef48)


3. **Terraform State Management**:
  > - **Issue**: Terraform state conflicts when trying to apply infrastructure changes.
     ![Screenshot 2023-10-13 150224](https://github.com/atlas-lion91/Deployment_5/assets/140761974/2c920596-8303-4eec-81c0-0dffb1047491)
  > - **Resolution**: Used remote state management for state locking to ensure consistent state management.

4. **Application Connectivity**:
  > - **Issue**: The application on the second EC2 instance was not accessible externally.
  > - **Resolution**: Verified security group rules and ensured that the necessary ports were open. Also, checked the application logs for any internal errors.

### Optimization Suggestions

1. **Infrastructure as Code (IaC) Enhancements**:
  > - Consider using Terraform modules to modularize and reuse common infrastructure components. This can simplify the Terraform code and make it more maintainable.
  > - Consider using an automation tool like Ansible to ensure that configurations are consistent across all environments. This reduces the "it works on my machine" type of issues and ensures that what you test is what you deploy.

2. **Jenkins Pipeline Enhancements**:
  > - Implement a rollback mechanism in the Jenkins pipeline. In case of deployment failures, this will ensure that the application can be reverted to its previous stable state.
  > - Consider using Jenkins shared libraries to reuse common pipeline steps across different Jenkinsfiles.

3. **Security Enhancements**:
  > - Implement a bastion host or a jump server to access the private EC2 instances. This adds an additional layer of security.
  > - Regularly rotate SSH keys and use AWS Key Management Service (KMS) for enhanced key management.

4. **Monitoring and Logging**:
  > - Set up centralized logging using tools like ELK Stack (Elasticsearch, Logstash, Kibana) or Graylog to have a consolidated view of logs from all instances.

5. **Cost Optimization**:
  > - Consider using EC2 Spot Instances for non-critical environments to save costs.
  > - Regularly review and terminate unused resources to avoid incurring unnecessary costs.


## Conclusion

> The deployment process outlined in this documentation represents a comprehensive approach to setting up a web application on AWS. By leveraging modern tools like Terraform and Jenkins, the deployment not only automated the provisioning and deployment processes but also ensured that the infrastructure is scalable, secure, and maintainable.

> Throughout the deployment, several challenges were encountered, from SSH key configurations to Terraform state management. However, with diligent troubleshooting and adherence to best practices, these issues were resolved, underscoring the importance of thorough planning and testing in any deployment process.

> The optimization suggestions and the purpose behind each step provide a roadmap for future enhancements and deployments. They emphasize the importance of continuous improvement in the ever-evolving landscape of cloud infrastructure and application deployment.

> In conclusion, this deployment serves as a testament to the power of automation and the importance of a well-thought-out infrastructure design. As the application grows and evolves, the foundations laid out in this deployment will ensure that it remains resilient, performant, and ready to meet future challenges.
