vmware
======

Ansible role to manage [VMware](https://www.vmware.com/) objects.

Description
-----------

* ### network ###

  `vmware_networks` is a list of interfaces to be set on VMware guest
  * if list element **contains** `vlan_id` attribute, named `vswitch` and `portgroup` will be created on `vmware_esxis` servers
  * if list element **does not contain** `vlan_id` attribute, `portgroup` should already exist and will be just assigned to VMware guest
  * if list elements **contains** `ip` attribute, will do network customization which requires VMware tools installed or task fails
  * if list elements **does not contain** `ip` attribute, *Manage VMware guest network* task will launch to assign `portgroup` or other interface properties

  [`vmware_guest`](https://docs.ansible.com/ansible/latest/collections/community/vmware/vmware_guest_module.html) ansible module performs network customization even if `networks` list contains only `portgroup` name so [`vmware_guest_network`](https://docs.ansible.com/ansible/latest/collections/community/vmware/vmware_guest_network_module.html) module must be used in second task even if only to change `portgroup`

* ### folders ###

  `vmware_folders` list contains elements which combines to vCenter VM and Templates folder path, i.e. below will create `/<vmware_datacenter>/vm/folder/subfolder/child`
  
  ```yaml
    vmware_folders:
      - name: "folder"
      - name: "subfolder"
        parent: "folder"
      - name: "child"
        parent: "folder/subfolder"
  ```

* ### guest ###

  Create `vmware_name` VMware guest or clone it from `vmware_template` and optionally reconfigure it with:

  * `vmware_guest_id` - one of [guest_id](https://code.vmware.com/apis/358/doc/vim.vm.GuestOsDescriptor.GuestOsIdentifier.html) list, not required for cloning,
  * `vmware_hardware` - vCPU and memory
  * `vmware_disks` - datastore and size
  * `vmware_annotation`
  * `vmware_customization`
  * `vmware_networks` - list of network interfaces with possible [customization](#network)

* ### guest_info ###

  Get and store in `vmware_guest` variable details about VMware guest or template.

* ### permissions ###

  Set `vmware_role` for `vmware_principal` user on `vmware_folder` (it's only supported type at the moment)

* ### storagepolicy ###

  Set storage `policy` on `vm_home` or per each `disk` selected by `unit_number`.

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
