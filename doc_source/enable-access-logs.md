# Enable Access Logs for Your Classic Load Balancer<a name="enable-access-logs"></a>

To enable access logs for your load balancer, you must specify the name of the Amazon S3 bucket where the load balancer will store the logs\. You must also attach a bucket policy to this bucket that grants Elastic Load Balancing permission to write to the bucket\.

**Important**  
The bucket and your load balancer must be in the same region\. The bucket can be owned by a different account than the account that owns the load balancer\.

**Topics**
+ [Step 1: Create an S3 Bucket](#create-s3-bucket)
+ [Step 2: Attach a Policy to Your S3 Bucket](#attach-bucket-policy)
+ [Step 3: Enable Access Logs](#enable-access-logs-console)
+ [Step 4: Verify that the Load Balancer Created a Test File in the S3 Bucket](#verify-access-logs)

## Step 1: Create an S3 Bucket<a name="create-s3-bucket"></a>

You can create an S3 bucket using the Amazon S3 console\. If you already have a bucket and want to use it to store the access logs, skip this step and go to [Step 2: Attach a Policy to Your S3 Bucket](#attach-bucket-policy) to grant Elastic Load Balancing permission to write logs to your bucket\.

**Tip**  
If you will use the console to enable access logs, you can skip this step and have Elastic Load Balancing create a bucket with the required permissions for you\. If you will use the AWS CLI to enable access logs, you must create the bucket and grant the required permissions yourself\.

**To create an Amazon S3 bucket**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create Bucket**\.

1. On the **Create a Bucket** page, do the following:

   1. For **Bucket Name**, type a name for your bucket \(for example, `my-loadbalancer-logs`\)\. This name must be unique across all existing bucket names in Amazon S3\. In some regions, there might be additional restrictions on bucket names\. For more information, see [Bucket Restrictions and Limitations](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html) in the *Amazon Simple Storage Service Developer Guide*\.

   1. For **Region**, select the region where you created your load balancer\.

   1. Choose **Create**\.

## Step 2: Attach a Policy to Your S3 Bucket<a name="attach-bucket-policy"></a>

After you've created or identified your S3 bucket, you must attach a policy to the bucket\. Bucket policies are a collection of JSON statements written in the access policy language to define access permissions for your bucket\. Each statement includes information about a single permission and contains a series of elements\.

If your bucket already has an attached policy, you can add the statements for the Elastic Load Balancing access log to the policy\. If you do so, we recommend that you evaluate the resulting set of permissions to ensure that they are appropriate for the users that need access to the bucket for access logs\.

**Tip**  
If you will use the console to enable access logs, you can skip this step and have Elastic Load Balancing create a bucket with the required permissions for you\.

**To attach a policy statement to your bucket**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Select the bucket, and then choose **Permissions**\.

1. Choose **Bucket Policy**\. If your bucket already has an attached policy, you can add the required statement to the existing policy\.

1. Choose **Policy generator**\. On the **AWS Policy Generator** page, do the following:

   1. For **Select Type of Policy**, select **S3 Bucket Policy**\.

   1. For **Effect**, select **Allow** to allow access to the S3 bucket\.

   1. For **Principal**, type the account ID for Elastic Load Balancing to grant Elastic Load Balancing access to the S3 bucket\. Use the account ID that corresponds to the region for your load balancer and bucket\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/elasticloadbalancing/latest/classic/enable-access-logs.html)

      \* This region requires a separate account\. For more information, see [AWS GovCloud \(US\-West\)](https://aws.amazon.com/govcloud-us/)\.

      \*\* This region requires a separate account\. For more information, see [China \(Beijing\)](http://www.amazonaws.cn/en/)\.

   1. For **Actions**, select `PutObject` to allow Elastic Load Balancing to store objects in the S3 bucket\.

   1. For **Amazon Resource Name \(ARN\)**, type the ARN of the S3 bucket in the following format:

      ```
      arn:aws:s3:::bucket/prefix/AWSLogs/aws-account-id/*
      ```

      You must specify the ID of the AWS account that owns the load balancer, and you should not include the hyphens\. For example:

      ```
      arn:aws:s3:::my-loadbalancer-logs/my-app/AWSLogs/123456789012/*
      ```

      Note that if you are using `us-gov-west-1` region, use `arn:aws-us-gov:` instead of `arn:aws:` in the ARN\.

   1. Choose **Add Statement**, **Generate Policy**\. The policy document should be similar to the following:

      ```
      {
        "Id": "Policy1429136655940",
        "Version": "2012-10-17",
        "Statement": [
          {
            "Sid": "Stmt1429136633762",
            "Action": [
              "s3:PutObject"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:s3:::my-loadbalancer-logs/my-app/AWSLogs/123456789012/*",
            "Principal": {
              "AWS": [
                "797873946194"
              ]
            }
          }
        ]
      }
      ```

   1. If you are creating a new bucket policy, copy the entire policy document, and then choose **Close**\.

      If you are editing an existing bucket policy, copy the new statement from the policy document \(the text between the \[ and \] of the `Statement` element\), and then choose **Close**\.

1. Go back to the Amazon S3 console and paste the policy into the text area as appropriate\.

1. Choose **Save**\.

## Step 3: Enable Access Logs<a name="enable-access-logs-console"></a>

You can enable access logs using the AWS Management Console or the AWS CLI\. Note that when you enable access logs using the console, you can have Elastic Load Balancing create the bucket for you with necessary permissions for the load balancer to write to your bucket\.

Use the following example to capture and deliver logs to your S3 bucket every 60 minutes \(the default interval\)\.

**To enable access logs for your load balancer using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, under **LOAD BALANCING**, choose **Load Balancers**\.

1. Select your load balancer\.

1. On the **Description** tab, choose **Configure Access Logs**\.

1. On the **Configure Access Logs** page, do the following:

   1. Choose **Enable access logs**\.

   1. Leave **Interval** as the default, `60 minutes`\.

   1. For **S3 location**, type the name of your S3 bucket, including the prefix \(for example, `my-loadbalancer-logs/my-app`\)\. You can specify the name of an existing bucket or a name for a new bucket\.

   1. \(Optional\) If the bucket does not exist, choose **Create this location for me**\. You must specify a name that is unique across all existing bucket names in Amazon S3 and follows the DNS naming conventions\. For more information, see [Rules for Bucket Naming](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules) in the *Amazon Simple Storage Service Developer Guide*\.

   1. Choose **Save**\.

**To enable access logs for your load balancer using the AWS CLI**  
First, create a \.json file that enables Elastic Load Balancing to capture and deliver logs every 60 minutes to the S3 bucket that you created for the logs:

```
{ 
  "AccessLog": {
    "Enabled": true,
    "S3BucketName": "my-loadbalancer-logs",
    "EmitInterval": 60,
    "S3BucketPrefix": "my-app"
  }
}
```

To enable access logs, specify the \.json file in the [modify\-load\-balancer\-attributes](https://docs.aws.amazon.com/cli/latest/reference/elb/modify-load-balancer-attributes.html) command as follows:

```
aws elb modify-load-balancer-attributes --load-balancer-name my-loadbalancer --load-balancer-attributes file://my-json-file.json
```

The following is an example response:

```
{
    "LoadBalancerAttributes": {
        "AccessLog": {
            "Enabled": true,
            "EmitInterval": 60,
            "S3BucketName": "my-loadbalancer-logs",
            "S3BucketPrefix": "my-app"
        }
    },
    "LoadBalancerName": "my-loadbalancer"
}
```

## Step 4: Verify that the Load Balancer Created a Test File in the S3 Bucket<a name="verify-access-logs"></a>

After the access log is enabled for your load balancer, Elastic Load Balancing validates the S3 bucket and creates a test file\. You can use the S3 console to verify that the test file was created\.

**To verify that Elastic Load Balancing created a test file in your S3 bucket**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Select your S3 bucket\.

1. Navigate to the test log file\. The path should be as follows:

   ```
   my-bucket/prefix/AWSLogs/123456789012/ELBAccessLogTestFile
   ```

**To manage the S3 bucket for your access logs**  
After you enable access logging, be sure to disable access logging before you delete the bucket with your access logs\. Otherwise, if there is a new bucket with the same name and the required bucket policy created in an AWS account that you don't own, Elastic Load Balancing could write the access logs for your load balancer to this new bucket\.