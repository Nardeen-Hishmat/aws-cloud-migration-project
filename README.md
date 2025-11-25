# AWS Cloud Refactoring Project 
####This project demonstrates the **Re-architecting (Refactoring)** of a multi-tier Java application on AWS. Instead of managing databases and brokers on VMs, we leverage AWS Managed Services (PaaS) for maximum scalability, security, and reduced operational overhead.

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
The application is deployed within a custom **VPC** designed for security, isolating the data layer in private subnets while exposing the application via a Load Balancer.

### Big Picture
```mermaid
graph TD
%% Definitions of Nodes
    User((User / Internet))
    R53[Route 53 <br> DNS]
    
    subgraph "AWS Custom VPC"
        style VPC fill:#f9f9f9,stroke:#333,stroke-width:2px
        
        subgraph "Public Subnet (DMZ)"
            ALB[Application Load Balancer <br> Replaces Nginx]
            Bastion[Bastion Host / Jump Server <br> (Optional for Admin Access)]
        end
        
        subgraph "Private Subnet (App Layer)"
            Tomcat[EC2 Instance <br> Apache Tomcat 9]
        end
        
        subgraph "Private Subnet (Managed Data Layer)"
            RDS[(Amazon RDS <br> MySQL)]
            MQ[Amazon MQ <br> RabbitMQ Engine]
            Cache[(Amazon ElastiCache <br> Memcached)]
        end
    end

    %% Traffic Flow
    User -- "HTTPS" --> R53
    R53 -- "Alias" --> ALB
    ALB -- "Forward Traffic" --> Tomcat
    
    %% Admin Access
    Bastion -.-> |SSH Key| Tomcat
    
    %% Backend Connections
    Tomcat -- "JDBC:3306" --> RDS
    Tomcat -- "AMQP:5671" --> MQ
    Tomcat -- "TCP:11211" --> Cache

    %% Styles
    style ALB fill:#ff9900,stroke:#333,stroke-width:2px,color:white
    style Tomcat fill:#1f77b4,stroke:#333,stroke-width:2px,color:white
    style RDS fill:#3b48cc,stroke:#333,stroke-width:2px,color:white
    style MQ fill:#3b48cc,stroke:#333,stroke-width:2px,color:white
    style Cache fill:#3b48cc,stroke:#333,stroke-width:2px,color:white
```
### Service Mapping (Local vs. AWS)

| Component | Legacy/Local Setup | AWS Cloud Native Solution (PaaS) | Benefit |
| :--- | :--- | :--- | :--- |
| **Load Balancing** | Nginx Reverse Proxy | **AWS Application Load Balancer (ALB)** | Layer 7 routing, SSL termination, and native integration. |
| **Application** | Tomcat on VM | **EC2 Instance (Private Subnet)** | Secure compute environment for the Java application. |
| **Database** | MySQL on VM | **Amazon RDS (MySQL)** | Automated backups, patching, and high availability. |
| **Caching** | Memcached on VM | **Amazon ElastiCache (Memcached)** | Fully managed, in-memory caching for sub-millisecond latency. |
| **Message Broker** | RabbitMQ on VM | **Amazon MQ (RabbitMQ)** | Managed message broker service, removing maintenance overhead. |
| **Network** | Default Network | **Custom VPC** | granular control over network isolation (Public vs. Private Subnets). |

---

## Security & Network Flow
1. **VPC Design:** A custom Virtual Private Cloud with Public and Private subnets across multiple Availability Zones.
2. **Access Control:**
   - **ALB** lives in the **Public Subnet** (Internet facing).
   - **App Server (EC2)** lives in the **Private Subnet** (Only accepts traffic from ALB).
   - **Backend Services (RDS, MQ, Cache)** live in the **Private Subnet** (Only accept traffic from the App Server).
3. **Security Groups:** Strict firewall rules ensuring least privilege access.

---
## Deployment Stack
* **VPC & Networking:** Internet Gateways, NAT Gateways, Route Tables.
* **Data Layer:** RDS, ElastiCache, Amazon MQ.
* **Compute:** EC2 with UserData script for Tomcat setup.
* **Routing:** Route 53 & Application Load Balancer.

---
