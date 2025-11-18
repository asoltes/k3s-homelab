# K3S Ansible Setup

Install with ansible-galaxy
```bash
ansible-playbook k3s.orchestration.site -i inventory.yml
```

Upgrade with ansible-galaxy

```bash
ansible-playbook k3s.orchestration.upgrade -i inventory.yml
```