This project demonstrates the creation of a production-ready Virtual Private Cloud (VPC) environment in AWS. The setup improves resiliency by deploying servers across multiple availability zones using an Auto Scaling Group and an Application Load Balancer. For additional security, the servers are hosted in private subnets and receive internet access via a NAT Gateway.

The architecture includes:

A VPC
Auto Scaling Group
Bastion Host
NAT Gateway
Load Balancer
Public and Private Subnets
Steps to Build the Environment
1. Create the VPC
Log in to the AWS Management Console.
Navigate to the VPC service and click on “Create VPC” > “VPC and more.”
Assign a name to your VPC, e.g., AWS-PROD-EXAMPLE.
Create the following subnets in two availability zones:
Two public subnets for the NAT Gateway and Load Balancer.
Two private subnets for the application servers.
Attach route tables to each subnet:
Public subnets: Configure a route to the Internet Gateway.
Private subnets: Configure a route to the NAT Gateway.
Click “Create VPC” and wait for the resources to be provisioned.
![Screenshot (118)](https://github.com/user-attachments/assets/98af21c0-640d-445d-9dbb-10ccf7ab1e21)
![Screenshot (119)](https://github.com/user-attachments/assets/03a40bf0-15b4-4d98-851f-8aedc8b726d4)



2. Set Up the EC2 Instances
To deploy application servers, use an Auto Scaling Group:

Create a Launch Template:
Assign a name and description.
Choose an AMI (e.g., Ubuntu).
Set the instance type (e.g., t2.micro).
Create a security group allowing:
SSH (port 22) access.
Application traffic (port 8000).
2.Navigate to Auto Scaling Groups and create one using the launch template:

Select the VPC and specify the two private subnets.
Set the desired capacity to 2 instances and the maximum capacity to 3.
Launch the Auto Scaling Group.
Verify that the instances have been successfully created in the private subnets.

![Screenshot (120)](https://github.com/user-attachments/assets/70f4a547-dce7-4ae3-8bd1-73d480a6ab91)
![Screenshot (122)](https://github.com/user-attachments/assets/a0c97a5b-32ca-44d6-a6d6-1a9e5448e2e3)


3. Set Up the Bastion Host
To securely access instances in the private subnets:

Launch an EC2 instance for the bastion host in the public subnet.
Enable auto-assignment of public IP addresses.
Create a security group allowing:
SSH access (port 22) from your IP address.
![Screenshot (123)](https://github.com/user-attachments/assets/f43636cd-a341-4ba1-851c-88a6b83a96bc)


Launch the instance and SSH into it using the key pair associated with the bastion host: example ; ssh -i /path/to/bastion-key.pem ubuntu@<bastion-public-ip>

4. Access Private Instances
Since private subnets lack public IP addresses:

Copy the private key required for accessing private instances to the bastion host: scp -i /path/to/bastion-key.pem /path/to/my-key.pem ubuntu@<bastion-public-ip>:/home/ubuntu
SSH into private instances from the bastion host:ssh -i /path/to/my-key.pem ubuntu@<private-instance-ip>
Transfer the Python application to the private instance: scp -i /path/to/my-key.pem /path/to/app.py ubuntu@<private-instance-ip>:/home/ubuntu
SSH into the private instance and start the application on port 8000.
6. Set Up the Load Balancer
To distribute traffic across the private instances:

Create an Application Load Balancer in the public subnet.
Configure a security group allowing:
HTTP traffic (port 80).
Application traffic (port 8000).
3. Create a target group and register the private instances.

4. Attach the target group to the load balancer.

5. Provision the load balancer and copy its public IP to access the application.
![image](https://github.com/user-attachments/assets/126328ec-01bc-4fd5-9f99-7b896096b70e)


Application Output
![image](https://github.com/user-attachments/assets/444fdf6d-efda-4bb5-bdd3-79aac3c948e4)

The deployed application displays the following message when accessed through the load balancer:


Challenges Encountered
Copying the Key File to the Bastion Host:
Initially struggled to SCP the private key file to the bastion host. Solved it by ensuring the correct file permissions and paths were used.
This project demonstrates the use of AWS services to create a scalable, secure, and production-ready environment while addressing challenges during implementation.
