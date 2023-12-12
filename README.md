# About

個人的な勉強用。EVPN/VXLAN Multihome 環境をEVENGで試したメモ用リポジトリ。

# Development

環境セットアップ方法を以下に記載します。

## Use Ansible

リストア  

```bash
ansible-playbook project/restore.yml --ask-vault-password
```

# Diagram

![](./diagram.drawio.png)

# Confioguration

## Policy

### ESI

検証でわかりやすくするため、先頭の1バイト（ESI Type）をのぞいた9バイトの内、先頭から6バイトの値はすべて同じ値を設定する  
残りの3バイトは0x00で埋める  

### LACP SystemID

ESIの10バイトの内、後方から数えて6バイトの値  
ESIが `00:11:22:33:44:55:66:77:88:99` の場合、SystemIDは `44:55:66:77:88:99` とする  

### ES import route-target

ESIの10バイトの内、先頭の1バイト（ESI Type）をのぞいた9バイトの内、先頭から6バイトの値  
ESIが `00:11:22:33:44:55:66:77:88:99` の場合、SystemIDは `11:22:33:44:55:66` とする  

## Parameter

Overlay

| | EVPN | VTEP | ASN |
| --- | --- | --- | --- |
| spine-01 | 10.0.0.0 |  | 65000 |
| leaf-01 | 10.0.0.1 | 10.1.0.1 | 65101 |
| leaf-02 | 10.0.0.2 | 10.1.0.2 | 65102 |
| leaf-03 | 10.0.0.3 | 10.0.0.3 | 65103 |

Underlay

|  | Local | Remote |  |
| --- | --- | --- | --- |
| leaf-01 Et1 | 10.2.0.1/31 | 10.2.0.0/31 | spine-01 Et8 |
| leaf-02 Et1 | 10.2.0.3/31 | 10.2.0.2/31 | spine-01 Et7 |
| leaf-03 ge-0/0/0 | 10.2.0.5/31 | 10.2.0.4/31 | spine-01 Et6 |

Customer

||address|ESI|
|---|---|---|
|customer-01|192.168.10.1|00:11:11:11:11:11:11:00:00:00|
|customer-02|192.168.10.2|00:22:22:22:22:22:22:00:00:00|
