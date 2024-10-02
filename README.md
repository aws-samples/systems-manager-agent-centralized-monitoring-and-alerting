## Centralized monitoring and alerting for AWS Systems Manager Agent status on managed nodes across AWS Organization
This repo hosts templates written for the AWS Blog Post "Centralized monitoring and alerting for AWS Systems Manager Agent status on managed nodes across AWS Organization" published on the [AWS Cloud Operations & Migrations blog](https://aws.amazon.com/blogs/mt/) channel.

## Overview
This post demonstrates how to monitor the status of the AWS Systems Manager Agent (SSM Agent) running on your critical managed nodes in your AWS Organization from a centralized Amazon Cloudwatch Dashboard and also configure Amazon Cloudwatch Alarms to send messages to an Amazon Simple Notification Service (SNS) topic that you define, whenever the SSM agent loses healthy connection to AWS Systems Manager. You can subscribe your email or mobile phone number to the SNS topic so that you can receive the alerts whenever the Amazon Cloudwatch Alarm is activated. The monitored critical managed nodes—which can be Amazon EC2 instances or on-premises nodes—are filtered out from the rest using specific tags that you have applied to these resources e.g. env:prod or SSMMonitoring:true

Be sure to:

* Change the title in this README
* Edit your repository description on GitHub

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

