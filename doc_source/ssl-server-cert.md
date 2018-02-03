# SSL/TLS Certificates for Classic Load Balancers<a name="ssl-server-cert"></a>

If you use HTTPS \(SSL or TLS\) for your front\-end listener, you must deploy an SSL/TLS certificate on your load balancer\. The load balancer uses the certificate to terminate the connection and then decrypt requests from clients before sending them to the instances\.

The SSL and TLS protocols use an X\.509 certificate \(SSL/TLS server certificate\) to authenticate both the client and the back\-end application\. An X\.509 certificate is a digital form of identification issued by a certificate authority \(CA\) and contains identification information, a validity period, a public key, a serial number, and the digital signature of the issuer\.

You can create a certificate using AWS Certificate Manager or a tool that supports the SSL and TLS protocols, such as OpenSSL\. You will specify this certificate when you create or update an HTTPS listener for your load balancer\.

## Creating an SSL/TLS Certificate Using AWS Certificate Manager<a name="create-certificate-acm"></a>

We recommend that you use AWS Certificate Manager \(ACM\) to create or import certificates for your load balancer\. ACM integrates with Elastic Load Balancing so that you can deploy the certificate on your load balancer\. To deploy a certificate on your load balancer, the certificate must be in the same region as the load balancer\. For more information, see [Request a Certificate](http://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request.html) or [Importing Certificates](http://docs.aws.amazon.com/acm/latest/userguide/import-certificate.html) in the *AWS Certificate Manager User Guide*\.

To allow an IAM user to deploy the certificate on your load balancer using the AWS Management Console, you must allow access to the ACM `ListCertificates` API action\. For more information, see [Listing Certificates](http://docs.aws.amazon.com/acm/latest/userguide/assets-inline.html#policy-list-certificates) in the *AWS Certificate Manager User Guide*\.