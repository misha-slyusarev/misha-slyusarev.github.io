---
layout: post
title:  "Install Ruby with Ansible"
date:   2017-03-20 08:00:00 +0100
categories: devops ansible
---

### Install and configure Ansible

I'm using OSX so `brew`is the best way to install Ansible. Please refer to [documentation](http://docs.ansible.com/ansible/intro_installation.html#installing-the-control-machine) to find out how to install it in your system.

You will need to have ssh access to a linux machine, so that Ansible can login to it and run instructions from a playbook. It doesn't need to have _a client_ installed on the machine, since it just uses bash to run the commands. In my case it will be an Ubuntu server with IP address 36.137.17.20.

{% highlight sh %}
$ brew install ansible
$ echo '36.137.17.20 ansible_user=ubuntu' >> /usr/local/etc/ansible/hosts
{% endhighlight %}

Let's check how installation works by updating the remote system:  

{% highlight sh %}
$ ansible all -a "apt-get update" --become
36.137.17.20 | SUCCESS | rc=0 >>
Hit:1 http://eu-central-1.ec2.archive.ubuntu.com/ubuntu xenial InRelease
Get:2 http://eu-central-1.ec2.archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
Get:3 http://eu-central-1.ec2.archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
Get:4 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
Fetched 306 kB in 0s (1216 kB/s)
Reading package lists...
{% endhighlight %}

### Playbook to install Ruby

Ansible uses Yaml to describe steps it is going to execute remotely. For this article I prepared a playbook [install-ruby.yml](https://gist.github.com/misha-slyusarev/4ea994a736329569e013a0c9cb12ee73). And below I will describe it in details.

First thing that we would like to do is to check if ruby already installed:

{% highlight yaml %}
---
- name: Install Ruby 2.3.3
  hosts: all
  remote_user: ubuntu
  become: yes
  tasks:
    - name: Check if Ruby is installed
      command: which ruby
      register: ruby_check
      ignore_errors: yes
{% endhighlight %}

So, we tell Ansible to login to all hosts we have, use remote user `ubuntu` and _become_ root via sudo. Then we register a new task that runs one single command `which ruby`. Result of this command will be either 0, if everything is ok, or an error code.

Next there is a block of steps that will download, unpack, and compile Ruby, and it makes sense to run this block only if Ruby is not installed (return code of `ruby_check` is not 0):

{% highlight yaml %}
- block:
  - name: Update the system
    apt: update_cache=true
...
  when: ruby_check.rc != 0
{% endhighlight %}

And that's pretty much it. Other parts of the main block are self descriptive and as I said they will just download, unpack, compile Ruby and install it in the system. Check the script to find out how these steps work.

{% highlight sh %}
$ ansible-playbook install-ruby.yml
{% endhighlight %}
