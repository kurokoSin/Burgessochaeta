# Burgessochaeta
# 概要
rubyの開発環境構築を安定的に用意したくて、Vagrant +　Ansibleを使って、
仮想サーバの構築からrubyの開発環境の整備まで少ないコマンドで用意できるようにしました。

本記事はその結果出来上がったコードを記述したものです。
これ以降のシリーズではそれぞれの過程において苦労したところを記述していくつもりです。

## 構成図
以下は自分の環境で構築した時の構成図です。

* 母艦(Ubuntu)にAnsibleコントロール用の仮想サーバ
* Ruby開発用の仮想サーバ

の構成になっております。

![structImage.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/14800/70097dab-8135-903e-4843-5e9d89e5e3ed.png)

## 構成

母艦：
　　OS:Ubnutu 18.04.2 LTS
　　仮想化：KVM　(libvirt 4.0.0)
　　Vagrant 2.0.2

子１：
　　ホスト名：ansible-ontrol
　　box OS: Ubuntu 18.04.2 LTS(generic/Ubuntu1804 1.9.12)
　　Ansible: 2.8

子２：
　　ホスト名：ansible-web
　　box OS: Ubuntu 18.04.2 LTS(generic/Ubuntu1804 1.9.12)
　　ruby: 2.6.3

# 本記事でやること
子１

* IPv6 の無効化
* 時刻同期サーバの設定(同期先：NICT)

子2

* IPv6 の無効化
* 時刻同期サーバの設定(同期先：NICT)
* git インストール
* Ruby用パッケージの追加インストール(gcc,make等)
* rbenv　インストール
* ruby-build インストール


# 実行手順
```bash
$ mkdir -p /srv/vagrant && cd /srv/vagrant
$ git clone https://github.com/kurokoSin/Burgessochaeta.git .
$ vagrant up
$ vagrant ssh control
$ ansible-playbook playbook/site.yml
```

## ディレクトリ構成

```
.
├── README.md
├── Vagrantfile
├── control
│   ├── ansible_conf.sh
│   ├── expect_sendkey.expect
│   └── playbook
│       ├── ansible.cfg
│       ├── config.yml
│       ├── hosts_ansible
│       ├── roles
│       │   ├── AddPacks
│       │   │   └── tasks
│       │   │       └── main.yml
│       │   ├── Japanize
│       │   │   └── tasks
│       │   │       └── main.yml
│       │   ├── common
│       │   │   └── tasks
│       │   │       └── main.yml
│       │   ├── git
│       │   │   └── tasks
│       │   │       └── main.yml
│       │   ├── ntp
│       │   │   ├── handlers
│       │   │   │   └── main.yml
│       │   │   ├── tasks
│       │   │   │   └── main.yml
│       │   │   ├── templates
│       │   │   │   └── ntp.conf.j2
│       │   │   └── vars
│       │   │       └── main.yml
│       │   ├── rbenv
│       │   │   ├── tasks
│       │   │   │   └── main.yml
│       │   │   └── templates
│       │   │       └── rbenv_setting.sh.j2
│       │   ├── ruby
│       │   │   ├── tasks
│       │   │   │   └── main.yml
│       │   │   └── templates
│       │   │       └── rbenv_setting.sh.j2
│       │   └── ssh
│       │       ├── tasks
│       │       │   └── main.yml
│       │       └── vars
│       │           └── main.yml
│       └── site.yml
└── web
    └── pexpect_sndekey.py

```



