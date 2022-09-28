---------------------
SSH Startup
---------------------

On client .ssh/config 

Linux

.. code::

  ssh-keygen -t ed25519 -C "testssh" -N "" -f .ssh/testssh_key


Using Windows 10 built in SSH

.. code::

  ssh-keygen -t ed25519 -C "testssh" -N '""' -f .ssh/testssh_key


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Setup environment 1/2 (ssh config)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

On client .ssh/config 

Host redhat
  Hostname 192.168.1.18
  IdentityFile ~/.ssh/testssh_key
  IdentitiesOnly yes
  User testssh
  
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Setup environment 2/2 (server)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

as root run the following command to setup the 'testssh' user

useradd testssh --create-home


mkdir -p /home/testssh/.ssh/
cat >"/home/testssh/.ssh/authorized_keys" <<< "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDa2CxBDArHFaM8EcBj+0+TB6/4bVZn84Tw6PUXztmT4 testssh"
chown -R testssh  /home/testssh/.ssh/
chmod 700 /home/testssh/.ssh/authorized_keys



^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Script Windows
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

create key
distribute new key
delete old key


Function generateNewKey {
$ssh_key_identifier = $args[0]
$keyfile=$args[1]
ssh-keygen -t ed25519 -C "$ssh_key_identifier"  -N '""' -f $keyfile  
}


generateNewKey "test1" .ssh/test1_key
cat .ssh/test1_key.pub| ssh redhat 'cat >> ~/.ssh/authorized_keys' 

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Script Linux
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

E:\00_majordome_folder\03_ws_linux\2022_01_22_ssh_key_rotate\rotateSSHKey.sh


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
SSH with hardware security key (HSM)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

ssh-keygen -t ed25519 -C "your_email@example.com"

You'll get a public/private keypair back as usual, except in this case, the private key file does not contain a highly-sensitive private key but instead holds a "key handle" that is used by the security key to derive the real private key at signing time.
(ref [manual-github-togeneratekeys]_ & [manual-github-togeneratekeys]_ )
.. code::

  ssh-keygen -t ecdsa-sk -C "your_email@example.com"


.. [manual-github-togeneratekeys] https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key-for-a-hardware-security-key
.. [forum-openssh-fidosupport] https://undeadly.org/cgi?action=article;sid=20191115064850