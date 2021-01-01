<p align="center">
  <a href="https://dev.to/vumdao">
    <img alt="Deploy Python Lambda functions with container images" src="https://dev-to-uploads.s3.amazonaws.com/i/1il5y8ycp87qqbh9118t.png" width="500" />
  </a>
</p>
<h1 align="center">
  Deploy Python Lambda functions with container images
</h1>


## You can deploy your Lambda function code as a container image. AWS provides the following resources to help you build a container image for your Python function:
- AWS base images for Lambda
  - These base images are preloaded with a language runtime and other components that are required to run the image on Lambda. AWS provides a Dockerfile for each of the base images to help with building your container image.

- Open-source runtime interface clients
  - If you use a community or private enterprise base image, add a runtime interface client to the base image to make it compatible with Lambda.


### 🚀 **[Create Docker image container](#-Create-Docke-image-container)**
#### **1. Create an image from an alternative base image**
[`Dockerfile.alter`](https://github.com/vumdao/lambda-container-image/blob/master/Dockerfile.alter)
```
FROM python:3.8-buster

RUN apt-get update && \
    apt-get install -y python3-pip && \
    pip3 install awslambdaric

COPY app.py ./

ENTRYPOINT [ "/usr/local/bin/python", "-m", "awslambdaric" ]
CMD ["app.handler"]
```

#### **2. Create an image from AWS base image**
[`Dockerfile.aws`](https://github.com/vumdao/lambda-container-image/blob/master/Dockerfile.aws)
```
FROM amazon/aws-lambda-python:3.8

COPY app.py ./

CMD ["app.handler"]
```

### 🚀 **[Create `app.py` as lambda handler](#-Create-`app.py`-as-lambda-handler)**
```
import sys


def handler(event, context):
    return 'Hello from AWS Lambda using Python' + sys.version + '!'
```

### 🚀 **[Build and push the image to ECR](#-Build0and-push-the-image-to-ECR)**
```
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 111111111111.dkr.ecr.ap-southeast-1.amazonaws.com
docker build -t pythonlambda .
docker tag pythonlambda:latest 111111111111.dkr.ecr.ap-southeast-1.amazonaws.com/pythonlambda:latest
docker push 111111111111.dkr.ecr.ap-southeast-1.amazonaws.com/pythonlambda:latest
```

### 🚀 **[Create Lambda container image](#-Create-Lambda-container-imager)**
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/jjqxhyu3twzave259jim.png)

### 🚀 **[Test the lambda function](#-Test-the-lambda-function)**
#### **1. Invoke lambda function**
- Start container
```
docker rm -f lambdaimagetest
docker run -d --name lambdaimagetest -it pythonlambda:latest bash
docker exec -it lambdaimagetest bash
```
- Invoke lambda function
```
⚡ $ aws lambda invoke --function-name python-lambda-image --region ap-southeast-1 outfile                                                                                                                          
{
    "StatusCode": 200,
    "ExecutedVersion": $LATEST"
}
⚡ $ cat outfile                                                                                                                                                                                                    
"Hello from AWS Lambda using Python3.8.5 (default, Aug  5 2020, 08:22:02) \n[GCC 8.3.0]!"
```

#### **2. Call lambda API from local container**
- This should be performed from AWS image base
- Run container
```
⚡ $ docker run -d --name testapi -p 9000:8080 pythonlambda:latest
```
- Call API
```
⚡ $ curl -XPOST "http://localhost:9000/2015-03-31/functions/function/invocations" -d '{}'
"Hello from AWS Lambda using Python3.8.6 (default, Dec 16 2020, 01:05:15) \n[GCC 7.3.1 20180712 (Red Hat 7.3.1-11)]!"
```
- Deploy new image
![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/bdrihq86bqmtnm4ei5xe.png)
- Invoke lambda function
```
⚡ $ aws lambda invoke --function-name python-lambda-image --region ap-southeast-1 outfile                                                                                                                          
{
    "StatusCode": 200,
    "ExecutedVersion": "$LATEST"
}
⚡ $ cat outfile
"Hello from AWS Lambda using Python3.8.6 (default, Dec 16 2020, 01:05:15) \n[GCC 7.3.1 20180712 (Red Hat 7.3.1-11)]!"
```

**Read More**
- [Pelican-resume with docker-compose and AWS + CDK](https://dev.to/vumdao/pelican-resume-with-docker-compose-and-aws-cdk-33e5)
- [Using Helm Install Botkube Integrate With Slack On EKS](https://dev.to/vumdao/using-helm-install-botkube-integrate-with-slack-on-eks-gmn)
- [Ansible AWS EC2 Dynamic Inventory Plugin](https://dev.to/vumdao/ansible-aws-ec2-dynamic-inventory-plugin-3bme)
- [How To List All Enabled Regions Within An AWS account](https://dev.to/vumdao/list-all-enabled-regions-within-an-aws-account-4oo7)
- [Using AWS KMS In AWS Lambda](https://dev.to/vumdao/using-aws-kms-in-aws-lambda-2jm2)
- [Create AWS Backup Plan](https://dev.to/vumdao/create-aws-backup-plan-a0f)
- [Techniques For Writing Least Privilege IAM Policies](https://dev.to/vumdao/techniques-for-writing-least-privilege-iam-policies-4fc7)
- [EKS Persistent Storage With EFS Amazon Service](https://dev.to/vumdao/eks-persistent-storage-with-efs-amazon-service-14ei)
- [Create k8s Cronjob To Schedule Delete Expired Files](https://dev.to/vumdao/create-k8s-cronjob-to-schedule-delete-expired-files-1i41)
- [Amazon ECR - Lifecycle Policy Rules](https://dev.to/vumdao/amazon-ecr-lifecycle-policy-rules-1l59)
- [Connect Postgres Database Using Lambda Function](https://dev.to/vumdao/connect-postgres-database-using-lambda-function-1mca)
- [Using SourceIp in ALB Listener Rule](https://dev.to/vumdao/using-sourceip-in-alb-listener-rule-377b)
- [Amazon Simple Systems Manager (SSM)](https://dev.to/vumdao/amazon-simple-systems-manager-ssm-2pb0)
- [Invalidation AWS CDN Using Boto3](https://dev.to/vumdao/invalidation-aws-cdn-using-boto3-2k9g)
- [Create AWS Lambda Function Triggered By S3 Notification Event](https://dev.to/vumdao/create-aws-lambda-function-triggered-by-s3-notification-event-9p0)
- [CI/CD Of Invalidation AWS CDN Using Gitlab Pipeline](https://dev.to/vumdao/ci-cd-of-invalidation-aws-cdn-using-gitlab-pipeline-34op)
- [Create CodeDeploy](https://dev.to/vumdao/create-codedeploy-4425)
- [Gitlab Pipeline With AWS Codedeploy](https://dev.to/vumdao/gitlab-pipeline-with-aws-codedeploy-30cl)
- [Create AWS-CDK image container](https://dev.to/vumdao/create-aws-cdk-image-container-43ei)

<h3 align="center">
  <a href="https://dev.to/vumdao">:stars: Blog</a>
  <span> · </span>
  <a href="https://vumdao.hashnode.dev/">Web</a>
  <span> · </span>
  <a href="https://www.linkedin.com/in/vu-dao-9280ab43/">Linkedin</a>
  <span> · </span>
  <a href="https://www.linkedin.com/groups/12488649/">Group</a>
  <span> · </span>
  <a href="https://www.facebook.com/CloudOpz-104917804863956">Page</a>
  <span> · </span>
  <a href="https://twitter.com/VuDao81124667">Twitter :stars:</a>
</h3>
