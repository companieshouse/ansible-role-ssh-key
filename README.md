# Ansible Role: SSH Key

An [Ansible Galaxy](https://galaxy.ansible.com/) role for retrieving and installing an SSH private key from Hashicorp Vault onto an Ansible controller host, primarily for use in Docker containers as part of a CI/CD workflow.

## Requirements

* This role depends on the [community.hashi_vault](https://galaxy.ansible.com/community/hashi_vault?extIdCarryOver=true&sc_cid=701f2000001OH7YAAW) collection which must be installed before the role can be used
* The private key should be stored as a base-64 encoded string to ensure that formatting and line breaks are preserved

```json
{
  "ssh_private_key" : "d93hGhsh3Zk39..."
}
```

## Role Variables

The role defaults (see `defaults/main.yml`) assume that the key is being installed for the `root` user with the key name `id_rsa`, and the directory `/home/root/.ssh` will be created with suitable permissions if it doesn't already exist.

To skip the creation of this directory:

```yaml
ansible_controller_ssh_dir_check: no
```

To specify a different user or different ownership or permissions for the `.ssh` directory use one or more of the following:

```yaml
ansible_controller_user: <user-name>
ansible_controller_ssh_dir_owner: <user-name>
ansible_controller_ssh_dir_group: <group-name>
ansible_controller_ssh_dir_mode: <permissions>
```

To specify a name other than the default `id_rsa` for the key being installed:

```yaml
ansible_controller_ssh_key_name: <key-name>
```

To change the default key permissions (`0600`):

```yaml
ansible_controller_ssh_key_mode: <permissions>
```

To remove a previously installed key:

```yaml
ansible_controller_ssh_key_install: no
```

## Example Requirements File

```yaml
---

roles:
  - src: https://github.com/companieshouse/ansible-role-ssh-key
    name: ssh-key
    version: "n.n.n"

collections:
  - name: community.hashi_vault
```

## Example Playbook

Specify `localhost` in your playbook to target the Ansible _controller_:

```yaml
---

- hosts: localhost
  roles:
    - ssh-key
  vars:
    hashicorp_vault_private_key_path: /applications/my-application-config
    ansible_controller_ssh_key_name: my-app-key
```

## License

MIT
