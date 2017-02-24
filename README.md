# unify_config
This is configuration for a VPS in order to run the website for Unify Water.
Some of this code comes from https://sysadmincasts.com/episodes/45-learning-ansible-with-vagrant-part-2-4


## Summary

 Everything will be provisioned using ansible.

 - Wordpress (LMEP stack)
 - Rails project
    - nginx
    - mysql
    - rbenv
 - Letsencrypt

As of February, 2017, we will be running things under a single Digital Ocean Droplet. If traffic requires it, we may need to move things to multiple machines behind a load balancer.
s


## Development Enviornment Setup

As of February, 2017, the ubuntu/xenial64 box is not useable. see [here](https://bugs.launchpad.net/cloud-images/+bug/1569237). Need to use bento/ubuntu-16.04

 - run `vagrant up` to install a management server and nodes
 - ssh into `mgmt`: `vagrant ssh`
 - symlink to /vagrant which points to `~` in host machine `ln -s /vagrant ~`
 - create `ansible.cfg` and `production` (inventory file) in home directory (see examples)
 - create vars: `mv vars/main.yml.example vars/main.yml`
 - give your self the right permission `sudo chown -R vagrant:vagrant /home/vagrant`
 - load all hosts into known_hosts: `ssh-keyscan web1 >> .ssh/known_hosts`
 - generate an ssh key: `ssh-keygen -t rsa -b 2048`
 - `cd vagrant`
 - download roles from ansible-galaxy `sudo ansible-galaxy install -r roles.yml`
 - run a playbook to distribute the generated key: `ansible-playbook playbooks/ssh-addkey.yml --ask-pass`

## gather facts

This is an example of what `gather_facts` will expose on the host system.

    web1 | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "10.0.15.11",
            "10.0.2.15"
        ],
        "ansible_all_ipv6_addresses": [
            "fe80::a00:27ff:fedd:51ae",
            "fe80::a00:27ff:fec3:a85"
        ],
        "ansible_architecture": "x86_64",
        "ansible_bios_date": "12/01/2006",
        "ansible_bios_version": "VirtualBox",
        "ansible_cmdline": {
            "BOOT_IMAGE": "/vmlinuz-4.4.0-51-generic",
            "quiet": true,
            "ro": true,
            "root": "/dev/mapper/vagrant--vg-root"
        },
        "ansible_date_time": {
            "date": "2017-02-22",
            "day": "22",
            "epoch": "1487761092",
            "hour": "10",
            "iso8601": "2017-02-22T10:58:12Z",
            "iso8601_basic": "20170222T105812824796",
            "iso8601_basic_short": "20170222T105812",
            "iso8601_micro": "2017-02-22T10:58:12.824936Z",
            "minute": "58",
            "month": "02",
            "second": "12",
            "time": "10:58:12",
            "tz": "UTC",
            "tz_offset": "+0000",
            "weekday": "Wednesday",
            "weekday_number": "3",
            "weeknumber": "08",
            "year": "2017"
        },
        "ansible_default_ipv4": {
            "address": "10.0.2.15",
            "alias": "enp0s3",
            "broadcast": "10.0.2.255",
            "gateway": "10.0.2.2",
            "interface": "enp0s3",
            "macaddress": "08:00:27:c3:0a:85",
            "mtu": 1500,
            "netmask": "255.255.255.0",
            "network": "10.0.2.0",
            "type": "ether"
        },
        "ansible_default_ipv6": {},
        "ansible_devices": {
            "sda": {
                "holders": [],
                "host": "SATA controller: Intel Corporation 82801HM/HEM (ICH8M/ICH8M-E) SATA Controller [AHCI mode] (rev 02)",
                "model": "VBOX HARDDISK",
                "partitions": {
                    "sda1": {
                        "holders": [],
                        "sectors": "997376",
                        "sectorsize": 512,
                        "size": "487.00 MB",
                        "start": "2048",
                        "uuid": "c7de8013-2418-45a5-bda5-0023073bac35"
                    },
                    "sda2": {
                        "holders": [],
                        "sectors": "2",
                        "sectorsize": 512,
                        "size": "1.00 KB",
                        "start": "1001470",
                        "uuid": null
                    },
                    "sda5": {
                        "holders": [
                            "vagrant--vg-root",
                            "vagrant--vg-swap_1"
                        ],
                        "sectors": "82882560",
                        "sectorsize": 512,
                        "size": "39.52 GB",
                        "start": "1001472",
                        "uuid": null
                    }
                },
                "removable": "0",
                "rotational": "1",
                "sas_address": null,
                "sas_device_handle": null,
                "scheduler_mode": "deadline",
                "sectors": "83886080",
                "sectorsize": "512",
                "size": "40.00 GB",
                "support_discard": "0",
                "vendor": "ATA"
            }
        },
        "ansible_distribution": "Ubuntu",
        "ansible_distribution_major_version": "16",
        "ansible_distribution_release": "xenial",
        "ansible_distribution_version": "16.04",
        "ansible_dns": {
            "nameservers": [
                "10.0.2.3"
            ],
            "search": [
                "natcky.rr.com"
            ]
        },
        "ansible_domain": "",
        "ansible_enp0s3": {
            "active": true,
            "device": "enp0s3",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "off [fixed]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off",
                "rx_checksumming": "off",
                "rx_fcs": "off",
                "rx_vlan_filter": "on [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "off [fixed]",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_segmentation": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "off [fixed]",
                "tx_lockless": "off [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "off [fixed]",
                "tx_sit_segmentation": "off [fixed]",
                "tx_tcp6_segmentation": "off [fixed]",
                "tx_tcp_ecn_segmentation": "off [fixed]",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_segmentation": "off [fixed]",
                "tx_vlan_offload": "on [fixed]",
                "tx_vlan_stag_hw_insert": "off [fixed]",
                "udp_fragmentation_offload": "off [fixed]",
                "vlan_challenged": "off [fixed]"
            },
            "ipv4": {
                "address": "10.0.2.15",
                "broadcast": "10.0.2.255",
                "netmask": "255.255.255.0",
                "network": "10.0.2.0"
            },
            "ipv6": [
                {
                    "address": "fe80::a00:27ff:fec3:a85",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "08:00:27:c3:0a:85",
            "module": "e1000",
            "mtu": 1500,
            "pciid": "0000:00:03.0",
            "promisc": false,
            "speed": 1000,
            "type": "ether"
        },
        "ansible_enp0s8": {
            "active": true,
            "device": "enp0s8",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "off [fixed]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off",
                "rx_checksumming": "off",
                "rx_fcs": "off",
                "rx_vlan_filter": "on [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "off [fixed]",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_segmentation": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "off [fixed]",
                "tx_lockless": "off [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "off [fixed]",
                "tx_sit_segmentation": "off [fixed]",
                "tx_tcp6_segmentation": "off [fixed]",
                "tx_tcp_ecn_segmentation": "off [fixed]",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_segmentation": "off [fixed]",
                "tx_vlan_offload": "on [fixed]",
                "tx_vlan_stag_hw_insert": "off [fixed]",
                "udp_fragmentation_offload": "off [fixed]",
                "vlan_challenged": "off [fixed]"
            },
            "ipv4": {
                "address": "10.0.15.11",
                "broadcast": "10.0.15.255",
                "netmask": "255.255.255.0",
                "network": "10.0.15.0"
            },
            "ipv6": [
                {
                    "address": "fe80::a00:27ff:fedd:51ae",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "08:00:27:dd:51:ae",
            "module": "e1000",
            "mtu": 1500,
            "pciid": "0000:00:08.0",
            "promisc": false,
            "speed": 1000,
            "type": "ether"
        },
        "ansible_env": {
            "HOME": "/home/vagrant",
            "LANG": "en_US.UTF-8",
            "LANGUAGE": "en_US:",
            "LOGNAME": "vagrant",
            "MAIL": "/var/mail/vagrant",
            "PATH": "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games",
            "PWD": "/home/vagrant",
            "SHELL": "/bin/bash",
            "SHLVL": "1",
            "SSH_CLIENT": "10.0.15.10 60030 22",
            "SSH_CONNECTION": "10.0.15.10 60030 10.0.15.11 22",
            "SSH_TTY": "/dev/pts/0",
            "TERM": "xterm-256color",
            "USER": "vagrant",
            "XDG_RUNTIME_DIR": "/run/user/1000",
            "XDG_SESSION_ID": "14",
            "_": "/bin/sh"
        },
        "ansible_fips": false,
        "ansible_form_factor": "Other",
        "ansible_fqdn": "web1",
        "ansible_gather_subset": [
            "hardware",
            "network",
            "virtual"
        ],
        "ansible_hostname": "web1",
        "ansible_interfaces": [
            "lo",
            "enp0s3",
            "enp0s8"
        ],
        "ansible_kernel": "4.4.0-51-generic",
        "ansible_lo": {
            "active": true,
            "device": "lo",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on [fixed]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "on [fixed]",
                "netns_local": "on [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on [fixed]",
                "rx_fcs": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "off [fixed]",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on [fixed]",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on [fixed]",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_segmentation": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "off [fixed]",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off [fixed]",
                "tx_scatter_gather": "on [fixed]",
                "tx_scatter_gather_fraglist": "on [fixed]",
                "tx_sit_segmentation": "off [fixed]",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_segmentation": "off [fixed]",
                "tx_vlan_offload": "off [fixed]",
                "tx_vlan_stag_hw_insert": "off [fixed]",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "on [fixed]"
            },
            "ipv4": {
                "address": "127.0.0.1",
                "broadcast": "host",
                "netmask": "255.0.0.0",
                "network": "127.0.0.0"
            },
            "ipv6": [
                {
                    "address": "::1",
                    "prefix": "128",
                    "scope": "host"
                }
            ],
            "mtu": 65536,
            "promisc": false,
            "type": "loopback"
        },
        "ansible_lsb": {
            "codename": "xenial",
            "description": "Ubuntu 16.04.1 LTS",
            "id": "Ubuntu",
            "major_release": "16",
            "release": "16.04"
        },
        "ansible_machine": "x86_64",
        "ansible_machine_id": "c23a9acbf8faca41b28b23ba58416bd1",
        "ansible_memfree_mb": 143,
        "ansible_memory_mb": {
            "nocache": {
                "free": 681,
                "used": 311
            },
            "real": {
                "free": 143,
                "total": 992,
                "used": 849
            },
            "swap": {
                "cached": 0,
                "free": 1023,
                "total": 1023,
                "used": 0
            }
        },
        "ansible_memtotal_mb": 992,
        "ansible_mounts": [
            {
                "device": "/dev/mapper/vagrant--vg-root",
                "fstype": "ext4",
                "mount": "/",
                "options": "rw,relatime,errors=remount-ro,data=ordered",
                "size_available": 36696322048,
                "size_total": 40576331776,
                "uuid": "01e26162-30a4-421d-a7bb-25327f40222c"
            },
            {
                "device": "/dev/sda1",
                "fstype": "ext2",
                "mount": "/boot",
                "options": "rw,relatime,block_validity,barrier,user_xattr,acl",
                "size_available": 409380864,
                "size_total": 494512128,
                "uuid": "c7de8013-2418-45a5-bda5-0023073bac35"
            }
        ],
        "ansible_nodename": "web1",
        "ansible_os_family": "Debian",
        "ansible_pkg_mgr": "apt",
        "ansible_processor": [
            "GenuineIntel",
            "Intel(R) Core(TM) i7-3520M CPU @ 2.90GHz"
        ],
        "ansible_processor_cores": 1,
        "ansible_processor_count": 1,
        "ansible_processor_threads_per_core": 1,
        "ansible_processor_vcpus": 1,
        "ansible_product_name": "VirtualBox",
        "ansible_product_serial": "NA",
        "ansible_product_uuid": "NA",
        "ansible_product_version": "1.2",
        "ansible_python": {
            "executable": "/usr/bin/python",
            "has_sslcontext": true,
            "type": "CPython",
            "version": {
                "major": 2,
                "micro": 12,
                "minor": 7,
                "releaselevel": "final",
                "serial": 0
            },
            "version_info": [
                2,
                7,
                12,
                "final",
                0
            ]
        },
        "ansible_python_version": "2.7.12",
        "ansible_selinux": false,
        "ansible_service_mgr": "systemd",
        "ansible_ssh_host_key_dsa_public": "AAAAB3NzaC1kc3MAAACBAOB903BxAjMo7aYQohajpMaSOVTrv4zDjxCbL8xHaYPhqCt7uN16daJ3+GY5PgcauLdxTZsbXfXFKz1bR7Lb+TPk+MXDU45KY3bb2NnxyJXz3OE9GzoXxaHClEKIVrT0TopK1QW/mPfzkST8M4JG89IH29BmMTNQsfLQ8w615G8vAAAAFQCTiz8jTCfiacFR4EE+Zjxkr4RItwAAAIBhxG8+9spUt/i5XDlqA3AUbgxzWUIZW7Frwbtbd1aXg55njZX9PaN+2lv9NrmEosIaJjJmsJokh3GQMXEU5nMZCKXDbCJoiJvWgzi5S8Uni0N0SbwGpFBrbgCGez9bZmajGnAYfrUNBZWSTkeMLG3wggKdDMpmQpzHpi6664z2VQAAAIAlD3VCgYIEHAVT2LOIvQYeKjr8clSDGiqw50Ecd3SRPEeM0u6Ti32oDbha6kayHsqQ0AJcZKfOA7oVHaWMopq0rpLM9MnuiBRBMjkgItDeoARBdmTRQrsHz+sTQX8JmkSZgNLQnm7BYhTAWb+9+FUN6TOGgEN4R8Bj559CUnNXaQ==",
        "ansible_ssh_host_key_ecdsa_public": "AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBLZ+nw4Pim+DmXI0TcEYQQGI0/EWOh67aXS+1jKSaSpLuyj1MYlsNkFdn7UfX57Z/VD7a3bd+MNQ2IcE1jhBoM8=",
        "ansible_ssh_host_key_ed25519_public": "AAAAC3NzaC1lZDI1NTE5AAAAIN0/rmDFG/tqSX99eVAGfvyWXJ0wKbCOI9RMIvqrcb52",
        "ansible_ssh_host_key_rsa_public": "AAAAB3NzaC1yc2EAAAADAQABAAABAQCovC6TNWgKpJEBduP1AITBOHcPKoF+goxvUuaARQjGgoUTwoTkh/Y8SuLRQxQ1Oje3LNNBKSl3XF5XNJlIH+Gdj0O1FPP9OJSQjtB3ZCKse1IUPcOBClFNNUJ9f4yIIWRkck01a1mG8cNQREEBlHbYu6DxqqWXb9QXGB7vLfhWmJDE23HrG/jKWo7kRvR8UFxoqUhzn7rZSaNlkn1MSNwIQYWSF27gSel5An4h0AwB3Y85UMfzV65zc6sxUE9GwuqP9lAyLCyyMpzleAufvifOvAkOMK1Ok8y3J324BjLfycQUerJn6W/CepqzvLkogrhJ9dm8/ldikau7coJdNk6t",
        "ansible_swapfree_mb": 1023,
        "ansible_swaptotal_mb": 1023,
        "ansible_system": "Linux",
        "ansible_system_capabilities": [
            ""
        ],
        "ansible_system_capabilities_enforced": "True",
        "ansible_system_vendor": "innotek GmbH",
        "ansible_uptime_seconds": 3772,
        "ansible_user_dir": "/home/vagrant",
        "ansible_user_gecos": "vagrant,,,",
        "ansible_user_gid": 1000,
        "ansible_user_id": "vagrant",
        "ansible_user_shell": "/bin/bash",
        "ansible_user_uid": 1000,
        "ansible_userspace_architecture": "x86_64",
        "ansible_userspace_bits": "64",
        "ansible_virtualization_role": "guest",
        "ansible_virtualization_type": "virtualbox",
        "module_setup": true
    },
    "changed": false
    }
