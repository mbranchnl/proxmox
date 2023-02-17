# Proxmox (homelab) state role

## Requirements

### Ansible modules

```yaml
---
collections:
  - community.general
```

### Pip modules

```yaml
ansible
requests
proxmoxer
```

## Example inventory

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

## Additional resources

```yaml
    k3s01:
      vmid: 101
      proxmox_memory: 4096
      proxmox_cores: 2
      datastore: vmstore / vmstore02
```

## Other template

```yaml
    centos7:
      vmid: 111
      template: 1011 (centos7)
      template: 1012 (centos8)
```

