# Ansible Guardicore Deployment

## 概要

このAnsibleプロジェクトは、Guardicoreエージェントを複数のLinuxサーバーに効率的にデプロイするサンプルになります。
Debian系のディストリビューションに対応しています。

## プロジェクト構造

```
./
├── ansible.cfg
├── group_vars
│   └── guardicore_ubuntu.yml #別途作成
├── guardicore.yml
└── linode.yml
```

## 前提

- ssh_configが設定されており、Linodeインスタンスのラベル名がHostnameになっていること
- LinodeインスタンスのタグにGuardicoreをインストールするためのタグを設定していること(サンプルではguardicoreとubuntuと付与されているのが対象)

## 使用方法

1. Vaultで利用するパスワードを生成
```
openssl rand -base64 32 > .vault-pass
```

2. Linode Ansible Collectionをインストール

```
ansible-galaxy collection install linode.cloud
```

3. Guardicoreエージェントのインストールするための変数を`group-vars`に作成します。(`guardicore_ubuntu.yml`)

```
ansible-vault edit group_vars/guardicore_ubuntu.yml
```

```
# Guardicore

## 基本URLを変数化
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
4. LinodeのAPI Tokenを設定

envrcを利用することをおすすめ

```
export LINODE_ACCESS_TOKEN=xxxxxxxxxx
```

5. 以下のコマンドを実行してプレイブックを実行します：

```
ansible-playbook guardicore_deploy.yml
```

## ライセンス

このプロジェクトはMITライセンスの下で公開されています。詳細については、LICENSEファイルを参照してください。
