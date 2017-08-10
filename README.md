# Pulp + Vagrant + Ansible

[![demo](https://asciinema.org/a/aopQgNcC6hNoxi2gzixRKabJ3.png)](https://asciinema.org/a/aopQgNcC6hNoxi2gzixRKabJ3?autoplay=1)

## Instructions

Download files:

	$ git clone https://github.com/rodrigobrim/pulp-vagrant.git

Change to downloaded directory to call vagrant commands:

	$ cd pulp-vagrant

	$ vagrant up

That's all to provision the Pulp VM. Now we can do all pulp interactions. To do so, connect to the VM:

	$ vagrant ssh

Log-in on Pulp:

	$ pulp-admin login -u admin -p admin

Create a repo (in this example, we'll create and sync a Docker CE repo):

	$ pulp-admin rpm repo create --repo-id=docker-ce-stable --description 'Docker CE Stable - x86_64' --display-name 'Docker CE Stable - x86_64' --feed=https://download.docker.com/linux/centos/7/x86_64/stable

Sync repo now:

	$ pulp-admin rpm repo sync run --repo-id docker-ce-stable

Schedule sync all days, at 4h45 AM:

	$ pulp-admin rpm repo sync schedules create --schedule "2017-08-01T04:45Z/P1D" --repo-id docker-ce-stable

On the consummer (yum clients), create the repofile:

	$ sudo vi /etc/yum.repos.d/docker-ce.repo

With:

	[docker-example-repo]
	name=Docker Internal Pulp repository for EL $releasever - $basearch
	baseurl=https://pulp.dev/pulp/repos/linux/centos/$releasever/$basearch/stable
	enabled=1
	gpgcheck=0
	sslverify=0
	proxy=_none_

Check the result:

	$ yum search docker-ce
	Loaded plugins: fastestmirror, pulp-profile-update
	docker-example-repo                                                                                           | 2.1 kB  00:00:00     
	(1/3): docker-example-repo/7/x86_64/group                                                                     |  124 B  00:00:00     
	(2/3): docker-example-repo/7/x86_64/primary                                                                   | 2.4 kB  00:00:00     
	(3/3): docker-example-repo/7/x86_64/updateinfo                                                                |   92 B  00:00:01     
	Loading mirror speeds from cached hostfile
	 * base: centos.brisanet.com.br
	 * epel: mirror.cedia.org.ec
	 * extras: centos.brisanet.com.br
	 * updates: centos.brisanet.com.br
	docker-example-repo                                                                                                              7/7
	====================================================== N/S matched: docker-ce =======================================================
	docker-ce.x86_64 : The open-source application container engine
	docker-ce-selinux.noarch : SELinux Policies for the open-source application container engine

# Proxy

Change the proxy settings on:

	playbook.yml
	yum_importer.json

All four proxy variables on yum_importer.json must be present. If you want to deploy without proxy, ajust the playbook.yml to don't copy the yum_importer.json.


## Tested on:

	Linux Mint 18 Sarah and Gubuntu 17.04

With:

	$ ansible --version
	ansible 2.3.2.0
	  config file = /etc/ansible/ansible.cfg
	  configured module search path = Default w/o overrides
	  python version = 2.7.12 (default, Nov 19 2016, 06:48:10) [GCC 5.4.0 20160609]

	$ vagrant version
	Installed Version: 1.9.7
	Latest Version: 1.9.7

## Author

Rodrigo Brim [blog](https://rodirgobrim.blogspot.com.br/)

## License

Copyright &copy; 2017-2017 Rodrigo Brim.

All code is licensed under the GPL, v3 or later. See LICENSE file for details.
