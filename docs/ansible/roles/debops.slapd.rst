debops.slapd
############

|Travis CI| |test-suite| |Ansible Galaxy|

.. |Travis CI| image:: http://img.shields.io/travis/debops/ansible-slapd.svg?style=flat
   :target: http://travis-ci.org/debops/ansible-slapd

.. |test-suite| image:: http://img.shields.io/badge/test--suite-ansible--slapd-blue.svg?style=flat
   :target: https://github.com/debops/test-suite/tree/master/ansible-slapd/

.. |Ansible Galaxy| image:: http://img.shields.io/badge/galaxy-debops.slapd-660198.svg?style=flat
   :target: https://galaxy.ansible.com/list#/roles/2243



This role installs and manages ``slapd``, OpenLDAP server. It will
automatically generate and store secure passwords for administrator
accounts and install useful scripts.

.. contents:: Table of Contents
   :local:
   :depth: 2
   :backlinks: top

Installation
~~~~~~~~~~~~

This role requires at least Ansible ``v1.7``. To install it, run::

    ansible-galaxy install debops.slapd


Role dependencies
~~~~~~~~~~~~~~~~~

- ``debops.ferm``
- ``debops.secret``
- ``debops.tcpwrappers``


Role variables
~~~~~~~~~~~~~~

List of default variables available in the inventory::

    ---
    
    # ---- OpenLDAP BaseDN configuration ----
    
    # BaseDN value of the OpenLDAP server, based on DNS domain
    slapd_basedn: '{{ "dc=" + ansible_domain.split(".") | join(",dc=") }}'
    
    # Length of passwords set on administrator accounts
    slapd_password_length: '128'
    
    # Log level (see slapd-config(5) for detauls)
    slapd_log_level: 'none'
    
    # Default services enabled in /etc/default/slapd (change requires restart)
    slapd_default_services: [ 'ldap:///', 'ldapi:///' ]
    
    
    # ---- Network access to OpenLDAP server ----
    
    # Lists of hosts or CIDR networks which are allowed to access slapd service. If
    # none are specified, any host can connect to OpenLDAP server
    slapd_allow: []
    slapd_group_allow: []
    slapd_host_allow: []
    
    # Default setting in /etc/hosts.allow if no hosts are specified in above lists
    slapd_tcpwrappers_default: 'ALL'
    
    
    # ---- Administrator accounts & passwords ----
    
    # slapd passwords cannot be directly generated by Ansible; they are handled in
    # separate tasks during runtime. Here you can change the location of passwords
    # in debops.secret storage if needed, for example to point several slapd
    # servers to the same set of passwords.
    
    # ==== cn=admin,cn=config ====
    
    # Base path to BaseDN administrator password
    slapd_config_admin_basepw: '{{ secret + "/credentials/" + ansible_fqdn + "/slapd/cn=config/cn=admin,cn=config" }}'
    
    # Plaintext password of BaseDN administrator
    slapd_config_admin_password: '{{ slapd_config_admin_basepw + ".password" }}'
    
    # Hash of BaseDN administrator password
    slapd_config_admin_hash: '{{ slapd_config_admin_basepw + ".hash" }}'
    
    # ==== BaseDN administrator ====
    
    # Distinguished Name of main administrator account
    slapd_basedn_admin: '{{ "cn=admin," + slapd_basedn }}'
    
    # Base path to BaseDN administrator password
    slapd_basedn_admin_basepw: '{{ secret + "/credentials/" + ansible_fqdn + "/slapd/" + slapd_basedn + "/" + slapd_basedn_admin }}'
    
    # Plaintext password of BaseDN administrator
    slapd_basedn_admin_password: '{{ slapd_basedn_admin_basepw + ".password" }}'
    
    # Hash of BaseDN administrator password
    slapd_basedn_admin_hash: '{{ slapd_basedn_admin_basepw + ".hash" }}'
    
    
    # ---- ldapscripts configuration ----
    
    # See templates/etc/ldapscripts/ldapscripts.conf for more information about
    # these variables
    
    # Enable ldapscripts support (install and configure ldapscripts)
    slapd_ldapscripts: False
    
    # LDAP server to configure
    slapd_ldapscripts_server: 'ldap://localhost'
    
    # Default BaseDN to use in ldapscripts
    slapd_ldapscripts_suffix: '{{ slapd_basedn }}'
    
    # Oranizational Units for Groups, Users and Machines
    slapd_ldapscripts_gsuffix: 'ou=Groups'
    slapd_ldapscripts_usuffix: 'ou=Users'
    slapd_ldapscripts_msuffix: 'ou=Machines'
    
    # BindDN admin account and file with password
    slapd_ldapscripts_binddn: '{{ slapd_basedn_admin }}'
    slapd_ldapscripts_bindpwdfile: '/etc/ldapscripts/ldapscripts.passwd'
    
    # Where to look for admin account password
    slapd_ldapscripts_password_lookup: '{{ slapd_basedn_admin_password }}'
    
    # Groups, User and Machine  start IDs
    slapd_ldapscripts_gidstart: '10000'
    slapd_ldapscripts_uidstart: '10000'
    slapd_ldapscripts_midstart: '20000'
    
    # Group membership management
    # Possible values: posixGroup, groupOfNames, groupOfUniqueNames
    slapd_ldapscripts_gclass: 'posixGroup'
    slapd_ldapscripts_gdummymember: 'uid=dummy,$USUFFIX,$SUFFIX'
    
    # User password generation
    slapd_ldapscripts_passwordgen: 'pwgen'

List of internal variables used by the role::

    slapd_register_config_admin_hash
    slapd_register_basedn_admin_hash


Authors and license
~~~~~~~~~~~~~~~~~~~

``debops.slapd`` role was written by:

- Maciej Delmanowski | `e-mail <mailto:drybjed@gmail.com>`__ | `Twitter <https://twitter.com/drybjed>`__ | `GitHub <https://github.com/drybjed>`__

License: `GPLv3 <https://tldrlegal.com/license/gnu-general-public-license-v3-%28gpl-3%29>`_

