Stunnel
=========
[![Galaxy](http://img.shields.io/badge/ansible--galaxy-stunnel-blue.svg)](https://galaxy.ansible.com/list#/roles/3502)
[![License](http://img.shields.io/:license-mit-blue.svg)](http://doge.mit-license.org)

This is a refreshed Fork of [migibert/stunnel-role](https://github.com/migibert/stunnel-role) - Thank you very much for your prework!

Ansible role to install stunnel in order to achieve SSL Termination on Linux machines.

Install it with `ansible-galaxy install cadirol.stunnel`


Role Variables
--------------

1. `stunnel_install_ssl_backend` (optional, default False) : determines if we want to install openssl by this role
1. `stunnel_use_certificate` (default True) : determines if we use certificates
1. `stunnel_use_psk` (default False) : determines if we use psk
1. `stunnel_certificate_generation` (default False) : determines if this role has to generate a self signed certificate
1. `stunnel_certificate_duration` (optional, if `stunnel_certificate_generation` is True, default 365) : self signed certificate validity duration
1. `stunnel_certificate_domain` (optional, if `stunnel_certificate_generation` is True, default www.domain.com) : self signed certificate domain field
1. `stunnel_certificate_country` (optional, if `stunnel_certificate_generation` is True, default CH) : self signed certificate country field
1. `stunnel_certificate_organization` (optional, if `stunnel_certificate_generation` is True, default organization) : self signed certificate organization field
1. `stunnel_certificate_state_name` (optional, if `stunnel_certificate_generation` is True, default country) : self signed certificate state field
1. `stunnel_certificate_locality` (optional, if `stunnel_certificate_generation` is True, default state) : self signed certificate locality field
1. `stunnel_certificate_file` certificate file to generate or use, depends on `stunnel_certificate_generation` value. Default is /tmp/certificate.pem
1. `stunnel_key_file` key file to generate or use, depends on `stunnel_certificate_generation` value. Default is /tmp/key.pem
1. `stunnel_psks` a list of psk. This look like this:
1. `stunnel_certificate_pem_file` certificate pem file

        - name: client1
          psk: AEO/WE+pBCn3+WBy3FJoyJF/HEBZqMym

2. `stunnel_services`: list of services.
    They look like this:

        - name: https
          accept: 443
          connect: 80

    Each service accepts parameters:
    1. `accept` (required) : determines address:port to listen
    2. `connect` (required) : determines address:port to connect
    3. `client` (optional, default `False`) : determines client-mode
    4. `use_psk` (optional, defaults to global `stunnel_use_psk`) : determines PSK usage for this specific service
    5. `PSKidentity` (optional, depends on `use_psk`) : determines PSK identity for this specific service. This identity should be configured in `PSKsecrets`

Dependencies
------------

This role has no dependencies.

Example Playbook
----------------

```yaml
- hosts: all

  roles:
  - role: stunnel-role
    stunnel_certificate_generation: True
    stunnel_certificate_duration: 365
    stunnel_certificate_domain: www.domain.com
    stunnel_certificate_country: CH
    stunnel_certificate_organization: organization
    stunnel_certificate_state_name: country
    stunnel_certificate_locality: state
    stunnel_certificate_file: /tmp/stunnel.pem
    stunnel_key_file: /tmp/key.pem
    stunnel_services:
      - name: https
        accept: 443
        connect: 80
```

you may also use [PSK (Pre Shared Keys)](https://www.stunnel.org/auth.html)
which allow faster communication
at the cost of knowing clients in advance.

```yaml
- hosts: all

  roles:
  - role: stunnel-role
    stunnel_use_certificate: false
    stunnel_use_psk: true
    stunnel_psks:
      - name: client1
        key: ATJX7VOAMIF2nhaknNVmSqSQGrCvMyPt
      - name: client2
        key: enNezGQMkZmSyjTDjpndjrBEXhJ9ki3v
    stunnel_services:
      - name: postfix
        accept: 12221
        connect: 21
      - name: mysql
        accept: 3307
        connect: 3306
        use_psk: yes
        client: yes
        PSKidentity: client2
```


License
-------

MIT - thankfull forked from Mikaël Gibert, Developer / Devops

Author Information
------------------

Adrian Kägi, Net-Ops
