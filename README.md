#Overview

Running a the [Gogs](http://gogs.io/) Git service as a container within OSE v3 (Beta4), with a persistant volume to survive restarts.

Currently it is configured to run in the infra labeled nodes (i.e. the master).

##OSE Environment Config

This assumes you have followed the [training](https://github.com/openshift/training/blob/master/beta-4-setup.md#using-persistent-storage-optional) in Using persistent storage. So from that the following would be configured :

	nfs-utils installed
	Firewall Configured
	SELinux Configured

1. Create another export

        mkdir -p /var/export/gogs
        chown nfsnobody:nfsnobody /var/export/gogs
        chmod 700 /var/export/gogs

2. Edit `/etc/exports` and add the following line:

        /var/export/gogs *(rw,sync,all_squash)

3. Export

        exportfs -a

Running `exportfs` you should see something similar to:

	/var/export/gogs
		<world>
	/var/export/vol1
		<world>

##Installation

This assumes you are creating this within the default namespace, i.e. running as the admin user. If you wish to create a seperate project to run this in then step one (Creating the PersistantVolume) still needs to be ran as the admininistrator.

1. Create the PersistantVolume:

        osc create -f persistent-volume.yaml

2. Create the PersistantVolumeClaim

        osc create -f persistent-volume-claim.yaml

3. Create the pods, service & route

        osc create -f gogs.yaml


##Running

OSE will do the running for you! First run you will have to config Gogs.

1. Head to http://gogs.cloudapps.example.com/ (That is the entry the route creates) which on first run will send you to the install page

2. Change the Path to `/data/gogs.db`

3. Change the Repository Root Path to `/data/gogs-repositories`

4. Change the Domain to `gogs.cloudapps.example.com`

5. Change the Application URL to `http://gogs.cloudapps.example.com/`

6. Configure a username/password for the admin user

7. Hit the Install Gogs button

You should see the files for the DB be created on the master under the /var/export/gogs directory



