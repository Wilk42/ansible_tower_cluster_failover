
Tower Cluster Disaster Recovery.
=========
This role is built to switch over from a Primary Cluster and a Secondary cluster. It depends on the databases being replicated without this role. The tower nodes in the primary cluster and secondary should be set to down, and this role run. The database in the secondary cluster promoted, and then the secondary nodes being set to up, and the correct provision and deprovision groups set, and then running this role.  

Thes role depends on the roles included with the Ansible Tower installer.

Examples of steps.
Turning all nodes off.
Do this before doing any failover process
1. Set tower_status in both groups to "down"
2. Run the role

Failing over from one cluster to another
1. On the database set, promote the database for chosen cluster.
2. Set tower_status on the chosen cluster to up
3. Set tower_status on the disabled cluster to down.
4. Set the deprovision_nodes to the group of the disabled cluster
5. Set the provision_nodes to the group of the choosen cluster
6. Run the playbook.


Requirements
------------

Ansible Tower installer roles in your `roles_path` as well as the Ansible Tower inventory file.

Add the slave database node to the Ansible Tower inventory file and define `postgresrep_role` for each database host.

```
[tower_primary]
10.12.32.103

[tower_dr]
10.12.32.104

[tower_primary:vars]
tower_status=up
broken_tower=no

[tower_dr:vars]
tower_status=down

[towers:children]
tower_primary
tower_dr

[all:vars]
#Set provisioning status, the ones to provision are the ones you want up.
deprovision_nodes=tower_dr
provision_nodes=tower_primary
#set to skip if your primary tower node is dead and unreadable.

...

```



Role Variables
--------------

| Name              | Default Value       | Description          |
|-------------------|---------------------|----------------------|
| `tower_status` | `skip` | `up` or `down`, which determines which tasks run on the host |
| `broken_tower` | `no` | 'skip' or 'Anything*' Set this to skip if the primary towers do not exist, This is for the version checking and you are turning on the backup nodes.  |
| `deprovision_nodes` | `tower_dr` | Password for replication account |
| `provision_nodes` | `tower_primary`  | WAL level |


Dependencies
------------
- firewall

Example Playbook
----------------

Install this role alongside the roles used by the Anisble Tower installer (bundled or standalone). Then run the example playbook.

---
- hosts: towers
  remote_user: root
  gather_facts: False
  roles:
    - tower-dr_role
...


License
-------

MIT
