Instructions for Building an AMI with Ubuntu and Jenkins and Installed
======================================================================

These instructions assume familiarity with Git and GitHub.

## Setting up your local machine
* Install [Packer](https://www.packer.io/downloads.html)
* Put packer in the PATH
```
cp packer /usr/local/bin
```

* Clone the forked repo to your local machine using this command: `git clone https://github.com/jnanjali/Jenkins-PackerAMI.git`

## Part I: Building an AMAZON AMI with Packer
This part is responsible for building the Amazon AMI.
Also make sure your AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY are set

```
     {
        "type": "amazon-ebs",
        "access_key": "{{ user `AWS_ACCESS_KEY_ID` }}",
        "secret_key": "{{ user `AWS_SECRET_ACCESS_KEY` }}",
        "region": "us-east-1",
        "instance_type": "t2.micro",
        "ssh_username": "ubuntu",
        "ami_name": "control-{{ user `PACKER_BOX_NAME` }}-{{timestamp}}",
        "source_ami_filter": {
           "owners": ["099720109477"],
           "filters": {
              "virtualization-type": "hvm",
              "name": "ubuntu/images/*ubuntu-xenial-16.04-amd64-server-*",
              "root-device-type": "ebs"
            },

           "most_recent": true
        } 
     }
```

* Run `cd Jenkins-PackerAMI/`
* Run `cd packer-templates`
* Run `packer validate --only amazon-ebs control-server.json `
* Run `packer build -only=amazon-ebs control-server.json`. 

After this build succeeds, you will see an output like this:-

```
Build 'amazon-ebs' finished.

==> Builds finished. The artifacts of successful builds are:
--> amazon-ebs: AMIs were created:
us-east-1: ami-00e6259bc9e9f70c1

--> amazon-ebs: 'aws' provider box: ../output/aws/control-ubuntu-14.04.6-server-amd64.box
```

## Part II: Launching your custom packer built AMI

* Select your AMI in AWS Console
* Select Launch
* Verify its up and running
* Add incoming rule in security group to allow all traffic
* Find the public ip address of the EC2 Instance
* Verify Jenkins is running by going to the following url
```
http://ec2-52-206-42-176.compute-1.amazonaws.com:8080/jenkins/
```


### Troubleshooting

If you encounter errors with Ubuntu version numbers not being available or checksum errors on Ubuntu,it means that 
this repository has not yet been updated for the latest Ubuntu version. Feel free to mention this in the 
[forum](https://discussions.udacity.com/c/nd012-p1-intro-to-devops/nd012-the-devops-environment). Meanwhile, you can 
fix this error for yourself by editing the contents of the `application-server.json` and `control-server.json` template 
files inside the `packer-templates` folder.

* Find the newest version number and checksum from the [Ubuntu website for this release](http://releases.ubuntu.com/trusty/)
* Edit `PACKER_BOX_NAME` and `iso_checksum` in the template files to match that version number and checksum.
