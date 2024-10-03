## Centralized monitoring and alerting for AWS Systems Manager Agent status on managed nodes across AWS Organization
This repo hosts templates written for the AWS Blog Post "Centralized monitoring and alerting for AWS Systems Manager Agent status on managed nodes across AWS Organization" published on the [AWS Cloud Operations & Migrations blog](https://aws.amazon.com/blogs/mt/) channel.

## Overview
This post demonstrates how to monitor the status of the AWS Systems Manager Agent (SSM Agent) running on your critical managed nodes in your AWS Organization from a centralized Amazon Cloudwatch Dashboard and also configure Amazon Cloudwatch Alarms to send messages to an Amazon Simple Notification Service (SNS) topic that you define, whenever the SSM agent loses healthy connection to AWS Systems Manager. You can subscribe your email or mobile phone number to the SNS topic so that you can receive the alerts whenever the Amazon Cloudwatch Alarm is activated. The monitored critical managed nodes—which can be Amazon EC2 instances or on-premises nodes—are filtered out from the rest using specific tags that you have applied to these resources e.g. `env:prod` or `SSMMonitoring:true`.

## Workflow

![Alt text](./images/centralized-ssm-monitoring.png)

The solution uses an AWS Lambda function to check the health of the SSM agent connection on your critical managed nodes across your AWS Organization (or specific AWS Accounts), using the specific tags and regions you define, and report their `PingStatus` metric to a centralized Amazon Cloudwatch dashboard. Whenever your managed nodes have an healthy connection to Systems Manager, the `PingStatus` attribute of the node from the `DescribeInstanceInformation` API reports `Online`. The Lambda function creates a `PingStatus` metric in Amazon Cloudwatch, such that when the PingStatus is `Online`, the metric is value `0`, otherwise it is `1`. The Lambda Function also creates Amazon Cloudwatch Alarms for the critical managed nodes and configures alerts to send messages to your defined SNS topic when activated. This Lambda function is periodically invoked by an Amazon EventBridge custom rule. You can define how frequent you want the Lambda Function to be invoked by defining your frequency in the Amazon EventBridge rule.

The workflow of the architecture you’ll create is as follows:

1.	An IAM role to be assumed by the Lambda Function in the target account(s). 
2.	An Amazon EventBridge rule that invokes a Lambda Function on a schedule e.g. every 15 minutes. 
3.	An AWS Lambda Function checks the SSM agent health status of your managed nodes with specific tags that you provide and creates a custom PingStatus metric in Amazon CloudWatch. The Lambda Function also calls other CloudWatch APIs to configure CloudWatch Alarm with the target Amazon SNS Topic for the PingStatus metrics (if desired), and create an Amazon Cloud
Watch Dashboard. 
4.	If a running instance with the tag does not appear in Systems Manager or has a Ping Status other than `Online`, the PingStatus for the managed node is reported as *Missing* and the metric value is set to `1`. If the PingStatus metric is `Online`, the metric is set to `0`. 
5.	Whenever the PingStatus metric for any of the managed nodes flips to `1`, the alarm is activated and the notification is sent to the subscribers of your Amazon SNS Topic. 
6.	If an instance that was monitored by the solution is terminated or the monitoring tags are removed, the corresponding alarm is deleted the next time the Lambda function is invoked and the CloudWatch dashboard is updated. 

## Deployment

**Prerequisites**
For this walkthrough, you should have the following: 

1. An  AWS Account or list of AWS Accounts or AWS Organization.
2. AWS SSM Managed nodes – on-premises or on Amazon EC2.
3. Tags applied to the managed nodes e.g. `env:prod` or `SSMMonitoring:true`.
4. An Amazon SNS Topic with subscribers in central dashboard region. The subscribers can be emails, SMS etc.  

**Walkthrough**
There are two CloudFormation templates you will deploy for this solution:

1.	Create an IAM role using [CloudFormation template](SSMAgent_IAM_Role.yml) in all your accounts in AWS Organization or specific AWS accounts. These IAM roles will be assumed by the SSMPingStatus Lambda Function to be created in next step. 
2.	Deploy the SSMPingStatus Monitoring solution by launching a CloudFormation stack in your desired central dashboard region and account using the provided [CloudFormation template](SSMAgent_status.yml). This CloudFormation Template will create the required components – AWS Lambda Function, CloudWatch Alarms (optional), Amazon EventBridge Rule, and AWS CloudWatch Dashboard. 

For the detailed instructions of deploying above steps, please follow the Walkthrough section of the blog.

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

