# vmcloner

## Introduction

vmcloner is VMware vSphere CLI tool to clone one or more Linux VMs described by a VM specification file (YAML).
It is writen using an Official VMware python API [pyvmomi](https://github.com/vmware/pyvmomi). 

## Features

  * Clone multiple VMs from multiple templates
  * CPU and memory customization for new VMs
  * Add multiple NICs and assign each of them to different network/portgroup
  * Network settings (IP, NETMASK, GATEWAY)
  * Disk customization: multiple disks, each disk should go to which datastore etc.
  * Put a VM in the right cluster ,ESX host and VM folder.
  ...

_Disclaimer_: I do not provide any support or respond to any damage this software may cause.


## Version

0.1 

## Installation

You will need [pyvmomi](https://github.com/vmware/pyvmomi) installed.

### Using pip

    sudo pip install pyvmomi

### Git

Follow an instruction from [pyvmomi](https://github.com/vmware/pyvmomi)


## Configuration

### Configuration file

You must provide a YAML configuration file for setting up default VM parameters (datacenter, cluster, datastore ...)
There are two options:

1. Create a file .vmcloner.conf in your home directory
2. Create a separated config file and pass this file to vmcloner

Below is an example of vmcloner configuration file

    datacenter: Toronto
    cluster:
    resource_pool:
    num_cpu: 1
    memory_mb: 1024
    folder: Lab
    template: centos-6-x64-minimal

    hosts:
        - esx1.home.lab
        - esx2.home.lab

    disk:
        size_gb: 20
        type: thick
        datastore: datastore1

    dns_domain: home.lab
    dns_servers:
        - 8.8.8.8
        - 8.8.4.4

    netmask: 255.255.255.0
    default_gateway: 172.25.10.1

    nic:
        driver: vmxnet3
        network: VMware Network
        network_type: standard
        type: Ethernet
        bootproto: none
        onboot: yes
        ipv6: yes


### VM specification file

Below is an example of VM specification file

    ---
    - name: vm1
      num_cpu: 1
      memory_mb: 1024
      folder: 'Lab'
      template: centos-6-x64-minimal
      host: esx1.home.lab
      disks:
          - id: 1
            size_gb: 20
            type: thick
            datastore: esx1-datastore1

          - id: 2
            size_gb: 40
            type: thick
            datastore: datastore1

      networks:
          dns_domain: home.lab
          dns_servers:
              - 8.8.8.8
              - 8.8.4.4

          default_gateway: 172.25.10.1

          nics:
              - id: 1
                network: 'VMware Network'
                network_type: standard
                type: Ethernet
                bootproto: none
                onboot: yes
                ip: 172.25.10.171
                netmask: 255.255.255.0
                gateway: 172.25.10.1
                ipv6: yes

              - id: 2
                network: 'VM Network'
                network_type: standard
                ip: 192.168.2.101
                netmask: 255.255.255.0
                ipv6: no

    - name: vm2
      num_cpu: 2
      memory_mb: 2048
      template: centos-6-x64-minimal
      folder: OpenStack
      host: esx2.home.lab
      disks:
          - id: 1
            size_gb: 20
            type: thick
            datastore: esx2-datastore1

          - id: 2
            size_gb: 10
            type: thick
            datastore: datastore1

      networks:
          nics:
              - id: 1
                ip: 172.25.10.172
                netmask: 255.255.255.0
                gateway: 172.25.10.1
                network: 'VMware Network'

    - name: vm3
      num_cpu: 2
      memory_mb: 1048
      host: esx1.home.lab

      disks:
          - id: 1
            size_gb: 20
            datastore: esx1-datastore1

      networks:
          nics:
              - id: 1
                ip: 172.25.10.173
                gateway: 172.25.10.1

    - name: vm4
      host: esx2.home.lab
      disks:
          - id: 1
            size_gb: 20
            datastore: esx2-datastore1

      networks:
          nics:
              - id: 1
                ip: 172.25.10.174
                gateway: 172.25.10.1

    - name: vm5
      host: esx1.home.lab
      networks:
          nics:
              - id: 1
                ip: 172.25.10.175
                gateway: 172.25.10.1

## Examples

    vmcloner -h
    usage: vmcloner [-h] [-c CONFIG] [-u USERNAME] -s VCENTER -f SPECS

    Creating one or more VMware Linux VMs from one or more VM templates according to a specs file

    optional arguments:
      -h, --help            show this help message and exit
      -c CONFIG, --config CONFIG
                            Config file path
      -u USERNAME, --username USERNAME
                            Username to connect to a vCenter
      -s VCENTER, --vcenter VCENTER
                            vCenter to connect to
      -f SPECS, --specs SPECS
                            VM specs file


    vmcloner -c sample_config.conf -uroot -s vcsa.home.lab -f sample_vm_specs.yml                                                                                                          
    root's password:                                                                                                                                                                                                                             
    - Cloning from [centos-6-x64-minimal] to [vm1] ... Done
      * Configuring VM disk(s) for [vm1] ... Done                                                                                                                                                                                                               
    - Cloning from [centos-6-x64-minimal] to [vm2] ... Done
      * Configuring VM disk(s) for [vm2] ... Done

    - Cloning from [centos-6-x64-minimal] to [vm3] ... Done
      * Configuring VM disk(s) for [vm3] ... Done

    - Cloning from [centos-6-x64-minimal] to [vm4] ... Done
      * Configuring VM disk(s) for [vm4] ... Done

    - Cloning from [centos-6-x64-minimal] to [vm5] ... Done
      * Configuring VM disk(s) for [vm5] ... Done

## License

GPLv2

### Links

[pyvmomi](https://github.com/vmware/pyvmomi)

