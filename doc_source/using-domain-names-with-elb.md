# Configure a Custom Domain Name for Your Classic Load Balancer<a name="using-domain-names-with-elb"></a>

Each Classic Load Balancer receives a default Domain Name System \(DNS\) name\. This DNS name includes the name of the AWS region in which the load balancer is created\. For example, if you create a load balancer named `my-loadbalancer` in the US West \(Oregon\) region, your load balancer receives a DNS name such as `my-loadbalancer-1234567890.us-west-2.elb.amazonaws.com`\. To access the website on your instances, you paste this DNS name into the address field of a web browser\. However, this DNS name is not easy for your customers to remember and use\.

If you'd prefer to use a friendly DNS name for your load balancer, such as `www.example.com`, instead of the default DNS name, you can create a custom domain name and associate it with the DNS name for your load balancer\. When a client makes a request using this custom domain name, the DNS server resolves it to the DNS name for your load balancer\.


+ [Associating Your Custom Domain Name with Your Load Balancer Name](#dns-associate-custom-elb)
+ [Configure DNS Failover for Your Load Balancer](#configure-dns-failover)
+ [Disassociating Your Custom Domain Name from Your Load Balancer](#dns-disassociate-custom-elb)

## Associating Your Custom Domain Name with Your Load Balancer Name<a name="dns-associate-custom-elb"></a>

First, if you haven't already done so, register your domain name\. The Internet Corporation for Assigned Names and Numbers \(ICANN\) manages domain names on the Internet\. You register a domain name using a *domain name registrar*, an ICANN\-accredited organization that manages the registry of domain names\. The website for your registrar will provide detailed instructions and pricing information for registering your domain name\. For more information, see the following resources:

+ To use Amazon Route 53 to register a domain name, see [Registering Domain Names Using Route 53](http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/registrar.html) in the *Amazon Route 53 Developer Guide*\.

+ For a list of accredited registrars, see the [Accredited Registrar Directory](http://www.internic.net/regist.html)\.

Next, use your DNS service, such as your domain registrar, to create a CNAME record to route queries to your load balancer\. For more information, see the documentation for your DNS service\.

Alternatively, you can use Route 53 as your DNS service\. You create a *hosted zone*, which contains information about how to route traffic on the Internet for your domain, and an *alias resource record set*, which routes queries for your domain name to your load balancer\. Route 53 doesn't charge for DNS queries for alias record sets, and you can use alias record sets to route DNS queries to your load balancer for the zone apex of your domain \(for example, `example.com`\)\. For information about transferring DNS services for existing domains to Route 53, see [Configuring Route 53 as Your DNS Service](http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/creating-migrating.html) in the *Amazon Route 53 Developer Guide*\.

**To create a hosted zone and an alias record set for your domain using Route 53**

1. Open the Route 53 console at [https://console\.aws\.amazon\.com/route53/](https://console.aws.amazon.com/route53/)\.

1. If you are new to Route 53, you see a welcome page; choose **Get Started Now** under **DNS Management**\. Otherwise, choose **Hosted Zones** in the navigation pane\.

1. Choose **Create Hosted Zone**\.

1. For **Create Hosted Zone**, do the following:

   1. For **Domain Name**, type your domain name\.

   1. Leave the default type, Public Hosted Zone, and optionally enter a comment\.

   1. Choose **Create**\.

1. Select the hosted zone that you just created for your domain\.

1. Choose **Go to Record Sets**\.

1. Choose **Create Record Set**\.

1. For **Create Record Set**, do the following:

   1. Leave the default name, which is the name of your domain\.

   1. For **Type**, select **A — IPv4 address**\. 

   1. For **Alias**, choose **Yes**\. An alias enables Route 53 to associate your domain name with an AWS resource, such as a load balancer\.

   1. Choose **Alias Target**\. Select your load balancer from the list\. The console adds the `dualstack` prefix\.

   1. For **Routing Policy**, select **Simple**\.

   1. Leave **Evaluate Target Health** set to **No**\.

   1. Choose **Create**\.

## Configure DNS Failover for Your Load Balancer<a name="configure-dns-failover"></a>

If you use Route 53 to route DNS queries to your load balancer, you can also configure DNS failover for your load balancer using Route 53\. In a failover configuration, Route 53 checks the health of the registered EC2 instances for the load balancer to determine whether they are available\. If there are no healthy EC2 instances registered with the load balancer, or if the load balancer itself is unhealthy, Route 53 routes traffic to another available resource, such as a healthy load balancer or a static website in Amazon S3\.

For example, suppose that you have a web application for `www.example.com`, and you want redundant instances running behind two load balancers residing in different regions\. You want the traffic to be primarily routed to the load balancer in one region, and you want to use the load balancer in the other region as a backup during failures\. If you configure DNS failover, you can specify your primary and secondary \(backup\) load balancers\. Route 53 directs traffic to the primary load balancer if it is available, or to the secondary load balancer otherwise\.

**To configure DNS failover for two load balancers using Route 53**

1. Open the Route 53 console at [https://console\.aws\.amazon\.com/route53/](https://console.aws.amazon.com/route53/)\.

1. In the navigation pane, choose **Hosted Zones**\.

1. Select your hosted zone\.

1. Choose **Go to Record Sets**\.

1. Choose **Create Record Set**\.

1. For **Name**, the default value is the name of your domain \(for example, `example.com`\)\. To route DNS queries for a subdomain \(for example, `www.example.com`\) to your load balancer, type the name of the subdomain\.

1. For **Type**, select **A \- IPv4 address**\.

1. For **Alias**, select **Yes**

1. For **Alias Target**, select your primary load balancer\. The console adds the `dualstack` prefix\.

   Note that the value of **Alias Hosted Zone ID** is based on the load balancer that you selected\.

1. For **Routing Policy**, select **Failover**\.

1. For **Failover Record Type**, select **Primary**\.

1. For **Set ID**, type an ID for the record set or use the default value\.

1. For **Evaluate Target Health**, select **Yes**\.

1. For **Associate with Health Check**, select **No**\.

1. Choose **Create**\.

1. Repeat the same steps to create an alias record set for your secondary load balancer, with the following exceptions:

   + For **Alias Target**, select your secondary load balancer\.

   + For **Failover Record Type**, select **Secondary**\.

   + For **Evaluate Target Health**, select **Yes** to evaluate the health of the secondary load balancer\. If the secondary load balancer is unhealthy, Route 53 routes traffic to the primary load balancer\. If you select **No**, Route 53 assumes that the secondary load balancer is healthy and routes traffic to it whenever the primary load balancer is unhealthy\.

For more information, see [Configuring Route 53 Active\-Active and Active\-Passive Failover](http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-configuring.html) in the *Amazon Route 53 Developer Guide*\.

## Disassociating Your Custom Domain Name from Your Load Balancer<a name="dns-disassociate-custom-elb"></a>

You can disassociate your custom domain name from a load balancer instance by first deleting the resource record sets in your hosted zone and then deleting the hosted zone\. For more information, see [Creating, Changing, and Deleting Resource Record Sets](http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/RRSchanges.html) and [Deleting a Hosted Zone](http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/DeleteHostedZone.html) in the *Amazon Route 53 Developer Guide*\.