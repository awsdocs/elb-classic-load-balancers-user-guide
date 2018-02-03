# Configure the Idle Connection Timeout for Your Classic Load Balancer<a name="config-idle-timeout"></a>

For each request that a client makes through a Classic Load Balancer, the load balancer maintains two connections\. One connection is with the client and the other connection is with a registered EC2 instance\. For each connection, the load balancer manages an idle timeout that is triggered when no data is sent over the connection for a specified time period\. If no data has been sent or received by the time that the idle timeout period elapses, the load balancer closes the connection\.

By default, Elastic Load Balancing sets the idle timeout to 60 seconds for both connections\. Therefore, if the instance doesn't send some data at least every 60 seconds while the request is in flight, the load balancer can close the connection\. To ensure that lengthy operations such as file uploads have time to complete, send at least 1 byte of data before each idle timeout period elapses, and increase the length of the idle timeout period as needed\.

If you use HTTP and HTTPS listeners, we recommend that you enable the HTTP keep\-alive option for your instances\. You can enable keep\-alive in your web server settings or in the kernel settings for your instances\. Keep\-alive, when enabled, enables the load balancer to reuse connections to your instance, which reduces the CPU utilization\. To ensure that the load balancer is responsible for closing the connections to your instance, make sure that the value you set for the HTTP keep\-alive time is greater than the idle timeout setting on your load balancer\.

Note that TCP keep\-alive probes do not prevent the load balancer from terminating the connection because they do not send data in the payload\.


+ [Configure the Idle Timeout Using the Console](#config-idle-timeout-console)
+ [Configure the Idle Timeout Using the AWS CLI](#config-idle-timeout-awscli)

## Configure the Idle Timeout Using the Console<a name="config-idle-timeout-console"></a>

Use the following procedure to set the idle timeout for your load balancer\.

**To configure the idle timeout setting for your load balancer**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, under **LOAD BALANCING**, choose **Load Balancers**\.

1. Select your load balancer\.

1. On the **Description** tab, choose **Edit idle timeout**\.

1. On the **Configure Connection Settings** page, type a value for **Idle timeout**\. The range for the idle timeout is from 1 to 4,000 seconds\.

1. Choose **Save**\.

## Configure the Idle Timeout Using the AWS CLI<a name="config-idle-timeout-awscli"></a>

Use the following [modify\-load\-balancer\-attributes](http://docs.aws.amazon.com/cli/latest/reference/elb/modify-load-balancer-attributes.html) command to set the idle timeout for your load balancer:

```
aws elb modify-load-balancer-attributes --load-balancer-name my-loadbalancer --load-balancer-attributes "{\"ConnectionSettings\":{\"IdleTimeout\":30}}"
```

The following is an example response:

```
{
    "LoadBalancerAttributes": {
        "ConnectionSettings": {
            "IdleTimeout": 30
        }
    }, 
    "LoadBalancerName": "my-loadbalancer"
}
```