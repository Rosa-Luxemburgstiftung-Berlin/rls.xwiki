[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](http://www.gnu.org/licenses/gpl-3.0)
![Ansible 12 read](https://img.shields.io/badge/ansible_12-ready-green?logo=ansible&labelColor=black)


# rls.xwiki

Setup [XWiki](https://www.xwiki.org/) on debian

## Features

  * deploy and configure xwiki on debian for
    * different appserver: tomcat10 , tomcat9, xjetty
    * different dbserver: mariadb, mysql, pgsql, ...
  * optional configuration for tomcat connectors
  * optional configuration for apache proxy

## Role Variables

[defaults/main.yml](defaults/main.yml)


## Example

### Vars

run xwiki with ssl support and apache proxy

```yaml
mariadb_databases:
  - xwiki

dbpw: XYZ_VAULT-ME_123

mariadb_users:
  - name: xwiki
    host: localhost
    password: "{{ dbpw }}"
    priv: "xwiki.*:All"

dbconfig:
  dbpass: "{{ dbpw }}"

xwiki_additional_packages:
  - libreoffice

systemdreadwritepaths:
  - /var/lib/tomcat/.cache

xwiki:
  properties:
    openoffice.serverType: '0'
    openoffice.autoStart: 'true'
    openoffice.homePath: /usr/lib/libreoffice/
  cfg:
    xwiki.store.cache.capacity: 1000

tomcatconffiles:
  - xwiki.ykiwi.p12

pkcspass: YetAnotherSecreteThatShouldBeVaulted

catalina:
  connector:
    8080: # port
      state: absent
    8443:
      state: present
      maxParameterCount: 1000
      address: 127.0.0.1
      protocol: "org.apache.coyote.http11.Http11NioProtocol"
      maxThreads: 150
      SSLEnabled: "true"
      elements:
        - '<SSLHostConfig>
              <Certificate certificateKeystoreFile="conf/xwiki.ykiwi.p12"
                         certificateKeystorePassword="{{ pkcspass }}" certificateKeystoreType="PKCS12" />
            </SSLHostConfig>'

use_apache_proxy: true
apache:
  XtraConfigHTTPS: |
      SSLProxyEngine on
  SSLCertificateCopy: true
  SSLCertificateFile: /etc/ssl/certs/xwiki.ykiwi.pem
  SSLCertificateKeyFile: /etc/ssl/private/xwiki.ykiwi.key
  Proxy: 127.0.0.1:8443
  xwiki_proto: https
```
### Play

```yaml
- hosts: xwiki
  roles:
    - role: systemli.mariadb
      tags: mariadb
    - role: rls.xwiki
  tags: xwiki
```
