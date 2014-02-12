riakcs
========

This role will setup Riak CS on a single node. It will ***not*** build a Riak CS cluster.  In order to build and configure cluster, use one of the example playbooks defined below.

Requirements
------------

None

Role Variables
--------------

The following variables are available

Variables listed with "OS Specific" and "Install specific" have values defined in `vars/<ansible_os_family>.yml`.




| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| riak_pb_port       | 8087            | The port you will use to connect to the Riak instance |
| riak_backend   | bitcask       | default backend for Riak           |
| riakcs_anon_user_creation| 'false'     | allow Riak CS user to be created anonymously |
| riakcs_auth_module | riak_cs_s3_auth         | Riak CS default authentication module |
| riakcs_bind_ip  | 0.0.0.0           | The IP address used to listen for Riak CS requests.   |
| riakcs_creds_path | /tmp/riakcs_creds.yml       | The path where Riak CS credentials will be stored  |
| riakcs_fold_objects_for_list_keys     | 'true'          | defines list keys behavior |
| riakcs_iface   | eth0 | interface used to listen for Riak CS requests |
| riakcs_ip_addr| {{ hostvars[inventory_hostname]['ansible_' + riakcs_iface]['ipv4']['address'] }}"             |  shortcut for the ip address associated with riakcs_iface |
| riakcs_node_name |"riak-cs@{{ riakcs_ip_addr }}" | node name for riak-cs node |
| riakcs_package_release| 1       | the package release version |
| riakcs_port | 8080 | the name you give the riak node |
| riakcs_request_pool| 256        | number of connections pooled |
| riakcs_rewrite_module| riak_cs_s3_rewrite           | Riak CS re-write module |
| riakcs_root_host   | s3.amazonaws.com          | the hostname of the root host to use. |
| riakcs_version| 1.4.4 | Riak CS version. |
| stanchion_ip | "{{ hostvars[groups['stanchion'][0]]['ansible_eth0']['ipv4']['address'] }}" | IP address of the Stanchion server. |
| riakcs_usr_lib   |OS specific    | the path to Riak libraries.



Dependencies
------------
  
  
The following roles should be applied in order: 

basho.riakcs-packages (depends on basho.riak-common)
basho.riakcs

You'll also need to apply the stanchion role to a node to have a functioning cluster.

See examples for more details

Playbooks
-------

There are some sample playbooks in the [examples/](examples/) directory as well as a typical hosts file.

Take a look at [setup_riakcs.yml](examples/setup_riakcs.yml).


---
This playbook will

* Install Riak CS packages on a node
* Install Riak on the node and configure it for Riak CS (riakcs role depends on Riak role)
* Configure Riak CS on the node
* Configure Stanchion on a node
* Form a cluster



**Riak CS Credentials**

You'll of course need some Riak CS credentials to use Riak CS.  After the above playbook is run, run the the [reset_creds.yml](examples/reset_creds.yml) playbook.  You'll only need to do this on a fresh install or if you need to reset the admin credentials.

This will generate the admin user keys for the installation, and store them in /tmp/riakcs-creds.yml.  This can be overriden with the riakcs_creds_path variable.

I highly recommend you use the --tags creds option when resetting creds, this will force ansible to run only parts of the role relating to resetting credentials.  Example:

`ansible-playbook -v -i <path_to_your_inventory> reset_creds.yml  --tags creds`



**Running on a single node?**

If you're doing simple, single node testing (not production!)


	- hosts: riak_cluster
	  sudo: True
	  roles:
	  - { role: riakcs-packages, tags: ["riakcs", "packages"] }
	  - { role: riakcs, tags: ["riakcs"] }
	  - { role: stanchion, tags: ["stanchion"] }

And you'll still have to reset your credentials with the `reset_creds.yml` as per above.


License
-------

Apache

Author Information
------------------

jmartin@baso.com
