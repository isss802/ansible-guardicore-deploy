# Ansible Guardicore Deployment

## Overview

This Ansible project serves as a sample for efficiently deploying Guardicore agents to multiple Linux servers. It is compatible with Debian-based distributions.

## Project Structure

```
./
├── ansible.cfg
├── group_vars
│ └── guardicore_ubuntu.yml #create separately
├── guardicore.yml
└── linode.yml
```

## Prerequisites

- `ssh_config` is set up, and the Linode instance label names are used as Hostnames.
- Linode instances are tagged with the appropriate tags for Guardicore installation (in this sample, instances tagged with both 'guardicore' and 'ubuntu' are targeted).

## Usage

1. Generate a password for Vault usage

```
openssl rand -base64 32 > .vault-pass
```

2. Install Linode Ansible Collection

```
ansible-galaxy collection install linode.cloud
```

3. Create variables for Guardicore agent installation in group-vars (guardicore_ubuntu.yml)

```
ansible-vault edit group_vars/guardicore_ubuntu.yml
```

```
# Guardicore

## Base URL variable
gc_base_url: 'aggr-xxxxxxxxxxxx.cloud.guardicore.com'

## Install Profile
gc_ui_um_password: 'xxxxxxxxxxx'
gc_profile: 'xxxxxxxx'
gc_expected_checksum: 'xxxxxxxx'
gc_ssl_addresses: '{{ gc_base_url }}:443'
gc_cert_url: 'https://{{ gc_base_url }}/guardicore-cas-chain-file.pem'
gc_cert_path: '/tmp/guardicore_cas_chain_file.pem'
gc_get_install_file_cmd: 'wget --ca-certificate={{ gc_cert_path }} -O /tmp/guardicore_install.sh https://{{ gc_base_url }}'
gc_installation_cmd: 'sudo -E bash /tmp/guardicore_install.sh'
```

4. Set up Linode API Token:

To dynamically retrieve the current list of instances using the Ansible dynamic inventory plugin for Linode, follow these steps

Use the Ansible dynamic inventory plugin for Linode. You can find detailed information about this plugin at

https://docs.ansible.com/ansible/latest/collections/community/general/linode_inventory.html

For setting environment variables, we recommend using envrc.

This approach allows you to dynamically fetch and manage your Linode instances within your Ansible environment, providing an up-to-date inventory for your playbooks

```
export LINODE_ACCESS_TOKEN=xxxxxxxxxx
```

5. Run the playbook with the following command:

```
ansible-playbook guardicore_deploy.yml
```

# License

This project is released under the MIT License. For more details, please refer to the LICENSE file.
text
