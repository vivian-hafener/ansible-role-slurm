ansible-role-slurm
------------------
[![Build Status](https://travis-ci.org/CSCfi/ansible-role-slurm.svg?branch=master)](https://travis-ci.org/CSCfi/ansible-role-slurm)


This is a fork of https://github.com/fgci-org/ansible-role-slurm. The primary changes herein is that I have a need to build Slurm myself instead of rely upon packaged versions. 

# Creates a SLURM cluster

Tested with these Linux distributions:
 - CentOS 7
 - Ubuntu
   - 18.04 (client only)

The role goes to some lengths to be backwards compatible.

For example in case we want to add default settings to slurm.conf in only some slurm versions we can do that by adding slurm_conf_version_specific_params_list to the version in vars/slurm\_version.yml

## Dependencies

 - https://github.com/jabl/ansible-role-pam
  - configure PAM - limit access to the compute nodes
 - https://github.com/CSCfi/ansible-role-nhc
  - Configure Node Health Checker

## How-To

### Initial playbook configuration

*Playbook variables:*

All variables should be defined in defaults/main.yml

All nodes run munge. Nodes which are part of the slurm\_compute host
group will additionally run slurmd. Nodes which are part of the
slurm\_service host group will additionally runs slurmctld and
slurmdbd (unless {{ slurm_accounting_storage_host }} is not the same as {{ slurm_service_node }}). Nodes which are in neither of these two host groups are 
assumed to be submit hosts.

You also need to add a mysql\_slurm_password: "PASSWORD" string
somewhere. This will be used to set a password for the slurm mysql
user. See http://docs.ansible.com/ansible/playbooks_vault.html

To add your own nodes and queues define the slurm_nodelist and slurm_partitionlist lists.

You don't have to setup a SQL server with this role, it's here for convenience and if you want to run the SQL on the same node as the slurmctld and slurmdbd.
It is possible to run the slurmdbd on a different host than the slurmctld by changing the slurm_accounting_storage_host variable.

It is also possible to setup a backup slurm controller by defining slurm_backup_controller variable. Please read the [SLURM HA documentation](https://slurm.schedmd.com/quickstart_admin.html#HA). For example you'll need a shared directory (for example NFS) available on both the slurm_service_node and slurm_backup_controller.

### Implementation

A playbook that uses this role: https://github.com/fgci-org/fgci-ansible

Or you can check out the [tests/test.yml](tests/test.yml) in this repo.

Example Playbook
----------------

<pre>
    - hosts: compute
      strategy: free
      roles:
         - { role: ansible-role-pam }
         - { role: ansible-role-nhc }
         - { role: ansible-role-slurm }

    - hosts: install
      roles:
         - { role: ansible-role-slurm }
</pre>

### Known Issues

 - Setting up a shared directory á la NFS for running a SLURM in HA is out of scope for this role. There are many [NFS server roles](https://github.com/CSCfi/ansible-role-nfs) and [Mount Filesystem roles](https://github.com/CSCfi/ansible-role-nfs_mount) roles out there.

### Testing and contributions

Testing is done with [Travis](.travis.yml).

 - PRs to master
 - if possible make sure that the new feature is also tested
 - strive for backwards compatibility

# Authors / Contributors:

 - Marco Passerini (original author)
 - https://github.com/martbhell
 - https://github.com/tiggi
 - https://github.com/A1ve5
 - https://github.com/jabl
 - https://github.com/mhakala
