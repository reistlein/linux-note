=================================
How to configure PAM
=================================

Authentication in Linux is done by matching the encrypted password in /etc/shadow file with the entered one.

To check if your program uses Linux-PAM or not run the command ldd (print required library):

 ldd /bin/su

 .. code::

    @ws-linux:~$ ldd /bin/su
	linux-vdso.so.1 (0x00007ffdfa7fc000)
	libpam.so.0 => /lib/x86_64-linux-gnu/libpam.so.0 (0x00007fd9a1a38000)
	libpam_misc.so.0 => /lib/x86_64-linux-gnu/libpam_misc.so.0 (0x00007fd9a1a32000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fd9a1800000)
	libaudit.so.1 => /lib/x86_64-linux-gnu/libaudit.so.1 (0x00007fd9a17d2000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fd9a1a70000)
	libcap-ng.so.0 => /lib/x86_64-linux-gnu/libcap-ng.so.0 (0x00007fd9a1a2a000)



---------------------
Pam configuration:
---------------------


Syntax of Linux PAM

In the case of PAM, we have 2 configuration files that complete the flow of the PAM utility. The main configuration file for PAM is present at /etc/pam.conf, and the configurations for any of the PAM-aware applications are present in /etc/pam.d/directory. So now, the PAM-aware applications are the ones that have been compiled specifically for using PAM.

Given below is the syntax for the configuration of the main PAM file in Linux.

service type control-flag module module-arguments

Here, each of the place holders signifies a particular component. For example, service needs to be replaced by actual application name, type signifies the type of module or context or interface which is nothing but the action or function that the application asks the PAM to perform, control-flag signifies what the behavior of the PAM-API needs to be in case the authentication is unsuccessful, module denotes the actual path of the file or the relative pathname and finally module-arguments which will control the module behavior and contain a space-separated list of tokens.

Now, the syntax for each of the files that goes in /etc/pam.d/directory is pretty much similar to the one that we have seen above and looks like:

.. code::

    type control-flag module module-arguments

The only thing which is missing is the service, as each file is a service in itself.
How does PAM work in Linux?

To see the working of PAM and for that, we would need to initially look at one use case where PAM is in general used in the industry. In many places, one would use PAM to control attempts made to log in and allow only the authorized and authenticate to pass through to the GUI or interface. In some other scenarios, developers also use PAM to control users who are authorized to use the su binary for switching identities or, in some cases, passwd utility, which enables users to change the password.

Now let us look at the workflow of PAM in Linux, and then we will see the options that are available in the fields in the syntax. The entire process of authentication is split into 4 management groups, of which all of them are independent to each other.



In case of type, we have 4 modules present, and they are:

* Auth: This type is to ensure that the user is authenticated and that it is only them whom they are supposed to be.
* Account: This is to look at the status of the account and ensure that the corresponding jobs are allowed by that account.
* Session: Actions like the setting of usage limits or mounting of the directory are done.
* Password: This option enables the change of passwords or any other credential as per the requirement.

In the case of control-flag the following options are available:

 * Sufficient: These arguments mention that if this is satisfied, nothing else needs to be checked.
 * Requisite: The arguments mention that if this fails, everything else fails, and PAM is stopped, and a failure message is sent.
 * Required: The argument helps in noting an unsuccessful auth in case of failure, but the flow is not stopped. Very handy in cases of not letting an attacker know that something of this log is noted in the logs.
 * Optional: The results from this authorization are generally ignored.
 * Include: All lines in the config file are pulled in when the corresponding param is matched.

.. [#] https://www.educba.com/linux-pam/
.. [#] https://www.redhat.com/en/blog/pam-%E2%80%93-pluggable-authentication-modules-linux-and-how-edit-defaults?intcmp=701f20000012ngPAAQ


.. code::

    man pam
    
This man page describes the overall process, including the types of calls and a list of files involved.
.. code::

    man pam.conf

This man page describes the overall format and defines keywords and fields for the pam.d configuration files.

.. code::

    man -k pam_

This search of man pages lists pages available for modules installed.


^^^^^^^^^^^^
Pam module
^^^^^^^^^^^^

pam_unix module
    check the user’s credentials against /etc/shadow file ``auth required pam_unix.so``.

pam_cracklib module
    This module ensures that you will use strong passwords ``password required pam_cracklib.so retry=2 minlen=12 difok=6``.

pam_rootok module
    This module checks if the user ID is 0, which means only root users can run this service. ``auth sufficient   pam_rootok.so``

.. [#] https://www.redhat.com/sysadmin/pluggable-authentication-modules-pam


^^^^^^^^^^^^^^^^^^^^
Pam authentication 
^^^^^^^^^^^^^^^^^^^^

Edit the following file

* /etc/pam.d/common-auth
  
  * add as first line  ``auth sufficient pam_yubico.so mode=challenge-response``
  * to have debug add debug keyword ex: ``auth sufficient pam_yubico.so mode=challenge-response debug``
* /etc/pam.d/login
  
  * For terminal
  * ``auth sufficient pam_yubico.so mode=challenge-response``
  
* /etc/pam.d/gdm-password
  
  * For desktop
  * ``auth sufficient pam_yubico.so mode=challenge-response``


^^^^^^^^^^^^^^^^^^^^
Pam password 
^^^^^^^^^^^^^^^^^^^^

/etc/pam.d/passwd

.. code::

    @include common-password


/etc/pam.d/common-password

.. code::

    # here are the per-package modules (the "Primary" block)
    password        [success=1 default=ignore]      pam_unix.so obscure yescrypt
    # here's the fallback if no module succeeds
    password        requisite                       pam_deny.so
    # prime the stack with a positive return value if there isn't one already;
    # this avoids us returning an error just because nothing sets a success code
    # since the modules above will each just jump around
    password        required                        pam_permit.so
    # and here are more per-package modules (the "Additional" block)
    password        optional        pam_gnome_keyring.so
    password        optional        pam_ecryptfs.so
    # end of pam-auth-update config


pam_unix.so
    obscure (Enable some extra checks on password strength)
    yescrypt (encrypt it with the yescrypt algorithm)



see /etc/security/pwquality.conf [#]_

..  [#] https://www.redhat.com/sysadmin/pam-authconfig


---------------------------------------------
Automatic configuration pam-auth-update
---------------------------------------------

pam-auth-update use profile defined in ``/usr/share/pam-configs/`` to setup automatically the configuration 
The  script  makes  every  effort  to respect local changes to /etc/pam.d/common-*. (ref [#]_)

.. code::

    /usr/share/pam-configs/yubico
    Name: Yubico authentication with YubiKey
    Default: no
    Priority: 704
    Auth-Type: Primary
    Auth:
            required        pam_yubico.so   mode=client try_first_pass id=<client id> key=<secret key>
    Auth-Initial:
            required        pam_yubico.so   mode=client try_first_pass id=<client id> key=<secret key>


.. [#] man of pam-auth-update