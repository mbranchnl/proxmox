# Proxmox (homelab) state role

This ansible-role helps you deploying Proxmox proxmox_vms with ease. In my homelab I deploy or scale VM's within 1 minute.

## Requirements

The following modules are required;

Collections:

- community.general

Pip modules:

- ansible
- requests
- proxmoxer

## Features

- Configure proxmox servers
- Deploy VM's
- Scale deployed VM's

## Usage

### First steps

One of the first steps is to configure the vars defined in `defaults/main.yml`. There you define your default template to use etc. etc..

### Inventory

The inventory should define your Proxmox hosts in the `proxmox` group and your proxmox_vms in the `servers` group. For example:

```yaml
proxmox:
  hosts:
    node01:
      ansible_host: node01.local
    node02:
      ansible_host: node02.local

servers:
  hosts:
    rke2:
      vmid: 101
      node: node01  # Optional: specify which Proxmox node to use
```

### Deployment

When you want to deploy VMs, define them in the servers hostgroup. You can optionally specify which Proxmox node to deploy to using the `node` variable:

```yaml
servers:
  hosts:
    web01:
      vmid: 101
    db01:
      vmid: 102
    web02:
      vmid: 101
      node: node02
```

or when you work with children:

```yaml
servers:
  children:
    servers_nestorix:
    servers_obelix:

servers_obelix:
  hosts:
    test:
      vmid: 110
      node: obelix
      # cores: 4
      # memory: 8192

servers_nestorix:
  hosts:
    rke2:
      vmid: 200
      node: nestorix
```

#### VM speficic variables

You can override the proxmox_cores and proxmox_memory variables by adding the desired specs to the specific host. Same for a different datastore or template to use:

```yaml
servers:
  hosts:
    k3s01:
      vmid: 131
      node: node01          # Optional: specify which Proxmox node to use
      datastore: 'vmstore01'
      cores: 4
      memory: 4096
      template: 1011
    k3s02:
      vmid: 132
    k3s03:
      vmid: 133
```

## Node specific (default) variables

```yaml
proxmox_subnet: 10.0.0.
proxmox_gateway: 10.0.0.1
proxmox_api_host_ip: 10.0.0.10
proxmox_api_hostname: nodename
proxmox_api_user: root@pam
proxmox_api_password:
proxmox_default_storagepool:
```

vmid for default template:

```yaml
proxmox_default_template: 2009
```

Default VM specifications:

```yaml
proxmox_cores: 1
proxmox_memory: 2048 # in MB
```

Allow node configuration for scaling governor, packages and datastores:

```yaml
proxmox_allow_node_configuration: true
proxmox_scaling_governor: "powersave"
proxmox_datastore_default_fs: ext4
```

Datastore configuration:

Example of default datastore of an existing folder/mount:

```yaml
proxmox_datastores:
  - name: vmstore01
    path: /media/vmstore01
    content: backup images (defaults to images)
    retention: 'keep-all=1'
  - name: vmstore02
    path: /media/vmstore02
```

The retention variable is additional and can be used for configuring backup retention.

Also adding a new disk including mount configuration use the following configuration:

```yaml
proxmox_datastores:
  - name: vmstore01
    path: /media/vmstore01
    dev: /dev/pve/data
    retention: 'keep-all=1'
  - name: vmstore02
    path: /media/vmstore02
    dev: /dev/nvme0n1p1
```

## Known issues

### Pre-install

When having a Nvidia GPU you need to start the installation with nomodeset:

- select install method
- type 'e'
- add: nomodeset to linux-line

### Cloudinit

The templates you build must use cloudinit inside your proxmox installation and have qemu-qa installed.
To be able to set hostname etc. configure qemu-qa with;

```shell
sed -i "s/.*BLACKLIST_RPC.*/#BLACKLIST_RPC=guest-file-open,guest-file-close,guest-file-read,guest-file-write,guest-file-seek,guest-file-flush,guest-exec,guest-exec-status/g" /etc/sysconfig/qemu-ga
```

## License

gnu-gpl-v3.0
