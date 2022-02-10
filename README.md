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

### What is NACL
-A network access control list (NACL) operates on a subnet level. Is an optional layer of security using firewalls for controlling traffic in and out of a subnet. The main use for NACLs is explicit deny which you can't do with Security Groups.

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



