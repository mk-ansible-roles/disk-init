disk-init
=========

This role adds complete disks in to LVM Groups. It is aimed to be idempotent and just adds new disks.
It currently does not remove disks from a diskgroup nor cleans up disks that do have a valid LVM signature on the disk (created by pvcreate)

Requirements
------------

It is tested on RHEL EL7, but should work on Centos 6,7, EL7 and Fedora.
The system must be properly subscribed to install the required packages for diskmanagement (gdisk, sg3_utils, lvm2)

Role Variables
--------------

To define the disk setup of s system the follwowing variables need to be set in your playbook:

    disks:
      <devicepath1> : <volumegroup>
      <devicepath2> : <volumegroup>
      ...

    logvols:
       <logical_volume_name1>:
             size: <size>
             vol: <volumegroup>
             mountpoint: <mountpoint>
             pvs: <devicepath> (optional, if you want to place vol explicitly)
             fstype: xfs (optional, defaults to xfs)
             opts: -b 4096 (optional paramters for mounting)
        <logical_volume_name2>:
             size: <size>
             vol: <volumegroup>
             mountpoint: <mountpoint>
             pvs: <devicepath> (optional, if you want to place vol explicitly)
             fstype: xfs (optional, defaults to xfs)
             opts: -b 4096 (optional paramters for mounting)
        ....


Example Playbook
----------------

Here is an example playbook that adds two disks in volumegroup vg00 and adds another one to the existing root volumegroup

    - hosts: servers
      remote_user: root

      vars:
          disks:
                  /dev/vdc: vg00
                  /dev/vdb: vg00
                  /dev/vdd: root_vg

          logvols:
                  hana_shared:
                          size: 24G
                          vol: vg00
                          mountpoint: /hana/shared
                  hana_data:
                          size: 24G
                          vol: vg00
                          mountpoint: /hana/data
                  hana_logs:
                          size: 12G
                          vol: vg00
                          mountpoint: /hana/logs
                          pvs: /dev/vdc
                  usr_sap:
                          size: 49G
                          vol: vg00
                          mountpoint: /usr/sap
                          fstyp: xfs
                          opts: -b4096
                  install:
                          size: 100G
                          vol: root_vg
                          mountpoint: /install
                          fstyp: ext4

      roles:
         - { role: mk-ansible-roles.disk-init }

License
-------

Apache License
Version 2.0, January 2004

Author Information
------------------

Markus Koch

Please leave comments in the github repo issue list
