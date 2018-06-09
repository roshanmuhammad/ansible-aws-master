# Creating AWS Instances with Ansible

[Ansible](http://www.ansible.com) playbook that creates an AWS instance, executes something inside, deletes the instance.

Tested for Ubuntu based instances.

### Reasons for writing this example

I wanted to test the entire process of creating, configuring and
deleting AWS instances because we're evaluating the opportunity of
moving a certain number of development machines (Linux used for embedded
systems software development) in the Cloud in order to gain speed during
compilation and debug and flexibility in terms of working seats.

I ended up merging several other examples, writing a playbook that
enables to deploy an unlimited number of AWS instances and play with
them for a while, deleting them at the end in the same play or
deciding later if and when to delete all or part of them.

Very useful for testing performances of different instances sizes,
speed of creation, investigate configurations.


### Usage

This [Ansible](http://www.ansible.com) playbook when run with all tasks
in a sequence will:

* create one or more AWS instances and wait for them to be ready (SSH available)
* upload a public SSH key as specified so that user "ubuntu" can log without password
* send back an email with the details of the created instances
* save in a local file the details of the created instances
* executes something in the instances (to be defined)
* delete the same instances sending back an email with the details


```
# install prerequisites
# in Ubuntu/Debian
#
user@host:~$ sudo apt-get install python-pip
user@host:~$ sudo apt-get install python-passlib
user@host:~$ sudo pip install ansible
user@host:~$ sudo pip install --upgrade ansible

# clone this script and configure for your target hosts
# hint: the configuration file enables to specify
# multiple instances, with different settings
#
user@host:~$ git clone <this repo>
user@host:~$ cd ansible-aws
user@host:~/ansible-aws$ cp group_vars/all.sample group_vars/all
user@host:~/ansible-aws$ nano group_vars/all

# run the full Ansible playbook
#
user@host:~/ansible-aws$ ansible-playbook -i hosts bootstrap.yml
```

Please note inside the configuration file ```group_vars/all```:

  - sections starting with ```- id:``` define one or more instances
    with a defined set of characteristics
  - lines similar to ```count: 1``` define the number of such
    identical instances to be created

A local file named ```/tmp/instances.yaml``` will be created at each
successful run with a description of the instances (yaml) in a format
useful as inventory file.

You can run the Ansible playbook limiting to certain operations (roles)
only. Available tags are ```create```, ```delete```, ```config```:

```
# run Ansible playbook with tags: create only
user@host:~/ansible-aws$ ansible-playbook -i /tmp/instances.yaml --tags "create" bootstrap.yml
```

You can always pass variables as arguments to Ansible.
So for example if you want to run the ```delete``` role only and you
need to specify the instance to be deleted you can use the generated
local file ```/tmp/instances.yaml``` (edited in case you need to operate
on certain instances only) and the run Ansible with:

```
# Prepare a description of instances to be deleted
user@host:~/ansible-aws$ cp /tmp/instances.yaml delete.yaml
user@host:~/ansible-aws$ nano delete.yaml

# run Ansible playbook with tags and variables
user@host:~/ansible-aws$ ansible-playbook -i delete.yaml --tags "delete" bootstrap.yml
```

### Testing

A bash script is included that will run all possible commands listed
above, with the current configuration. Errors are reported.

### Known issues


### References

* [Ansible](http://www.ansible.com/)

### Contact

* Author: [BlueWind] Stefano Costa (http://www.bluewind.it)
* Email: stefano DOT costa AT bluewind DOT it

