# Ansible Role: SSH Key

An [Ansible Galaxy](https://galaxy.ansible.com/) role for retrieving and installing an SSH private key from Hashicorp Vault onto an Ansible controller host, primarily for use in Docker containers as part of a CI/CD workflow.

## Requirements

* This role depends on the [community.hashi_vault](https://galaxy.ansible.com/community/hashi_vault?extIdCarryOver=true&sc_cid=701f2000001OH7YAAW) collection which must be installed before the role can be used
* It is expected that the private key is stored as a _single string_, and without headers, in a JSON document in Hashicorp Vault under the key `ssh_private_key`:

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

The private key is assumed to be an RSA key with PEM format by default, but this can be changed to support newer OpenSSH format keys:

```yaml
ansible_controller_ssh_key_type: openssh
```

To change the default key permissions (`0600`):

```yaml
ansible_controller_ssh_key_mode: <permissions>
```

When installing the key a suitable header and footer comment will be added using the specified key format (one of `openssh` or the default `rsa`) and the key will be split over 64 characters per line.

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
