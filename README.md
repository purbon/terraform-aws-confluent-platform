# terraform-aws-confluent-platform

Terraform Module(s) for Deploying the Confluent Platform within AWS.

# Features

## Feature Metric

| CP Component    | EC2 Instance | EBS Volumes | Route53 DNS | Security Groups | Load Balancers | Multi AZ | Auto Scaling Groups | Multi Cluster | Monitoring |
|:--------------- |:------------:|:-----------:|:-----------:|:---------------:|:--------------:|:--------:|:-------------------:|:-------------:|:----------:|
| Zookeeper       | X            | X           | X           | X               | N/A            | X        | N/A                 | N/A           | X          |
| Kafka Broker    | X            | X           | X           | X               |                | X        | N/A                 | N/A           | X          |
| Kafka Connect   | X            | X           | X           | X               |                | X        |                     |               | X          |
| ksqlDB          | X            | X           | X           | X               |                | X        |                     |               | X          |
| Rest Proxy      | X            | X           | X           | X               |                | X        |                     | N/A           | X          |
| Schema Registry | X            | X           | X           | X               |                | X        | N/A                 | N/A           | X          |
| Control Center  | X            | X           | X           | X               | N/A            | X        | N/A                 | N/A           | X          |

## Feature Limitations

1. Out of the box all nodes and security groups are required to live in the same VPC

NOTE: If you leverage the individual component modules, some of these limitations can be worked around.
These limitation just haven't be able to be baked into a single unified parent module yet, or may not be possible to at all.

## Defining separate providers

As of 2.4.3 you can now define separate providers for your EC2 instance creation and your DNS creation. 
This is to support those users that may need to use separate IAM accounts to for DNS then they do to create EC2, EBS, and SecGroups.

These provider alias are as follows:

```
provider "aws" {
    alias = "default"
}
provider "aws" {
    alias = "dns"
}
```

Example of how to pass these references:

```
module "shared-cp-aws" {
    source  = "nerdynick/confluent-platform/aws"
    version = "2.4.4"
    
    providers = {
        aws.default = aws.default
        aws.dns = aws.dns
    }
    
    ....
}
```

## Monitoring

Currently Prometheus and Jolokia are the only supported monitoring platforms.
The contained features around them will Setup Security Groups and EC2 instance tags appropriate to each component.
The EC2 tags are designed to allow you to use Prometheus' EC2 Service Discovery feature, 
[as discussed here](https://medium.com/investing-in-tech/automatic-monitoring-for-all-new-aws-instances-using-prometheus-service-discovery-97d37a5b2ea2), 
to find each component and automaticly start reading from that component.