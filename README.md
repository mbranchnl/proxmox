# Proxmox (homelab) state role

This ansible-role helps you deploying Proxmox VMs with ease. In my homelab I deploy VM's within 1 minute.

## Requirements

The following modules are required;

Collections:

- community.general

Pip modules:

- ansible
- requests
- proxmoxer

## Known issues

### VM resource upgrade

Due to some minor issues upgrading of resources is currently disabled

### Power state

Currently all VM's need to be in a powered-on state

### Cloudinit

The templates you build must use cloudinit inside your proxmox installation and have qemu-qa installed.
To be able to set hostname etc. configure qemu-qa with;

```shell
sed -i "s/.*BLACKLIST_RPC.*/#BLACKLIST_RPC=guest-file-open,guest-file-close,guest-file-read,guest-file-write,guest-file-seek,guest-file-flush,guest-exec,guest-exec-status/g" /etc/sysconfig/qemu-ga
```

## Usage

### First steps

One of the first steps is to configure the vars defined in `defaults/main.yml`. There you define your default template to use etc. etc..

Due to some logic i use you need to defined your proxmox host with inventory_hostname `proxmox_api_host`

e.g.;
```yaml
all:
  hosts:
    proxmox_api_host:
      ansible_host: your_server
```

### Inventory

When you want to deploy a VM define the VM's in the servers hostgroup;

```yaml
servers:
  hosts:
    k3s01:
      vmid: 131
    k3s02:
      vmid: 132
    k3s03:
      vmid: 133
```

If you want to provide a specific vm with more resource just use host_vars to expand. 

```yaml
    k3s01:
      vmid: 101
      proxmox_memory: 4096
      proxmox_cores: 2
      datastore: vmstore / vmstore02
      template: 1011 (centos7)
```

## License

gnu-gpl-v3.0
