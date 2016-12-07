# Mindflash Mailcatcher Demo

> Requires two CentOS 7 VM's for happy path, but can be done in a single VM

## By hand

1. SSH into the EC2 instance
2. Update yum packages
`sudo yum update`
2. Install Ruby
 * `sudo yum install ruby`
3. Install Mailcatcher 
 * `gem install mailcatcher`
4. Install Ruby Dependencies

```
sudo yum install -y git-core zlib zlib-devel gcc-c++ patch readline readline-devel libyaml-devel libffi-devel openssl-devel make bzip2 autoconf automake libtool bison curl sqlite-devel
```
5. Install Ruby Development Kit
 * `sudo yum install ruby-rdoc ruby-devel`
5. Install Mailcatcher, now that dependencies are set up

 * `gem install mailcatcher`
 * `mailcatcher`
6. Test that it's up
 * `curl localhost:1080`
7. Time to try it out in the browser
8. Try it again, kill the process after finding the PID 
9. It's not there, adjust security groups to open ports 1080 and 1025
10. Change the ip address to 0.0.0.0 with the mailcatcher CLI
 * `mailcatcher --ip 0.0.0.0`
11. Send a test email with telnet
 * `telnet <PublicIP> 1025`
 
	```
	mail from: ljoslin@holidays.com
	rcpt to: santa@naughtylist.com
	 
	data
	
	Subject: Am I a naughty or nice engineer?
	
	Dear Santa,
	I know you are kind wise man but I confess that I have done some naughty things this year. My naughty deeds include failing to share enough cappuccinos,returning my library books overdue, and neglecting to practice piano as frequently as I should. However, my greatest naughty deed of all was setting up this mailserver by hand.
	I don't want coal in my stocking this year or start 2017 with paying back technical debt as my first task at Mindflash. Is there a way I can amend this and get on the nice list?
	Apologies,
	Lydia
	 
	.
	``` 
	 
	 
	```
	mail from: santa@naughtylist.com
	rcpt to: ljoslin@holidays.com
	 
	data
	 
	Dear Lydia,
	 
	Don't worry about the piano, coffee, and library books. I know you tried your best. You have done many good deeds this year so those things are trivial.
	
	Bad engineering practices will get you stuck on the naughty list and this is a serious issue. If you keep setting up things by hand, I'm afraid you are stuck on the naughty list.  
	Fortunately, I have a long and successful track record of delivering presents in multiple environments. How about you try to ship the mailcatcher server in a container? Packaging the app correctly will move you to the nice list, and Tech Santa will give you some upgrades in your stocking for 2016.
	
	Holiday Cheers,
	
	Santa
	.
	
	``` 


## Packaged Nicely
1. Let's try it with docker! 
2. SSH into the EC2 instance
3. Set yourself as root
 * `sudo su`
4. Update Yum Packages
 * `yum update -y`
5. Add the docker repo

	```
	tee /etc/yum.repos.d/docker.repo <<-'EOF'
	[dockerrepo]
	name=Docker Repository
	baseurl=https://yum.dockerproject.org/repo/main/centos/7/
	enabled=1
	gpgcheck=1
	gpgkey=https://yum.dockerproject.org/gpg
	EOF
	```

6. Install the docker engine package

 * `yum install -y docker-engine`

7. Enable docker to start as a service 
 * `systemctl enable docker.service`

8. Start docker as a service
 * `systemctl start docker`

9. Check with `docker ps -a`
 
10. Go to dockerhub, select the right build -schickling/mailcatcher 
 * https://hub.docker.com/r/schickling/mailcatcher/

11. Start up the mailcatcher server
 *`docker run -d -p 1080:80 -p 1025:25 --name mailcatcher1 helder/mailcatcher`
 
12. See it in the browswer with IP:1080, send emails with IP:1025 (assume security groups are correct)

```
mail from: santa@nicelist.com
rcpt to: ljoslin@holidays.com
	 
data
	 
Lydia,x

I see you have set up my mailbox for the people on the nice list with a docker container. 

This is exactly what I wanted - Send me a list with your wares (hard and soft) of choice and I will see what I can do!

Cheers,

Santa

```

13. Send emails with telnet for show

---
> Reference: [Ruby Installation](http://etutorialsworld.com/2016/03/install-ruby-ruby-gems-and-rails-on-centos-7/)

> Reference: [Docker Install on Centos 7](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-centos-7)
