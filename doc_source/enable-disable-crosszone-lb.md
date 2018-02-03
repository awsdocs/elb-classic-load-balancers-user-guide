# Configure Cross\-Zone Load Balancing for Your Classic Load Balancer<a name="enable-disable-crosszone-lb"></a>

If the load balancer nodes for your Classic Load Balancer can distribute requests regardless of Availability Zone, this is known as *cross\-zone load balancing*\. With cross\-zone load balancing enabled, your load balancer nodes distribute incoming requests evenly across the Availability Zones enabled for your load balancer\. Otherwise, each load balancer node distributes requests only to instances in its Availability Zone\. For example, if you have 10 instances in Availability Zone us\-west\-2a and 2 instances in us\-west\-2b, the requests are distributed evenly across all 12 instances if cross\-zone load balancing is enabled\. Otherwise, the 2 instances in us\-west\-2b serve the same number of requests as the 10 instances in us\-west\-2a\.

Cross\-zone load balancing reduces the need to maintain equivalent numbers of instances in each enabled Availability Zone, and improves your application's ability to handle the loss of one or more instances\. However, we still recommend that you maintain approximately equivalent numbers of instances in each enabled Availability Zone for higher fault tolerance\.

For environments where clients cache DNS lookups, incoming requests might favor one of the Availability Zones\. Using cross\-zone load balancing, this imbalance in the request load is spread across all available instances in the region, reducing the impact of misbehaving clients\.

When you create a Classic Load Balancer, the default for cross\-zone load balancing depends on how you create the load balancer\. With the API or CLI, cross\-zone load balancing is disabled by default\. With the AWS Management Console, the option to enable cross\-zone load balancing is selected by default\. After you create a Classic Load Balancer, you can enable or disable cross\-zone load balancing at any time\.


+ [Enable Cross\-Zone Load Balancing](#enable-cross-zone)
+ [Disable Cross\-Zone Load Balancing](#disable-cross-zone)

## Enable Cross\-Zone Load Balancing<a name="enable-cross-zone"></a>

You can enable cross\-zone load balancing for your Classic Load Balancer at any time\.

**To enable cross\-zone load balancing using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, under **LOAD BALANCING**, choose **Load Balancers**\.

1. Select your load balancer\.

1. On the **Description** tab, choose **Change cross\-zone load balancing setting**\.

1. On the **Configure Cross\-Zone Load Balancing** page, select **Enable**\.

1. Choose **Save**\.

**To enable cross\-zone load balancing using the AWS CLI**

1. Use the following [modify\-load\-balancer\-attributes](http://docs.aws.amazon.com/cli/latest/reference/elb/modify-load-balancer-attributes.html) command to set the `CrossZoneLoadBalancing` attribute of your load balancer to `true`:

   ```
   aws elb modify-load-balancer-attributes --load-balancer-name my-loadbalancer --load-balancer-attributes "{\"CrossZoneLoadBalancing\":{\"Enabled\":true}}"
   ```

   The following is an example response:

   ```
   {
      "LoadBalancerAttributes": {
        "CrossZoneLoadBalancing": {
            "Enabled": true
          }
      },
      "LoadBalancerName": "my-loadbalancer"
    }
   ```

1. \(Optional\) Use the following [describe\-load\-balancer\-attributes](http://docs.aws.amazon.com/cli/latest/reference/elb/describe-load-balancer-attributes.html) command to verify that cross\-zone load balancing is enabled for your load balancer:

   ```
   aws elb describe-load-balancer-attributes --load-balancer-name my-loadbalancer
   ```

   The following is an example response:

   ```
   {
       "LoadBalancerAttributes": {
           "ConnectionDraining": {
               "Enabled": false, 
               "Timeout": 300
           }, 
           "CrossZoneLoadBalancing": {
               "Enabled": true
           }, 
           "ConnectionSettings": {
               "IdleTimeout": 60
           }, 
           "AccessLog": {
               "Enabled": false
           }
       }
   }
   ```

## Disable Cross\-Zone Load Balancing<a name="disable-cross-zone"></a>

You can disable the cross\-zone load balancing option for your load balancer at any time\.

**To disable cross\-zone load balancing using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, under **LOAD BALANCING**, choose **Load Balancers**\.

1. Select your load balancer\.

1. On the **Description** tab, choose **Change cross\-zone load balancing**\.

1. On the **Configure Cross\-Zone Load Balancing** page, select **Disable**\.

1. Choose **Save**\.

To disable cross\-zone load balancing, set the `CrossZoneLoadBalancing` attribute of your load balancer to `false`\.

**To disable cross\-zone load balancing using the AWS CLI**

1. Use the following [modify\-load\-balancer\-attributes](http://docs.aws.amazon.com/cli/latest/reference/elb/modify-load-balancer-attributes.html) command:

   ```
   aws elb modify-load-balancer-attributes --load-balancer-name my-loadbalancer --load-balancer-attributes "{\"CrossZoneLoadBalancing\":{\"Enabled\":false}}"
   ```

   The following is an example response:

   ```
   {
      "LoadBalancerAttributes": {
        "CrossZoneLoadBalancing": {
            "Enabled": false
          }
      },
      "LoadBalancerName": "my-loadbalancer"
    }
   ```

1. \(Optional\) Use the following [describe\-load\-balancer\-attributes](http://docs.aws.amazon.com/cli/latest/reference/elb/describe-load-balancer-attributes.html) command to verify that cross\-zone load balancing is disabled for your load balancer:

   ```
   aws elb describe-load-balancer-attributes --load-balancer-name my-loadbalancer
   ```

   The following is an example response:

   ```
   {
       "LoadBalancerAttributes": {
           "ConnectionDraining": {
               "Enabled": false, 
               "Timeout": 300
           }, 
           "CrossZoneLoadBalancing": {
               "Enabled": false
           }, 
           "ConnectionSettings": {
               "IdleTimeout": 60
           }, 
           "AccessLog": {
               "Enabled": false
           }
       }
   }
   ```