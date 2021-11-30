Role Name
=========
nar_solidfire_sds_install


Description
===========
This role automates the deployment of the SolidFire Enterprise SDS (eSDS) software on all hosts specified in the inventory file. After this role successfully completes, run nar_solidfire_cluster_config role to create a storage cluster using the newly installed eSDS nodes.


Requirements
============
* This role requires the target hosts to be running minimum RHEL 7.6 or other supported versions from the Interoperability Matrix (https://mysupport.netapp.com/matrix/imt.jsp?components=97283;&solution=1757&isHWU#playground).

* This role performs some operations directly on each target host. So, the controller system where Ansible is run needs to have network connections to all hosts. For more details on prerequisites for networking configuration, see the eSDS product documentation (https://docs.netapp.com/us-en/element-software/esds/concept_esds_prerequisite_tasks.html).

* The SolidFire eSDS RPM file should be downloaded from NetApp Support Site (https://mysupport.netapp.com/site/products/all/details/solidfire-enterprise-sds/downloads-tab), and either be published to an http/ftp file server accessible by the target hosts or be available locally on the controller.

* The inventory file for this role needs network addresses or FQDN information for all hosts, device names, the path to the target Element solidfire-element RPM package, and other required variables.

* This role requires Python version 3.6 or later and Ansible version 2.10 or later installed on the controller.

* This role requires root or superuser privilege to run.


Role Variables
==============

* cache_devices:
  There is at least one "high speed" device utilized for caching functionality in eSDS. Raw cache device name (/dev/device-name) can be specified for the device that is intended to be used as cache device. The raw device name (nvme) will be automatically updated with by-id/by-uuid in sf_sds_config file when the install role is run.

* storage_devices:
  This must be a list of full paths (/dev/device-name, /dev/disk/by-id/<device-id> or /dev/disk/by-uuid/<device-uuid>) for devices that will be used for data storage by eSDS.  The product requires at least two of these drives.

| Variable              | Required | Default | Description                   | Comments                  |
|-----------------------|----------|---------|-------------------------------|---------------------------|
| solidfire_element_rpm | yes*     | N/A     | URL or local path for RPM     | See the example below [1] |
| mgmt_iface            | yes      | N/A     | Valid NIC iface name          | Redundant NIC (team/bond) |
| storage_iface         | yes      | N/A     | Valid NIC iface name          | Redundant NIC (team/bond) |
| storage_devices       | yes      | N/A     | List of storage devices       | See example in note [2]   |
| cache_devices         | yes      | N/A     | List of cache devices         | See example in note [3]   |
| na_sf_validate_certs  | no       | False   | Validate SSL/TLS certificates |                           |
| na_sf_use_proxy       | no       | False   | Use proxy config on hosts     |                           |


Notes:
------
[1]:
```
Example of URL: `http://<server><:port>/<path>/solidfire-element-W.X.Y.Z-N.el{7,8}.x86_64.rpm`
Example of local path: `/tmp/solidfire-element-W.X.Y.Z-N.el{7,8}.x86_64.rpm`
```
[2]:
```
Examples of storage_devices:
/dev/nvme0n1
/dev/disk/by-uuid/nvme-ZZZZZZZ-YYYY_XXXXXXXX
/dev/disk/by-id/nvme-ZZZZZZZ-YYYY_XXXXXXXX
```
[3]:
```
Examples of cache_devices:
/dev/nvme1n1
/dev/disk/by-uuid/nvme-ZZZZZZZ-YYYY_XXXXXXXX
/dev/disk/by-id/nvme-ZZZZZZZ-YYYY_XXXXXXXX
```

Example Playbook
----------------
```
 - name: Install SolidFire Enterprise SDS
   remote_user: <root or superuser>
   gather_facts: True
   hosts: all
   roles:
     - role: nar_solidfire_sds_install
```

Example Inventory
-----------------
```
all:
  hosts:
    DL360:
      ansible_host: <DL360 IP>
      storage_devices:
        - "/dev/nvme0n1"
        - "/dev/nvme1n1"
        - "/dev/nvme2n1"
        - "/dev/nvme3n1"
        - "/dev/nvme4n1"
        - "/dev/nvme5n1"
        - "/dev/nvme7n1"
        - "/dev/nvme8n1"
        - "/dev/nvme9n1"
      cache_devices:
        - "/dev/nvme6n1"
    DL380:
      ansible_host: <DL380 IP>
      storage_devices:
        - "/dev/nvme0n1"
        - "/dev/nvme1n1"
        - "/dev/nvme2n1"
        - "/dev/nvme3n1"
        - "/dev/nvme4n1"
        - "/dev/nvme5n1"
        - "/dev/nvme7n1"
        - "/dev/nvme8n1"
        - "/dev/nvme9n1"
        - "/dev/nvme10n1"
        - "/dev/nvme11n1"
        - "/dev/nvme12n1"
        - "/dev/nvme13n1"
        - "/dev/nvme14n1"
      cache_devices:
        - "/dev/nvme6n1"
    R640:
      ansible_host: <R640 IP>
      storage_devices:
        - "/dev/nvme0n1"
        - "/dev/nvme1n1"
        - "/dev/nvme2n1"
        - "/dev/nvme3n1"
        - "/dev/nvme4n1"
        - "/dev/nvme6n1"
        - "/dev/nvme7n1"
        - "/dev/nvme8n1"
        - "/dev/nvme9n1"
      cache_devices:
        - "/dev/nvme5n1"
  vars:
    ansible_python_interpreter: <full path on target hosts to a python interpreter, such as /usr/libexec/platform-python>
    become: True
    ansible_password: <login user password> # prefer vault key
    ansible_become_pass: <privilege escalation password> # or --ask-become-pass on command line to prompt for input
    solidfire_element_rpm: http://<server>/<path>/solidfire-element-W.X.Y.Z-N.el{7,8}.x86_64.rpm
    mgmt_iface: mgmt_t0
    storage_iface: strg_t1
```

License
-------
GNU v3

Author Information
------------------
NetApp
https://www.netapp.com
