debops.dnsmasq
##############

|Travis CI| |test-suite| |Ansible Galaxy|

.. |Travis CI| image:: http://img.shields.io/travis/debops/ansible-dnsmasq.svg?style=flat
   :target: http://travis-ci.org/debops/ansible-dnsmasq

.. |test-suite| image:: http://img.shields.io/badge/test--suite-ansible--dnsmasq-blue.svg?style=flat
   :target: https://github.com/debops/test-suite/tree/master/ansible-dnsmasq/

.. |Ansible Galaxy| image:: http://img.shields.io/badge/galaxy-debops.dnsmasq-660198.svg?style=flat
   :target: https://galaxy.ansible.com/list#/roles/1561



This role installs and configures ``dnsmasq`` as a local DNS, DHCP and
PXE/TFTP server. You can use it to configure host-based LAN behind a bridge
interface for virtual machines / containers, or use it in your internal
network.

.. contents:: Table of Contents
   :local:
   :depth: 2
   :backlinks: top

Installation
~~~~~~~~~~~~

This role requires at least Ansible ``v1.7.0``. To install it, run::

    ansible-galaxy install debops.dnsmasq


Role dependencies
~~~~~~~~~~~~~~~~~

- ``debops.ferm``
- ``debops.tcpwrappers``


Role variables
~~~~~~~~~~~~~~

List of default variables available in the inventory::

    ---
    
    # ---- Global options ----
    
    # Enable or disable access to local DNS from upstream networks. You can publish
    # your subdomain in public DNS by delegating it in your zone configuration.
    dnsmasq_public_dns: False
    
    # Enable or disable DHCPv4 support
    dnsmasq_dhcpv4: True
    
    # Enable or disable DHCPv6 support (router support is required)
    dnsmasq_dhcpv6: True
    
    # Enable or disable local router support (no routing information is sent to
    # hosts, DHCPv6 doesn't work)
    dnsmasq_router: True
    
    # Enable or disable TFTP server
    dnsmasq_tftp: True
    
    
    # ---- Network interface options ----
    
    # List of network interfaces that are assumed to be upstream networks, if
    # present on the host
    dnsmasq_upstream_interfaces: [ 'br0', 'br1', 'eth0', 'eth1' ]
    
    # Name of the IPv4 gateway interface
    dnsmasq_ipv4_gateway: '{{ ansible_default_ipv4.interface | default("") }}'
    
    # Name of the IPv6 gateway interface
    dnsmasq_ipv6_gateway: '{{ ansible_default_ipv6.interface | default("") }}'
    
    # List of internal interfaces on which dnsmasq will configure its services
    dnsmasq_interfaces:
    
        # Name of the interface
      - interface: 'br2'
    
        # DNS A record of the interface
        name: 'gw'
    
        # Optional DNS CNAME records of the interface
        aliases: [ 'router', 'gateway' ]
    
        # First IP address in the DHCP range (index)
        dhcp_range_start: '10'
    
        # Last IP address in the DHCP range (index)
        dhcp_range_end: '-10'
    
        # DHCP lease lifetime
        dhcp_lease: '24h'
    
        # DHCPv6 options configured on this interface
        ipv6_mode: 'ra-names,ra-stateless,slaac'
    
    
    # ---- DNS options ----
    
    # DNS domain configured by dnsmasq for all local networks
    dnsmasq_domain: '{{ ansible_fqdn }}'
    
    # Set DNS MX record of the main domain to dnsmasq host; set to False to disable
    dnsmasq_domain_mx: '{{ ansible_fqdn }}'
    
    # Alternative '/etc/hosts' read by dnsmasq, set to False to use default
    # '/etc/hosts' file
    dnsmasq_etc_hosts: '/etc/hosts.dnsmasq'
    
    # List of additional search domains. Set 'dnsmasq_search' to False
    # to completely disable this functionality.
    dnsmasq_search: [ '{{ dnsmasq_domain }}', '{{ ansible_domain }}' ]
    
    # Hash of DNS CNAME records to configure for hosts found in local DNS
    # Entries should be specified without the domain suffix
    dnsmasq_cname: {}
      #'host1': 'cname1'
      #'host2': [ 'list', 'of', 'aliases' ]
    
    
    # ---- TFTP options ----
    
    # Path to TFTP root directory
    dnsmasq_tftp_root: '/srv/tftpboot'
    
    # BOOTP command passed to clients (see dhcp-boot in dnsmasq(8)
    dnsmasq_tftp_boot: 'pxelinux.0'
    
    
    # ---- Other options ----
    
    # Additional options passed as a YAML text block
    dnsmasq_options: ''




Authors and license
~~~~~~~~~~~~~~~~~~~

``debops.dnsmasq`` role was written by:

- Maciej Delmanowski | `e-mail <mailto:drybjed@gmail.com>`__ | `Twitter <https://twitter.com/drybjed>`__ | `GitHub <https://github.com/drybjed>`__

License: `GPLv3 <https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29>`_

