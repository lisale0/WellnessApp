# Automatic Scaling and Deployment with AWS
This project contains functions for autoscaling and auto deployment through the AWS CLI
## Getting Started

## Requirements
### Ubuntu Machine
You can use last version of Ubuntu or any other OS 
(Note: unit test done on Ubuntu 14.04 ubuntu/images/hvm-ssd/ubuntu-trusty-14.04-amd64-server-20170727 (ami-841f46ff), available through AWS).

### Pip3
```
~$ sudo apt-get install python3-pip
```
### Fabric
```
sudo apt-get install fabric
```

### AWS CLI
```
~$ pip3 install awscli
```

#### Configure AWS
Configure your settings for the [aws cli](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html).  Note that we currently only have tests in the regions us-west-1 and us-east-1 because certain aws commands are available in these two regions
```
~$ aws configure
AWS Access Key ID: <add access key> 
AWS Secret Access Key: <add secret access key>
Default region name: us-east-1
Default output format [json]: json
```


## Files
There are Fabric files that condenses all aws calls for automatic scaling and deployment

|   **Name**    |  **Description** |
|---------------|----------------|
|**autoscale.py**|    This is a fabric file that contains all the methods for automatics scaling of EC2 instances      |
|**autodeploy.py**|    This is a fabric file that automatically deploys the application to a server      |
|**bash_scripts/config_haproxy.sh**|     Stand alone script for configuring haproxy    |
|**bash_scripts/create_instance.sh**|     Stand alone aws script for creating EC2 instances   |
|**bash_scripts/get_instanceIP.sh**|     Stand alone aws script for getting an EC2 instance's IP address  |
|**bash_scripts/get_metrics.sh**|     Stand alone aws script for getting an EC2 instance's metrics  |
|**bash_scripts/write_instances.sh**|    This script gets instanceid and instance public ip address and writes it to instance_id.txt and instance_ip.txt, respectively   |

## Creating New AWS Instances
A new aws instance is created, when the IP address is created it will automatically deploy the code.

### configure the scale()
Configure the scale method to include information pertaining to your aws account.
Example:
```
def scale():
    """ run methods here"""
    new_instance = create_instance("ami-7dce6507", "t2.micro", "sg-54345f20", "instancekey","subnet-af38da80", 1)
    new_instance_id = get_instance_id(new_instance)
    tag_instance(new_instance_id, "instance-group", "wellness")
    new_instance_info = get_instance_info(new_instance_id)
    new_instance_ip = get_instance_ip(new_instance_info)
    print(new_instance_ip)
    time.sleep(20)
    local("fab -i instancekey -f autodeploy.py deploy -u ubuntu -H {}".format(new_instance_ip))
    add_all_instances_to_target("arn:aws:elasticloadbalancing:us-east-1:38743897246:targetgroup/wellness-target-group/5433534ja70335", "instance-group", "wellness")
```

```
fab -f autoscale.py scale
```
## Deploying to AWS Instances
You can also manually set the host IP address to deploy the code
```
fab -i <private-key-for-instance> -f autodeploy.py deploy -u ubuntu -H <hosts>
```

## Resource(s)
* [Getting started with AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)
* [Working with AWS Services](http://docs.aws.amazon.com/cli/latest/userguide/chap-working-with-services.html)
* [Using Amazon EC2 instances](http://docs.aws.amazon.com/cli/latest/userguide/cli-ec2-launch.html)
* [Boto3](https://boto3.readthedocs.io/en/latest/reference/services/autoscaling.html)

