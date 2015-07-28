---
layout: post
title: "Using Knife to launch EC2 instances without a Chef Server"
date: 2012-03-16 16:30
comments: true
categories: 
 - boto
 - chef
 - deployment
 - development
 - ec2
 - knife
---

In this post we look at how we can make it easy to launch servers on Amazon's EC2.
We'll use Knife to launch our instances since it gives us a lot of other features
that we can make use of when managing our servers. But we'll do this without the
complication of setting up a Chef Server.

<!-- more -->

## Launching instances

The easiest way to launch a server on EC2 is almost certainly via the control panel.
From there it's straightforward to choose an instance size, select a region to
launch into, and so on. However, given that we are always striving to make as much
of our work as possible reproducible then we need a command-line way to launch servers.
The two main options are the Amazon CLI tools and Opscode's Knife which is part of the
Chef collection of resources, although for more advanced situations it's also
possible to roll your own with libraries like [Boto].

### The Amazon CLI tools

The [Amazon CLI tools] do everything that you need, but are pretty raw. In the
early days of Amazon's service there was little else, but now I'd recommend
using something like Knife.

### Knife

Knife is part of the [Chef suite of tools], and is used for carrying out general
tasks to do with cookbooks and servers. It supports a plug-in architecture and
one of the plug-ins that has been added provides a number of commands for EC2.

Knife and Chef are usually used in conjunction with a Chef server which keeps
track of the servers you've launched, their capabilities, the cookbooks and
recipes you have available, and which of them have been applied to which servers.
However, recently we're seeing an increasing number of people using Chef Solo to
get up and running quickly managing Chef cookbooks and recipes by referencing
individual servers directly.

The problem is that whilst we can use the Knife cookbook tools without a Chef
Server, we can't use the EC2 commands, so in the instructions that follow we
include a small patch to make this possible.

This small change is a big win, since it gives us a convenient tool for launching
servers and a powerful way to manage our local cookbooks -- all without needing
additional infrastructure. And of course it keeps open the possibility of moving
up to Chef Server in the future.

## Installing Knife

The quickest way to get up and running with Chef and Knife is by following steps
2 and 3 from Opscode's [Fast Start Guide]. And if you're using a Mac for
your development (and after all, who isn't thinking differently these days?) you
can get up and running even more quickly by simply installing XCode (to get some
Ruby dependencies) and then Chef. Here's how:

### Install XCode

If you have the *Mac OS X Install* DVD that came with your Mac to hand then open
the *Optional Installs* folder and then click on *XCode* to install it (you
can accept the default settings). If you've lost the disk or you just can't be
bothered to get out of your chair to find it, then get it from the App Store
(just be prepared for a long download).

### Install Chef

Once XCode is installed run the following command to install Chef which will also
install Knife. Note that we're also asking for the EC2 and GitHub plugins to be
installed as well:

``` bash
gem install \
  chef \
  net-ssh net-ssh-multi \
  fog highline \
  knife-ec2 knife-github-cookbooks
```

There may be a short delay, but after a bit you should get some messages indicating
what has been installed. Once complete, check that all is well by running `knife`:

``` bash
knife --version
```

You should get back something like this:

```
Chef: 0.10.8
```

Now that we have the tools we need to add a little bit of configuration information
and patch one of the EC2 sub-commands.

### Configure EC2 security

If you've already installed the Amazon CLI tools then you will have some of what
you need here, but if you haven't then you'll need to add your key IDs to your
environment and set a default region. The easiest way to do this is to tack the
values on to the end of your bash profile. You can either edit `~/.bash_profile`
(still assuming a Mac environment) directly or follow these steps:

* Add a few variables that will be used in the next command (without the squiggly
  brackets):

``` bash
AAKI={your access key ID}
ASAK={your secret access key}
AZ={your preferred availability zone, such as eu-west-1a}
REGION={your preferred region, such as eu-west-1}
```

You don't *have* to do this but it makes it just a little bit easier to run the
next command (otherwise you'll have to paste each line individually, modifying
the values as you go).

* Paste the following into a terminal window. It will use the variables we've
  just defined to update your profile:

``` bash
cat >> ~/.bash_profile << EOF
# Set up Amazon access:
#
export AWS_ACCESS_KEY_ID=${AAKI}
export AWS_SECRET_ACCESS_KEY=${ASAK}
export EC2_AVAILABILITY_ZONE=${AZ}
export EC2_REGION=${REGION}
EOF
```

* To get a shell with these new settings applied, either open a new terminal
  window, or reload the profile in the current window:

``` bash
source ~/.bash_profile
```

### Create a Knife configuration file

The next step is to add a Knife configuration file which will pick up these
default settings. You can paste the following commands into a terminal window:

``` bash
mkdir ~/.chef
cat > ~/.chef/knife.rb << EOF
# EC2 sub-command
#
knife[:availability_zone] = "#{ENV['EC2_AVAILABILITY_ZONE']}"
knife[:aws_access_key_id] = "#{ENV['AWS_ACCESS_KEY_ID']}"
knife[:aws_secret_access_key] = "#{ENV['AWS_SECRET_ACCESS_KEY']}"
knife[:chef_mode] = "solo"
knife[:region] = "#{ENV['EC2_REGION']}"
EOF
```

Note that we could have placed our keys and regions directly into this file but
there are a number of advantages to the way we're doing it here. The first is
that you don't need to worry about keeping you `knife.rb` file secret, which
means you could share it with colleagues if you want to get them set up quickly.
Second, the environment variables that we're picking up are also used by other
Amazon-related tools which means (a) if you are using these tools already you
will already have these values set and (b) if you ever change these values you'll
only ever need to do it in one place and all of your tools will benefit.

### Modify the Knife EC2 plugin

The final step is to make a very small change to the EC2 plugin which will make
it easier to use with Chef Solo. The [EC2 plugin for Knife is maintained by Opscode]
and is designed to be used with Chef Server. This means that once a new server
has been launched on Amazon, Knife will then attempt to register it with Chef
Server and bootstrap it with Chef. We could just ignore the error message that
we get at the end, but since it's pretty straightforward to patch and override
plugins, we'll do that.

Knife plugins have a nice hierarchical architecture, which means that we can
easily override one of the EC2 sub-commands without having to override all of
them. So first create a directory for your 'personal' plugins:

``` bash
mkdir -p ~/.chef/plugins/knife/
```

Now get a copy of the patched 'server create' plugin:

``` bash
curl -L https://raw.github.com/gist/2049991/170e0fd2a5b9c5b8532a385d990d04400b182fb4/ec2_server_create.rb \
  > ~/.chef/plugins/knife/ec2_server_create.rb
```

We should now be all set to launch a server.

## Launching a server

The command to launch a server in Knife is part of the EC2 plug-in, and looks
something like this:

``` bash
knife ec2 server create \
  --groups=default \
  --region=eu-west-1 --availability-zone=eu-west-1a \
  --image=ami-895069fd --flavor=m1.medium \
  --ssh-user=ubuntu \
  --ssh-key=keyname --identity-file=~/.ssh/keyname.pem
```

The `--groups` parameter indicates which security group you want to use. (A
security group is basically a set of firewall settings.) If you want to provide
a list of groups then just use a comma:

``` bash
--groups=http,solr,ssh
```

The `--region` and `--availability-zone` parameters determine where your server
will be located and can be omitted if you set them in your environment earlier.

The type of server you want to launch is determined by the `--image` and `--flavor`
parameters. There are 32- and 64-bit AMIs, varying by operating system, and in
this example I'm using a 64-bit version of Ubuntu 11.10 (`ami-895069fd`). Each
permutation is repeated in each region so you'll need to make sure that you get
the right AMI for the region you want to launch the server in. I usually use AMIs
from the [Ubuntu Cloud Images] list.

The `--ssh-user` parameter determines the user name that will be used to access
the server, and is set in the AMI; if you use AMIs from other sources then you'll
need to know the name that was used when creating the image.

Finally, the `--ssh-key` parameter refers to the name of a key pair that you will
have previously created in Amazon, perhaps via the management console. The
`--identity-file` parameter refers to the private part of that key pair.

In addition to the keys and region information we can also add `image` and `flavor`
to `knife.rb`. For example:

``` bash
cat > ~/.chef/knife.rb << EOF
# EC2 sub-command
#
knife[:availability_zone] = "#{ENV['EC2_AVAILABILITY_ZONE']}"
knife[:aws_access_key_id] = "#{ENV['AWS_ACCESS_KEY_ID']}"
knife[:aws_secret_access_key] = "#{ENV['AWS_SECRET_ACCESS_KEY']}"
knife[:image] = "ami-895069fd"
knife[:flavor] = "m1.medium"
knife[:chef_mode] = "solo"
knife[:region] = "#{ENV['EC2_REGION']}"
EOF
```

This would allow you to launch a new server like this:

``` bash
knife ec2 server create \
  --groups=default \
  --ssh-user=ubuntu \
  --ssh-key=keyname --identity-file=~/.ssh/keyname.pem
```

Whilst the server is launching you'll see a lot of information flying by about
what is going on, including the fact that Knife has bootstrapped Chef for us.
At the end we get a list of details such as the instance ID and the public and
private DNS names and IP addresses, but note that since we're using Chef Solo,
Knife has not bootstrapped Chef in the way that it normally would -- but we'll
come to that in a follow-up post on using Chef with our instances.

## Managing instances

You can list and terminate instances with Knife, as well as issue commands to one
or more instances.

### Listing running instances

To see a list of running instances for a particular region use the `server list`
sub-command:

``` bash
knife ec2 server list --region=eu-west-1
```

You'll get a list of the instances with their public and private IP addresses,
instance IDs, AMI used, and so on.

### Accessing a server

We can connect to the new server using SSH with the public IP address or DNS name
that was given to us within the output of the Knife `server create` or `server
list` commands. The usual `ssh` command can be used:

``` bash
ssh -i ~/.ssh/keyname.pem ubuntu@176.34.221.207
```

Note that the RSA key and user name must be the same as those used to launch the
server with Knife.

### Terminating a running instance

You can shutdown a server with its instance ID which you can get from the `server
list` command. For example:

``` bash
knife ec2 server delete i-a4ddeced --region=eu-west-1
```

You'll be asked to confirm that you really want to shutdown the server, although
you can add a `-y` option if you're feeling confident. If you run the list
command again you'll see that the `State` column says `shutting-down`.

## Conclusion

That's it -- you now have a way to quickly launch and terminate servers in the
cloud which is a bit easier than the usual Amazon command-line tools. What's more,
the tool we use to start and stop instances gets us onto the Chef ladder for
easy deployment and configuration. We'll look at how we can use Chef on our
instances in a future post.

  [Amazon CLI tools]: http://aws.amazon.com/developertools/351
  [Boto]: http://boto.readthedocs.org/en/latest/index.html
  [Chef suite of tools]: http://wiki.opscode.com/display/chef/Home
  [EC2 plugin for Knife is maintained by Opscode]: https://github.com/opscode/knife-ec2
  [Fast Start Guide]: http://wiki.opscode.com/display/chef/Fast+Start+Guide
  [Ubuntu Cloud Images]: http://uec-images.ubuntu.com/releases/
