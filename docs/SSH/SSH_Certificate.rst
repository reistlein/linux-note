=================================================
OpenSSH PKI, SSH certificate [#man_sshkeygen]_
=================================================


.. [#man_sshkeygen] https://man.openbsd.org/ssh-keygen.1#CERTIFICATES

---------------------------------------------------------------
Overviews
---------------------------------------------------------------

Since version 5.4, OpenSSH provides a PKI in order to facilitate key management by simplifying 
the number of relationships to maintain between entities (hosts and users) (see youtube video for dynamic explanation [#40]_ ).

 User certificates authenticate users to servers  

.. [#40] https://www.youtube.com/watch?v=Lqbtn0Gjnho

1.  Create CA certificate for user & host [#41]_ 
2.  Create user certificate [#42]_ 
3.  Create host certificate [#42]_ 
4.  Allows CA certificate for user & host [#42]_ & [#43]_

.. [#41] consise cookbook with openssh certificate https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Certificate-based_Authentication
.. [#42] details commands from https://www.lorier.net/docs/ssh-ca.html
.. [#43] explanation on open-ssh certificate & tools https://smallstep.com/blog/use-ssh-certificates/

---------------------------------------------------------------
Setup
---------------------------------------------------------------


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
1. Create CA certificate for user & host
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

On the CA server

.. code::

  ssh-keygen -f ca_hostkey -b 4096 -t rsa -C "host CA key" 
  ssh-keygen -f ca_userkey -b 4096 -t rsa -C "user CA key" 



^^^^^^^^^^^^^^^^^^^^^^^^^^
2. Create User Certificate
^^^^^^^^^^^^^^^^^^^^^^^^^^

* User certificates authenticate users to servers 

.. code::

  #generate private & public key
  ssh-keygen -f user_key  -C "user key" -N ""

  #sign the public key with the CA
  ssh-keygen -s ca_key -I "userkey_id" -n "username" -z +1  -V +52w user_key.pub 

-s ca_key   Certify (sign) a public key using the specified CA key 
-I certificate_identity   allows to identify the certificate for revocation)

-V validity_interval  Specify a validity interval when signing a certificate. (ex: +52w  Valid from now to 52 weeks from now).
-n principals  Certificates may be limited to be valid for a set of principal (user/host) names. By default, generated certificates are valid for all users or hosts. To generate a certificate for a specified set of principals:
see man ssh-keygen for more information ([#man_sshkeygen]_)
-z serial_number  Specifies a serial number to be embedded in the certificate to distinguish this certificate from others from the same CA

Full command to run on client ws, then to execute on the CA-server :

.. code::
echo ssh-keygen -s /etc/ssh/ca_userkey \
    -I \"$(whoami)@$(hostname --fqdn) user key\" \
    -n \"$(whoami)\" \
    -V -5m:+365d \
    -z +1 \
    ~/.ssh/id_rsa.pub \
    ~/.ssh/id_dsa.pub \
    ~/.ssh/id_ecdsa.pub


^^^^^^^^^^^^^^^^^^^^^^^^^^^^
3. Create Host  Certificate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The host certificate can re-use host public key, otherwise it is possible to generate a new hostkey using ``ssh-keygen -f ssh_host_rsa_key  -C "<comment>" -N ""``

Host certificates authenticate server hosts to users

.. code::

  ssh-keygen -s /path/to/ca_key -I "key_id" -n "ServerMachine1.example.com" -h /etc/ssh/ssh_host_rsa_key.pub -V +52w

Full command to run on host server, then execute the ssh-keygen command on the CA-server :

.. code::

  echo ssh-keygen -s /etc/ssh/ca_hostkey \
      -I \"$(hostname --fqdn) host key\" \
      -n \"$(hostname),$(hostname --fqdn),$(hostname -I|tr ' ' ',')\" \
      -V -5m:+365d \
      -z +1 \
      -h \
      /etc/ssh/ssh_host_rsa_key.pub \
      /etc/ssh/ssh_host_dsa_key.pub \
      /etc/ssh/ssh_host_ecdsa_key.pub


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
4.1. Server Allows CA certificate for user & host
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As root Edit ``/etc/ssh/sshd_config``

.. code::

  root $ cp ca_userkey.pub /etc/ssh/
  root $ cp ssh_host_rsa_key-cert.pub  /etc/ssh/

.. code::
  
root $ cat | tee -a /etc/ssh/sshd_config <<EOF
# Logging
SyslogFacility AUTH
LogLevel VERBOSE
# Specifies a file that lists principal names that are accepted for certificate authentication (%h = USER HOME). 
AuthorizedPrincipalsFile %h/.ssh/authorized_principals

# Trust user certificate
TrustedUserCAKeys /etc/ssh/ca_userkey.pub

# Host private key and certificate
HostKey  /etc/ssh/ssh_host_rsa_key
HostCertificate  /etc/ssh/ssh_host_rsa_key-cert.pub
EOF

Then restart ssh service

.. code::
  
  root$ systemctl restart sshd


On user home on the server side:

.. code::

  user$ echo $(whoami) > ~/.ssh/authorized_principals


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
4.2. Client Allows CA certificate for user & host
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Edit ``.ssh/known_hosts``

.. code::
  
  # Declare a CA key acceptable for any host
  @cert-authority * ssh-rsa AAAAB4Nz1...1P4=



echo "@cert-authority * $(cat ca_hostkey.pub)" >>~/.ssh/known_hosts


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
5. Test 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Precise the key using the parameter ``-i`` if the public/private key didn't have one of the default name:
* ~/.ssh/id_rsa
* ~/.ssh/id_ecdsa
* ~/.ssh/id_ecdsa_sk
* ~/.ssh/id_ed25519
* ~/.ssh/id_ed25519_sk
* ~/.ssh/id_dsa

From client 

.. code::

  ssh -i user_key <user>@<ip> 


-----------------------------------
Open-ssh Additional information 
-----------------------------------


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Convert certificate into open-ssh certificate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


 Note that OpenSSH certificates are a different, and much simpler, format to the X.509 certificates used in ssl(8).
Certificate created from  open-ssh cannot be used as X.509 certificate.
However ssh-keygen is able to convert PKCS8, X.509 certificate and PKCS#12 into public key (private key for PKCS#12) [#30]_ .  

.. [#30] see §4.1.3 https://www.ssi.gouv.fr/en/guide/openssh-secure-use-recommendations/

.. note::

  **Converting a PEM encoded public key into an OpenSSH public key**
  ..code::

      ssh-keygen -i -m PKCS8 -f <public key>
  
  **Retrieving the public key of an X.509 certificate and importing it into OpenSSH**
  ..code::
    
    openssl x509 -pubkey -noout -in <certificate> | ssh-keygen -i \
      -m PKCS8 -f /dev/stdin
  
  **Retrieving the private key in order to obtain the corresponding OpenSSH public key from a PKCS#12 file**
  ..code::
    
    openssl pkcs12 -in <PKCS#12 file> -nocerts -aes128 > <private key>
    openssl pkcs12 -in <PKCS#12 file> -nocerts -nodes | ssh-keygen -y \
      -f /dev/stdin > <public key>



^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Get more information on generated certificate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This following command allows to get details on public key and determine whether the keys is an open-ssh certificate

..code::

  root@ubuntu-focal:/home/vagrant/test_ssh# ssh-keygen -Lf  user_key.pub
  user_key.pub:1 is not a certificate



..code::

  root@ubuntu-focal:/home/vagrant/test_ssh# ssh-keygen -Lf  user_key-cert.pub
    user_key-cert.pub:
            Type: ssh-rsa-cert-v01@openssh.com user certificate
            Public key: RSA-CERT SHA256:FVinSc/J4B1YOicOLzNdD095UQyhnoCX6o1CiYMEUxw
            Signing CA: RSA SHA256:CfhJQ2eKooaTnOfC77a05hTQvjUHKe2LaUq8zoI7QpA (using rsa-sha2-512)
            Key ID: "userkey_id"
            Serial: 1
            Valid: from 2022-09-03T20:49:00 to 2023-09-02T20:50:22
            Principals:
                    username
            Critical Options: (none)
            Extensions:
                    permit-X11-forwarding
                    permit-agent-forwarding
                    permit-port-forwarding
                    permit-pty
                    permit-user-rc