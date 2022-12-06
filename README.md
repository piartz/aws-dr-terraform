# AWS Regional Disaster Recovery with Terraform

This project aims to provide a PoC environment to implement several strategies on AWS disaster recovery. This repository creates the standard service, composed in essence by an autoscaler group of two EC2 machines providing an HTTP service with an access via a load balancer (ALB). For the replication environment and the implemented disaster recovery strategies on another region, see [aws-disaster-recovery-with-terraform-replication-zone](https://gitlab.com/xebia-security/aws-disaster-recovery-with-terraform-replication-zone).

By default, this project is deployed on `eu-central-1`, with two subnets on two different AZs (`eu-central-1a` and `eu-central-1b`). You can change these settings by editing the correspondent options on [providers.tf](https://gitlab.com/xebia-security/aws-dr-terraform/-/blob/main/providers.tf) and [variables.tf](https://gitlab.com/xebia-security/aws-dr-terraform/-/blob/main/variables.tf), and also the EC2 AMIs as they are region-dependent. 

The project has been built using resources from the **free-tier** as of December 2022. While the deployed infrastructure should be free of charge, any changes to terms and conditions may result in associated costs. Please contact XSEC or create a Pull Request in that case, so the project remains as free as possible. 

## Cloud Diagram

The project will generate a subset of the following diagram (*source: [AWS Disaster Recovery Workloads](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html#warm-standby)*). This repository implements **the region in the left**, which corresponds to the main service. 

![Basic diagram for a main service with Pilot Light](aws-pilot-light.png "Basic diagram for a main service with Pilot Light")

In our case, we are offering **the front-end layer** of a three-tier architecture. For simplicity and cost-efficiency on a PoC, backend instances and the storage layer have been omitted. Furthermore, the Route 53 DNS association has not been included for convenience, avoiding the reservation of a domain only for demonstration purposes. In a real-world use case, Route 53 must be configured so the primary service is set as the default routing, and the DR Region endpoint is configured as the secondary path when the main one is not in a healthy state. 

## How to deploy

This project has been built on Terraform 1.3.6. While other versions may still work, it has not been tested. Update to 1.3.6 or give it a run and see if it works as-is :)
- Clone this repository and `cd` into it.
- Use `terraform init` to initialize your workspace. 
- Make sure you [add your AWS credentials for the project](https://blog.knoldus.com/add-aws-credentials-in-terraform/).
- Use `terraform plan` to check the changes and `terraform apply` to execute the deployment. 
- You can now log in into the AWS Management Console and check all resources have been deployed correctly. 
- Go to EC2 -> Load Balancers and check the DNS name for the created load balancer. 
- In your browser, access the DNS name over HTTP. 
- You will see a website informing you about the hostname of the service. If you refresh the page several times, you can see it changes since it is a load balancer calling two possible instances. 
