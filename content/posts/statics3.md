---
title: "Automated setup of a static site on S3 using Terraform"
date: 2019-06-16
draft: false
tags: ["tech"]
---
Many people, businesses don't need a dynamic website. they just need a placeholder on the web that gives information to customers about their business, contact info, address etc. A static website is a pretty easy way to achieve this. AWS S3 allows you to host static content and serve it up as webpages. You can set it up manually by following instructions [here](https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html).

This post provides scripts for automating it via [Terraform](https://www.terraform.io/).

**NOTE**: One thing to remember is that aws [does not support https for static websites](https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteEndpoints.html). If that is important to you (and it should be!) use other providers such as [Netlify](https://www.netlify.com/) for your static needs.
    
  (*A reader pointed out that this is not entirely correct. Actually you can have https by fronting your static site via cloudfront / route53. For the curious, [here is an aws knowledge base article](https://aws.amazon.com/premiumsupport/knowledge-center/cloudfront-https-requests-s3/) that can help you do it. Automating this into the terraform manifests listed on this post is left as an exercise for the reader.*)

  NOTE: [You cannot request a certificate for Amazon-owned domain names such as those ending in amazonaws.com, cloudfront.net, or elasticbeanstalk.com.](https://docs.aws.amazon.com/acm/latest/userguide/troubleshooting-requests.html#failed-additional-verification-required) i.e. if you use a vanilla static domain name ending in *.amazonaws.com and not a CNAME with cloudfront, you will not be able to use https even with cloudfront.


We will be using a `Makefile` as a top level interface for everything. So all you have to do to bring up a static site should be:

{{< highlight makefile >}}
make tf-init
make tf-apply
make push-content NAMESPACE={your-namespace}
{{< / highlight >}}

In the above `namespace` is any username you wish and all your static assets will be hosted in a S3 bucket named `{namespace}-demos3staticweb`.

The crux of the terraform script is a section like:

{{< highlight terraform >}}
resource "aws_s3_bucket" "static_website_bucket" {
  bucket = "${var.namespace}-demos3staticweb"
  acl = "public-read"
  
  tags {
    Name = "DemoAWSS3StaticWeb"
    Environment = "production"
  } 
  
  policy = <<EOF
{ 
  "Version":"2012-10-17",
  "Statement":[{
    "Sid":"PublicReadGetObject",
        "Effect":"Allow",
      "Principal": "*",
      "Action":["s3:GetObject"],
      "Resource":["arn:aws:s3:::${var.namespace}-demos3staticweb/*"
      ]
    } 
  ] 
} 
EOF

  website {
    index_document = "index.html"
  } 
} 
{{< / highlight >}}

As you can see above, it sets the ACL of the bucket to be `public-read` which allows the world to be able to read its content. Remember that the content uploaded to that bucket will have to have its Content-Type set appropriately to html/css etc, else you will get interesting error messages.

After applying terraform, you should be able to see your site at `http://{namespace}-demos3staticweb.s3-website-us-west-2.amazonaws.com`. e.g. you can see the example I uploaded [here](http://jimmyislive-demos3staticweb.s3-website-us-west-2.amazonaws.com).

Instructions and details are on [github](https://github.com/jimmyislive/demoS3staticweb)

