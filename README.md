# AWS Networking/VPC

### What is a VPC?
- A VPC is a private virtual network dedicated to your AWS account that allows you to launch resources such as EC2 instances.
- AWS VPC is a service that provides the base to build cloud infrastructure, with control over security, of the traffic between services and overall more control over the network.
- 
### What is a CIDR block
- CIDR stands for Classless Inter-Domain Routing. The CIDR value denotes the max number of IP addresses that can be used in the VPC. Allocates IP addresses for IP routing.
### What is Internet Gateway
- Is a highly available Amazon VPC component that allows communication between your VPC and the internet and vice versa. 
### What is subnet
- A subnet is a subdivision of an IP network, that allows you to launch resources such as EC2 instances into a specific subnet. 

### What is route table
- Sets rules that determine where network traffic is directed and allowed to go.
- It tells the network to route traffic to if the destination isn't within the same subnet. Traffic can be routed to an interface or gateway.
- 
### What is NACL
-A network access control list (NACL) operates on a subnet level. Is an optional layer of security using firewalls for controlling traffic in and out of a subnet. The main use for NACLs is explicit deny which you can't do with Security Groups.
![Screenshot 2022-02-10 133025](https://user-images.githubusercontent.com/98215575/153418064-6bc490b9-db64-4618-a56e-f3e63414fc3e.png)

### What is NACL
- A network access control list (NACL) operates on a subnet level. Is an optional layer of security using firewalls for controlling traffic in and out of a subnet. The main use for NACLs is explicit deny which you can't do with Security Groups.

### Creating a VPC

- Select region -Ireland 
- Create VPC- virtual private cloud
- Select valid CDIR block for our VPC to split into blocks e.g. 10.0.0.0/16
- Step 2 create internet gateway (IG)
- sept 2.1 attach the IG to your VPC 
- Step 3 create a public subnet
- Step 3.1 assoacites subnet to VPC
- Step 4 route tables (rt) for public subnet
- step 4.1 edit routes to allow IG
- 4.2 assoiacte to our public subnet

Step 5 create a secuity group in our public subnet to allow required ports/ traffic
- allow port 80
- port 3000
- https -ssl if needed
- 
### Step 1: Creating a VPC
- Make sure you're in the appropriate region (Ireland)
- On the AWS Dashboard search for `VPC` and select `create VPC`
- Add an appropriate name such as `eng103a_name_vpc`
- Add a IPv4 CDIR block we used `10.0.0.0/16`

### Step 2: Create Internet Gateway
- Select `Internet Gateways` on the dashboard then select `Create Internet Gateway`
- Add an appropriate name tag(eng103a_name_IG) then select `Create Internet Gateway`
- Once created, select actions and attach VPC
- Now attach the VPC already made (without it nothing within our vpc will receive traffic )
- Finally attach Internet Gateway

### Step 3: Creating a subnet
- Return to the AWS dashboard and select subnet
- Then create a subnet
- Create a public subnet (Will have access to the internet gateway through the VPC)
- Create a private subnet (Can only be accessed by public subnet after given permissions )
- Enter names with the previous naming convenvtion e.g. `eng103a_name_subnet_public` , for the private subnet end the name in `private`
- Select the VPC previosuly created 
- Select the availability zones needed (eu-west-1a)
- Public CIDR block `10.0.5.0/24`
- Private CIDR block `10.0.6.0.24` (adding 1 to the public CIDR's 3rd digit) 

### Step 4 Creating a route table
- Return to the VPC dashboard again and select create route table.
- Select the VPC just created.
- Enter an appropriate name e.g. `eng103a_name_RT_public`
- Then select actions -> edit routes -> add route
- Destination `0.0.0.0/0` can be accessed by anyone 
- Target `Internet Gateway` then select the IG we previosuly created. Save changes
- Select actions -> edit subnet associations -> select public subnet
- Create a private route table named `eng103a_name_RT_private` (Do Not edit routes as this should have no public access )

Create a new EC2 instance

- Go to EC2 and launch instance
- Select your own network and public subnet
- Under user data enter

```
#!/bin/bash

sudo apt-get update -y
sudo apt-get upgrade -y

sudo apt-install nginx -y
sudo systemctl restart nginx
sudo systemctl enable nginx
```

Rest of the steps are the same as before
Wait for EC2 to initialise
Test it by using the public IP address

### Connecting our DB to app -
- Using the private subnet created above
- Select your VPC
Enter your CIDR `10.0.6.0/24` This is different to your subnet for public.
- Use private route table creates above
- We don't need to add ports for this as we don't want to allow internet access as it is private
- Launch VPC DB

Now  start a new DB instance using our DB AMI
- When creating this it will be same steps as always but we will change few details
Use the VPC that was created earlier
- This will automatically choose our subnet but we need to change it to private subnet
- Disable the `public IP`
- Important step: Now when we get onto security we need to add a new rule
- Custom TCP Rule > Port Range : 27017 > Source > `10.0.5.0/24`
This will allow the publci subnet to access DB.
- Now carry on and add any tags necessary
- Now enter this under user data at the bottom

```
#!/bin/bash
sudo apt update -y && sudo apt upgrade -y
sudo su ubuntu
echo "export DB_HOST='mongodb://10.0.6.98:27017/posts'" >> /home/ubuntu/.bashrc
source /home/ubuntu/.bashrc
export DB_HOST='mongodb://10.0.6.98:27017/posts'
cd /home/ubuntu/app && screen node seeds/seed.js
cd /home/ubuntu/app && screen -d -m npm start 
```
- From the code above, we obtained the IP address from our running DB instance. This is the private IPv4 address.
- Add security tags as necessary.
- With both instances running we can see if `app` and `/posts` works and the publci IP of the app instance.


