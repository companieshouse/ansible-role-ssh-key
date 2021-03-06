# Ansible Role: SSH Key

An [Ansible Galaxy](https://galaxy.ansible.com/) role for retrieving and installing an SSH private key from Hashicorp Vault onto an Ansible controller host, primarily for use in Docker containers as part of a CI/CD workflow.

## Requirements

* This role depends on the [community.hashi_vault](https://galaxy.ansible.com/community/hashi_vault?extIdCarryOver=true&sc_cid=701f2000001OH7YAAW) collection which must be installed before the role can be used
  * The [hvac](https://pypi.org/project/hvac/) Python module is required as a dependency of the `community.hashi_vault` Ansible module
* The private key should be stored as a base-64 encoded string in a key named `ssh_private_key` at the Vault path specified by the variable `hashicorp_vault_private_key_path`:

```json
{
  "ssh_private_key" : "d93hGhsh3Zk39..."
}
```

## Role Variables

The role defaults (see `defaults/main.yml`) assume that the key is being installed for the `root` user with key name `id_rsa`, and the directory `/home/root/.ssh` will be created with suitable permissions if it doesn't already exist.

To skip the creation of this directory:

```yaml
ssh_dir_check: no
```

To specify different ownership or permissions for the `.ssh` directory or key use one or more of the following:

```yaml
# Key options
ssh_key_owner: <user-name>
ssh_key_group: <group-name>
ssh_key_mode: <permissions>

# .ssh directory options
ssh_dir_owner: <user-name>
ssh_dir_group: <group-name>
ssh_dir_mode: <permissions>
```
The value of `ssh_dir_owner` (default `root`) is used to determine the path to the `.ssh` directory. For example, for `root` this will be `/root/.ssh` and for any non-root value this will be `/home/{{ ssh_dir_owner }}/.ssh`.

To specify a name other than the default `id_rsa` for the key being installed:

```yaml
ssh_key_name: <key-name>
```

To remove a previously installed key:

```yaml
ssh_key_action: remove
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
    ssh_key_name: my-app-key
```

## License

MIT
