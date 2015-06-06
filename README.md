#Cloudbreak

##What is cloudbreak?
Cloudbreak is a **tool that helps you create hadoop clusters in cloud**. You might ask the same is done by apache ambari. Cloudbreak makes use of ambari internally. In addition, Cloudbreak takes care of the following:

1. Creating cloud(Amazon AWS, Azure, Google cloud,openstack) instances
2. Installing ambari server in a node(called gateway in cloudbreak terms) in the cloudbreak.
3. Installing ambari agent in all the nodes in the cluster
4. Submitting the blueprint provided by user to ambari. From here, ambari takes care of installing and monitor the required components specified in blueprint
5. There is one more component associated with cloudbreak deployer called as Periscope. It takes care of scaling clusters based on the metrics provided by ambari.
  
Let's start with the installation of cloudbreak deployer.

###Prerequisites

+ Docker version >= 1.5.0
+ curl

###Installation

Github repo for cloudbreak-deployer : https://github.com/sequenceiq/cloudbreak-deployer

Run the command,
	curl https://raw.githubusercontent.com/sequenceiq/cloudbreak-deployer/master/install | sh && cbd --version

###To check installation is successful
cbd --version

Running the above command will display the local version and latest release version

###Update cloudbreak deployer
cbd update

###Starting cloudbreak
- Create a new directory let's say "cloudbreak" in home directory and then cd to the directory.
- Run "cbd init". After init if cloudbreak deployer can't find the PUBLIC_IP of the machine, run the command, 
	+ echo "export PUBLIC_IP=<Public IP of the machine>" > Profile
- If deploying in AWS instance, AWS access and secret key pair is essential (used for assuming a AWS role which helps in launching, stop and terminate instance from cloudbreak). Add them to the Profile file as follows:
	+  echo "export AWS_ACCESS_KEY_ID=<YOUR ACCESS KEY>" >> Profile
  +  echo "export AWS_SECRET_KEY=<YOUR SECRET KEY>" >> Profile
- In order to avoid using the default username/password combination provided by cloudbreak specify your own username and password as follows:
  +  export UAA_DEFAULT_USER_EMAIL=myself@example.com
  +  export UAA_DEFAULT_USER_PW=demo123
- Then run, "cbd start". It should create docker-compose.yml and uaa.yml and also it downloads the necessary docker images which includes cloudbreak, periscope, UAA, consul, sultans and a few others as well.

###Verify cloudbreak services are running
Run, "cbd ps" to check if 11 of 12 services are up and running. All these are basically the docker containers running in the machine
Run, "cbd logs" to check the cloudbreak and other services' logs. 
To check logs from cloudbreak alone, run, cbd logs cloudbreak.

###Cloudbreak UI
Access the UI in browser with the public IP provided in the Profile file
- http://<$PUBLIC_IP>:3000

###Steps to create and run a cluster
In the cloudbreak UI login with credentials:
	-admin@example.com/cloudbreak

1. Create a blueprint
2. Add a credential(AWS)
	- Create a role in AWS as mentioned here(http://sequenceiq.com/cloudbreak/#accounts) with Account ID as the account ID of your AWS account where cloudbreak is deployed. The one given there is the hosted cloudbreak account id.
	- Then create a key pair as well in AWS
	- In cloudbreak UI specify the role ARN and the public key crated in the above step. This key is used while launching instances in cloud. You can use this private key to login to the cloud instance.
3. Create Template. Same as the machine types mentioned in AWS.
4. Select the credentials from the top of the page.
5. Create cluster using the blueprint added in step 1. It takes more than 15 minutes for the cluster to be up and running

The steps are mentioned clearly in the below youtube link:
https://youtu.be/E6bnEW76H_E

###Periscope

####Autoscaling
1. Time based - upscale or downscale based on time (http://blog.sequenceiq.com/blog/2014/11/25/periscope-scale-your-cluster-on-time/)
2. Metric based - alerts in ambari are used and then upscale or downscale based on the same (http://blog.sequenceiq.com/blog/2015/03/29/periscope-ambari-2-dot-0-scale-based-on-any-metric/)
