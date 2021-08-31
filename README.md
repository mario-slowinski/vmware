vmware
======

Ansible role to manage [VMware](https://www.vmware.com/) objects.

Requirements
------------

* [ansible.builtin](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/index.html)
* [ansible.posix](https://docs.ansible.com/ansible/latest/collections/ansible/posix/index.html)
* [community.vmware](https://docs.ansible.com/ansible/latest/collections/community/vmware/index.html)

Role Variables
--------------

* defaults

  * `main.yaml`

    ```yaml
    vmware_host: ""             # vCenter or ESXi server
    vmware_user: ""             # vCenter or ESXi server user
    vmware_password: ""         # vCenter or ESXi server user's password
    vmware_validate_certs: bool # whether validate vCenter SSL certificate

    vmware_name: ""             # VMware guest name
    vmware_annotation: ""       # VMware guest annotation
    vmware_template: ""         # VMware template to clone to guest

    vmware_datacenter: ""       # name of VMware datacenter
    vmware_cluster: ""          # name of VMware cluster
    vmware_esxis: []            # list of ESXi standalone servers
    vmware_datastores: []       # list of datastores to be used in vmware_disks

    vmware_folders: []          # list of vCenter folders concatenated into path
    #  - name: "folder"
    #  - name: "subfolder"
    #    parent: "folder"
    vmware_folder: ""           # vCenter folder used for guest

    vmware_guest_id: ""         # VMware guest id i.e. rhel7_64Guest

    vmware_disks: []            # list of guest disks
    #  - datastore: ""          # disk datastore
    #    size_gb: int           # disk size

    vmware_hardware: []         # list of guest hardware
    #  num_cpus: int
    #  memory_mb: int
    #  scsi: ""

    vmware_networks: []         # list of guest networks
    #  - name: "VM Network"     # name of portgroup
    #    type: "static"         # static or dhcp
    #    ip: ""                 # ip address for OS customization
    #    netmask: ""            # netmask for OS customization
    #    gateway: ""            # gateway for OS customization
    #    start_connected: bool

    vmware_customization: []
    #  hostname: "{{ inventory_hostname_short }}"
    #  timezone: "Europe/Warsaw"
    #  dns_servers: []
    #  dns_suffix: []
    #  domain: example.com

    vmware_ip_address_wait: false
    vmware_ip_address_wait_timeout: int
    vmware_customization_wait: false
    vmware_customization_wait_timeout: int

    vmware_netplan_dir: ""

    vmware_principal: ""        # user to assign privileges
    vmware_guest_user: ""       # user used to copy files with vmtools
    vmware_guest_password: ""   # user's password used to copy files with vmtools

    vmware_guest_schema: ""     # schema used for vmware_guest_info
    vmware_guest_properties: ""

    vmware_storagepolicies:     # list of storage policies
      vm_home: ""
      disk: []
    ```

Dependencies
------------

*No* *dependencies*

Tags
----

* **vmware.folder** - Manage VMware vCenter folders
* **vmware.network** - Manage VMware network
  * **vmware.network.vswitch** - Create VMware vswitch
  * **vmware.network.portgroup** - Create VMware portgroups (with defined vlan_id) in vswitch
* **vmware.guest** - Manage VMware guest
  * **vmware.guest.vm** - Manage VMware guest vm
  * **vmware.guest.network** - Manage VMware guest network
* **vmware.custom** - Manage VMware guest custom attributes
* **vmware.storagepolicy** - Manage VM Storage Policy

Examples
--------

* `requirements.yaml`

  ```yaml
  - name: vmware
    src: https://github.com/mario-slowinski/vmware
  ```

* `playbook.yaml`

  ```yaml
  - hosts: servers
    gather_facts: true
    roles:
      - role: vmware
  ```

License
-------

[GPL-3.0](https://www.gnu.org/licenses/gpl-3.0.html)

Author Information
------------------

[mario.slowinski@gmail.com](mailto:mario.slowinski@gmail.com)