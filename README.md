# AWSEndpointProxy

This project is a VPC private endpoint module that can provide proxification to every AWS services for instances that are on private subnets.
This product is auto-scalable and is based on a high availability architecture. It allows users to keep their main VPCs fully private, as well as the on-premise network that is linked by DX VIFs to those VPCs.
 
To build the project, 

 * Execute the following commands (to be replaced in cdk using DockerImageAsset class):
```
aws ecr create-repository --repository-name aws-proxy
$(aws ecr get-login --no-include-email)
docker build -t XXXXXXXXXXXX.dkr.ecr.us-west-2.amazonaws.com/aws-proxy:latest .
docker push XXXXXXXXXXXX.dkr.ecr.us-west-2.amazonaws.com/aws-proxy:latest
```
 * Then `aws cloudformation deploy --template-file template.yml --stack-name {STACK_NAME} --capabilities CAPABILITY_IAM`

Once the stack is deployed, run `aws ec2 describe-vpc-endpoint-services --query 'ServiceDetails[?Owner!=`amazon`]'` in order to get the endpoint Service name. From the AWS console, with this endpoint service name, you will be able to create a VPC endpoint to any VPC of your choice.

To test it with a private endpoint in a private VPC, connect to a private instance and do the following:
* `export http_proxy=vpce-xxxxxxxxxxx-xxxxxxxxxxxx.vpce-svc-xxxxxxxxxxxxxxx.eu-west-1.vpce.amazonaws.com:80`
* `export https_proxy= vpce-xxxxxxxxxxx-xxxxxxxxxxxx.vpce-svc-xxxxxxxxxxxxxxx.eu-west-1.vpce.amazonaws.com:443`
* `export NO_PROXY=169.254.169.254`  
And do something like `aws s3 ls s3://[name_of_one_of_your_bucket]` (considering the instanceRole of the instance has the right S3 policy of course)
