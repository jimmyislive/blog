---
title: "Docker image tagging in ECR"
date: 2017-12-10
draft: false
tags: ["tech"]
---

AWS provides a docker repository called [ECR](https://aws.amazon.com/ecr/). This is similar to [Docker Hub](https://hub.docker.com/), [Artifactory](https://jfrog.com/artifactory/) etc and provides a convenient place to place docker images for later use e.g. deployment.

ECR [supports Docker Image Manifest V2, Schema 2](https://aws.amazon.com/about-aws/whats-new/2017/01/amazon-ecr-supports-docker-image-manifest-v2-schema-2/), providing the ability to add multiple tags per image. I recently ran into an issue wherein I tagged an image with multiple tags at build time and then just pushed one tag, thinking all tags would appear on ECR. That did not work and my image in ECR always had only one tag. To ensure that your image has all the tags on ECR, push each tag individually.

{{< highlight bash >}}

docker tag tag1 imageName
docker tag tag2 imageName
docker tag tag3 imageName
docker push tag1
docker push tag2
docker push tag3

{{< / highlight >}}