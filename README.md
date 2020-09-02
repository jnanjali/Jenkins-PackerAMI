Instructions for Building an AMI with Ubuntu and Jenkins and Installed
======================================================================

These instructions assume familiarity with Git and GitHub. If you are not comfortable with those tools, please complete Udacity's [How to Use Git and GitHub](https://www.udacity.com/course/how-to-use-git-and-github--ud775) course before proceeding.

After installing the required tools, you will need to ensure that your computer can find the executables to run them. For this, you might need to modify the PATH environment variable. A good overview is at [superuser.com](https://superuser.com/questions/284342/what-are-path-and-other-environment-variables-and-how-can-i-set-or-use-them). You may need to search the web for instructions on how to set the PATH variable for your specific operating system and version.

## Setting up your local machine
* Install [Packer](https://www.packer.io/downloads.html)
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

```

* Run `cd Jenkins-PackerAMI/`
* Run `cd packer-templates`
* Run `packer build -only=amazon-ebs control-server.json`. 


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

If you encounter errors with Ubuntu version numbers not being available or checksum errors on Ubuntu,it means that this repository has not yet been updated for the latest Ubuntu version. Feel free to mention this in the [forum](https://discussions.udacity.com/c/nd012-p1-intro-to-devops/nd012-the-devops-environment). Meanwhile, you can fix this error for yourself by editing the contents of the `application-server.json` and `control-server.json` template files inside the `packer-templates` folder.

* Find the newest version number and checksum from the [Ubuntu website for this release](http://releases.ubuntu.com/trusty/)
* Edit `PACKER_BOX_NAME` and `iso_checksum` in the template files to match that version number and checksum.
