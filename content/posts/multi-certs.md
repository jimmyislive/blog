---
title: "Adding multiple certs to an ALB"
date: 2018-02-16T21:35:45-08:00
draft: false
tags: ["aws", "alb", "ec2", "elb", "certificates"]
---

If you are using AWS and ALBs, you have the ability to [add multiple certs to the ALB](https://redirect.viglink.com/?u=https%3A%2F%2Faws.amazon.com%2Fblogs%2Faws%2Fnew-application-load-balancer-sni%2F&ref=https%3A%2F%2Ft.umblr.com&key=440c1d93533c952bd78a45f41d1cdb78&subId=t%3AGEwx1T_6jQbUrEStrh5JQg&loc=https%3A%2F%2Fjimmyislive.tumblr.com%2Fpost%2F170967009405%2Fadding-multiple-certs-to-an-alb) and terminate SSL there.

While it is easy to do via the AWS console, their documentation is not that clear as to how to do it in an automated way. The following is the code snippet, written with [troposphere](https://github.com/cloudtools/troposphere), to show you how to do it.

First create a HTTS listener with a certificate:
{{< highlight python >}}
def create_lb_listener_https(alb, default_target_group, param_cert_one):
   return Listener(‘LoadBalancerListenerHTTPS’,
                   Port='443’,
                   Protocol='HTTPS’,
                   LoadBalancerArn=Ref(alb),
                   DefaultActions=[Action(Type='forward’,
                                   TargetGroupArn=Ref(default_target_group))],
                   # Note, only one cert ARN can be specified here, else you will get an error
                   Certificates=[
                       Certificate(CertificateArn=Ref(param_cert_one)),
                   ]
                )
{{< /highlight >}}

Now we have one listener, with a cert, attached to the ALB:

{{< highlight python >}}
listener_arn = create_lb_listener_https(alb, default_target_group, param_cert_primary)
{{< /highlight >}}

We can now add more certs via a ListenerCertificate: 
{{< highlight python >}}
def make_listener_certificate_two(listener_arn, param_cert_two):
   return ListenerCertificate('ListenerCertificate’,
                              Certificates=[
                                   Certificate(CertificateArn=Ref(param_cert_two)),
                              ],
                              ListenerArn=Ref(listener_arn),
                              Condition=condition
                              )

def make_listener_certificate_three(listener_arn, param_cert_three):
  return ListenerCertificate('ListenerCertificate’,
                             Certificates=[
                                  Certificate(CertificateArn=Ref(param_cert_three)),
                             ],
                             ListenerArn=Ref(listener_arn),
                             Condition=condition
                             )
{{< /highlight >}}

The above is not describer clearly in the AWS docs. Hopefully this saves you some time if you run into this.
