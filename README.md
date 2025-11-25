# AWS Cloud Architecture Deployment
---
![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![EC2](https://img.shields.io/badge/EC2-orange?style=for-the-badge&logo=amazon-ec2&logoColor=white)
![Java](https://img.shields.io/badge/java-%23ED8B00.svg?style=for-the-badge&logo=openjdk&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Shell Script](https://img.shields.io/badge/shell_script-%23121011.svg?style=for-the-badge&logo=gnu-bash&logoColor=white)
![MySQL](https://img.shields.io/badge/mysql-4479A1.svg?style=for-the-badge&logo=mysql&logoColor=white)
![RabbitMQ](https://img.shields.io/badge/Rabbitmq-FF6600?style=for-the-badge&logo=rabbitmq&logoColor=white)
![Memcached](https://img.shields.io/badge/Memcached-00A32A?style=for-the-badge&logo=memcached&logoColor=white)
![Apache Tomcat](https://img.shields.io/badge/apache%20tomcat-%23F8DC75.svg?style=for-the-badge&logo=apache-tomcat&logoColor=black)
![Maven](https://img.shields.io/badge/Apache%20Maven-C71A36?style=for-the-badge&logo=Apache%20Maven&logoColor=white)

## Architecture Overview
---
The deployment leverages AWS managed services to replace local virtualization components, ensuring high availability, scalability, and fault tolerance.

### Service Mapping (Local vs. AWS)

| component | Role | Local/Legacy Setup | AWS Cloud Solution |
| :--- | :--- | :--- | :--- |
| **Compute** | Application & Backend Hosting | Virtual Machines (VMware/VirtualBox) | **AWS EC2 Instances** (Hosting Tomcat, RabbitMQ, Memcached, MySQL) |
| **Load Balancing** | Traffic Distribution | Nginx Reverse Proxy | **AWS ELB (Application Load Balancer)** - Replaces Nginx for better integration and managed routing. |
| **Scaling** | Handling Traffic Spikes | Manual VM Creation | **AWS Auto Scaling Group** - Automatically adds/removes Tomcat instances based on CPU load. |
| **Storage** | Shared Files & Artifacts | Local Disk / NFS | **AWS S3 & EFS** - S3 for artifact storage and EFS for shared instances storage. |
| **DNS** | Name Resolution | `/etc/hosts` file | **AWS Route 53** - Managed Private/Public DNS service. |
| **Security** | Access Control | `iptables` / UFW | **AWS Security Groups & IAM Roles** - Fine-grained firewall and access management. |
---

##  Deployment Flow
1. **Infrastructure Setup:** Configuring VPC, Security Groups, and Key Pairs.
2. **Backend Services:** Provisioning EC2 instances for Database (MySQL), Cache (Memcached), and Message Broker (RabbitMQ).
3. **Application Layer:** Setting up Tomcat on EC2 and creating an **AMI (Amazon Machine Image)**.
4. **High Availability:** Creating a Load Balancer (ELB) to distribute traffic.
5. **Auto Scaling:** Configuring Launch Templates and Auto Scaling Groups to handle load.
6. **DNS Routing:** Mapping a friendly domain name using Route 53.

---
