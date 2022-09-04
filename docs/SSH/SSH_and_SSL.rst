================================
Get configuration
================================

On client the configuration is in :

* ~/.ssh/authorized_keys
  * authorized_keys from other host (usually empty on client)
* ~/.ssh/known_hosts 
  * verification of host key
* ~/.ssh/authorized_principals 
  * specific for open-ssh certificate
* /etc/ssh/ssh_config

On server the configuration is in :

* ~/.ssh/authorized_keys
  * authorized_keys from other hoste
* /etc/ssh/sshd_config



---------------------------------------------------------------
Get client  SSH configuration [#6]_ & [#7]_
---------------------------------------------------------------

Queries for the algorithms supported by one of the following features:

* cipher (supported symmetric ciphers)
* cipher-auth (supported symmetric ciphers that support authenticated encryption)
* mac (supported message integrity codes)
* kex (key exchange algorithms)
* key (key types)
* key-cert (certificate key types)
* key-plain (non-certificate key types)
* key-sig (all key types and signature algorithms)
* protocol-version (supported SSH protocol versions)
* sig (supported signature algorithms).

.. code::

  sshd -Q cipher #Supported cipher suites
  sshd -Q mac #Supported message integrity codes
  sshd -Q kex #Key exchange algorithms
  sshd -Q key #Key type

.. [#6] https://man.openbsd.org/ssh
.. [#7] https://cinhtau.net/2016/03/21/check-supported-algorithms-in-openssh/
---------------------------------------------------------------
Get server  SSH configuration [#10]_
---------------------------------------------------------------

.. code::


  sshd -T  #from the server side


  ssh -G toto@<hostname> -p <port>  #get information of the server from the client
  
  user toto
  hostname <hostname>
  port <port>
  addressfamily any
  ...
  numberofpasswordprompts 3
  serveralivecountmax 3
  serveraliveinterval 0
  ciphers aes128-ctr ....
  loglevel INFO
  macs hmac-sha2-512-etm@openssh.com....



.. [#10] https://linux-audit.com/audit-and-harden-your-ssh-configuration/


---------------------------------------------------------------
How to see which ciphers are supported by OpenSSL? [#20]_
---------------------------------------------------------------

.. code::

  openssl ciphers -v | column -t


.. [#20] https://linuxsecurity.expert/how-to/see-supported-openssl-ciphers/

