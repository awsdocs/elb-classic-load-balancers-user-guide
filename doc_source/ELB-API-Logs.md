# AWS CloudTrail Logging for Your Classic Load Balancer<a name="ELB-API-Logs"></a>

Elastic Load Balancing is integrated with AWS CloudTrail, which captures APIs calls and delivers log files to an Amazon S3 bucket that you specify\. There is no cost to use CloudTrail\. However, the standard rates for Amazon S3 apply\.

CloudTrail logs calls to the AWS APIs, including the Elastic Load Balancing API, whenever you use them directly or indirectly through the AWS Management Console\. You can use the information collected by CloudTrail to determine what API call was made, what source IP address was used, who made the call, when it was made, and so on\. You can use AWS CloudTrail to capture information about the calls to the Elastic Load Balancing API made by or on behalf of your AWS account\. For the complete list of Elastic Load Balancing API actions, see the [Elastic Load Balancing API Reference version 2012\-06\-01](http://docs.aws.amazon.com/elasticloadbalancing/2012-06-01/APIReference/)\.

To monitor other actions for your load balancer, such as when a client makes a request to your load balancer, use access logs\. For more information, see [Access Logs for Your Classic Load Balancer](access-log-collection.md)\.


+ [Enable CloudTrail Event Logging](#configure-ct-log)
+ [Elastic Load Balancing Event Information](#ct-log-files)

## Enable CloudTrail Event Logging<a name="configure-ct-log"></a>

If you haven't done so already, use the following steps to enable CloudTrail event logging for your account\.

**To enable CloudTrail event logging**

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. Choose **Get Started Now**\.

1. For **Trail name**, type a name for your trail\.

1. Leave **Apply trail to all regions** as **Yes**\.

1. Select an existing S3 bucket for your CloudTrail log files, or create a new one\. To create a new bucket, specify a unique name in **S3 bucket**\. To use an existing bucket, change **Create a new S3 bucket** to **No** and then select your bucket from **S3 bucket**\.

1. Choose **Turn on**\.

The log files are written to your S3 bucket in the following location:

```
my-bucket/AWSLogs/123456789012/CloudTrail/region/yyyy/mm/dd/
```

For more information, see the [AWS CloudTrail User Guide](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

## Elastic Load Balancing Event Information<a name="ct-log-files"></a>

The log files from CloudTrail contain event information in JSON format\. An event record represents a single AWS API call and includes information about the requested action, such as the user that requested the action, the date and the time of the request, the request parameters, and the response elements\.

The log files include event records for all AWS API calls for your AWS account, not just Elastic Load Balancing API calls\. However, you can read the log files and scan for calls to the Elastic Load Balancing API using the `eventSource` element with the value `elasticloadbalancing.amazonaws.com`\. To view information about a specific ELB API, such as `CreateLoadBalancer`, scan for the API name in the `eventName` element\.

The following example shows CloudTrail log records for a user who created a load balancer and then deleted it using the AWS CLI\. The user is identified by the `userIdentity` element\. The CLI is identified by the `userAgent` element\. The requested API calls \([CreateLoadBalancer](http://docs.aws.amazon.com/elasticloadbalancing/2012-06-01/APIReference/API_CreateLoadBalancer.html) and [DeleteLoadBalancer](http://docs.aws.amazon.com/elasticloadbalancing/2012-06-01/APIReference/API_DeleteLoadBalancer.html)\) are identified by the `eventName` element for each record\. For more information about the different elements and values in a CloudTrail log file, see [CloudTrail Event Reference](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/event_reference_top_level.html) in the *AWS CloudTrail User Guide*\.

```
{
  "Records": [
  {
     "eventVersion": "1.03",
     "userIdentity": {
        "type": "IAMUser",
        "principalId": "AIDAJDPLRKLG7UEXAMPLE",
        "arn": "arn:aws:iam::123456789012:user/Alice",
        "accountId": "123456789012",
        "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
        "userName": "Alice"
     },
     "eventTime": "2016-04-01T15:31:48Z",
     "eventSource": "elasticloadbalancing.amazonaws.com",
     "eventName": "CreateLoadBalancer",
     "awsRegion": "us-west-2",
     "sourceIPAddress": "198.51.100.1",
     "userAgent": "aws-cli/1.10.10 Python/2.7.9 Windows/7 botocore/1.4.1",
     "requestParameters": {
        "subnets": ["subnet-12345678","subnet-76543210"],
        "loadBalancerName": "my-load-balancer",
        "listeners": [{
           "protocol: "HTTP",
           "loadBalancerPort": 80,
           "instanceProtocol": "HTTP",
           "instancePort": 80
        }]
     },
     "responseElements": {
        "dNSName": "my-loadbalancer-1234567890.elb.amazonaws.com"
     },
     "requestID": "b9960276-b9b2-11e3-8a13-f1ef1EXAMPLE",
     "eventID": "6f4ab5bd-2daa-4d00-be14-d92efEXAMPLE",
     "eventType": "AwsApiCall",
     "apiVersion": "2012-06-01",
     "recipientAccountId": "123456789012"
  },
  {
     "eventVersion: "1.03",
     "userIdentity": {
        "type": "IAMUser",
        "principalId": "AIDAJDPLRKLG7UEXAMPLE",
        "arn": "arn:aws:iam::123456789012:user/Alice",
        "accountId": "123456789012",
        "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
        "userName": "Alice"
     },
     "eventTime": "2016-04-08T12:39:25Z",
     "eventSource": "elasticloadbalancing.amazonaws.com",
     "eventName": "DeleteLoadBalancer",
     "awsRegion": "us-west-2",
     "sourceIPAddress": "198.51.100.1",
     "userAgent": "aws-cli/1.10.10 Python/2.7.9 Windows/7 botocore/1.4.1",
     "requestParameters": {
        "loadBalancerName": "my-load-balancer"
     },
     "responseElements": null,
     "requestID": "f0f17bb6-b9ba-11e3-9b20-999fdEXAMPLE",
     "eventID": "4f99f0e8-5cf8-4c30-b6da-3b69fEXAMPLE"
     "eventType": "AwsApiCall",
     "apiVersion": "2012-06-01",
     "recipientAccountId": "123456789012"
  },
  . . . 
]}
```

You can also use one of the Amazon partner solutions that integrate with CloudTrail to read and analyze your CloudTrail log files\. For more information, see the [AWS partners](https://aws.amazon.com/cloudtrail/partners/) page\.