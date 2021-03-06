[![License](http://img.shields.io/:license-apache-blue.svg)](http://www.apache.org/licenses/LICENSE-2.0.html) [![Build Status](https://travis-ci.org/simp/pupmod-simp-backuppc.svg)](https://travis-ci.org/simp/pupmod-simp-backuppc) [![SIMP compatibility](https://img.shields.io/badge/SIMP%20compatibility-4.2.*%2F5.1.*-orange.svg)](https://img.shields.io/badge/SIMP%20compatibility-4.2.*%2F5.1.*-orange.svg)

## Work in Progress

Please excuse us as we transition this code into the public domain.

Downloads, discussion, and patches are still welcome!

## This is a SIMP module
This module is a component of the [System Integrity Management Platform](https://github.com/NationalSecurityAgency/SIMP), a compliance-management framework built on Puppet.

If you find any issues, they can be submitted to our [JIRA](https://simp-project.atlassian.net/).

Please read our [Contribution Guide](https://simp-project.atlassian.net/wiki/display/SD/Contributing+to+SIMP) and visit our [developer wiki](https://simp-project.atlassian.net/wiki/display/SD/SIMP+Development+Home).

Puppet Module: backuppc

= DESCRIPTION =

BackupPC is a utility for archiving and restoring data from a central,
networked, location. The backuppc module allows for general use and
setup, but the default configuration is designed to securely pass data
using rsync over SSH.

= USAGE =

To get the basic BackupPC setup working in your environment, you
should follow either the File Based Authentication or LDAP
Authentication below.

Look at the code comments or the developer section under 'simp doc'
for additional information on extended usage.

== Server Configuration with File Based Authentication ==

This method allows for a basic setup and will provide you with a
working environment using Apache's file based basic authentication.

  # Repeat this for every user you want on the system.
  backuppc::server::user { 'username':
    password => 'output of ruby -r sha1 -r base64 -e 'puts "{SHA}"+Base64.encode64(Digest::SHA1.digest("password"))''
  }

include 'backuppc'

An example of <fqdn>.yaml:

---
backuppc::is_server: true
backuppc::backup_hosts:
  - backupserver.example.domain
backuppc::server::cgi_admin_users:
  - user1
  - user2
apache::conf::user: 'backuppc'
apache::conf::group: 'apache'
# Only set this if your clients have personal certificates.
apache::ssl::sslverifyclient: 'none'

== Server Configuration with LDAP Authentication ==

include 'backuppc'

An example <fqdn>.yaml config:

---
backuppc::backup_hosts:
  - 'backupserver.example.domain'
backuppc::server::cgi_admin_users:
  - 'user1'
  - 'user2'
backuppc::server::httpd_file_auth: false
backuppc::server::httpd_ldap_auth: true
apache::conf::user: 'backuppc'
apache::conf::group: 'apache'
apache::ssl::sslverifyclient: 'none'

At this point, you should be able to access BackupPC by accessing
https://<your_servername>/BackupPC.

== Configuring the Client ==

The client is much simpler to set up.

  include 'backuppc'

With the following in Hiera:

---
backuppc::backup_hosts:
  - 'backupserver.example.domain'

= NOTES =

 * After the first run, puppet is no longer authoritative for the BackupPC
   configuration by default. If you want puppet to be authoritative, you'll
   need to set $authoritative_conf to 'true' when calling
   backuppc::server::conf

 * This module automatically creates an SSH user key for BackupPC that is used
   by the bpc_user. This will not be created until a call to backuppc::conf has
   been made.

 * You can use both httpd_file_auth and httpd_ldap_auth simultaneously if you
   so choose.

= SEE ALSO =

The BackupPC web page: http://backuppc.sourceforge.net/
