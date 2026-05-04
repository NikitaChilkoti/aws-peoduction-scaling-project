## The story behind

After stabilizing the system, the focus I shifted to was on improving scalability, availability, and observability. So, this is an extended part of [aws-system-reliability-project](https://github.com/NikitaChilkoti/aws-system-reliability-project.git)


Single point of failure is an absolute no, so worked on 
* ALB (Application load balancer) - For distributed traffic, health checks, fault tolerance
* ASG (Auto scaling group) - To handle high traffic on scale, to replace failed instances
* Launch template - To remove repetitive manual work of setting up EC2 instances,
* Cloudwatch logs - To unsee those messy logs, and improve centralized logging, and easier debugging
* Dashboard setup - for visual system monitoring
* Iac (Infra as code) - To sutomate infra creation, ensuring consistency, and enabling repeatable deployments minus the manual error using Terraform

This project added learnings and experience at so many levels.
