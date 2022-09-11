=============================================
Samba Installation  & Configuration
=============================================

--------------------------------
Linux Samba server 
--------------------------------

^^^^^^^^^^^^^^^^^^^^^^
Samba configuration
^^^^^^^^^^^^^^^^^^^^^^


.. [#] https://doc.ubuntu-fr.org/samba_smb.conf



But you can change that by editing /etc/samba/smb.conf and adding the following to the [global] section [#]_ :

.. code::

    server min protocol = SMB3
    #client min protocol = SMB3

.. [#] https://askubuntu.com/questions/919967/how-to-tell-gigolo-gvfs-to-use-smbv2-for-windows-shares

.. code::


    #Specifies which ports the server should listen on for SMB traffic.
    smb ports = 445 139 

    bind interfaces only = yes 
    interfaces = enp0s3 lo
.. [#] https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html#BINDINTERFACESONLY


.. code::

    server signing = required
    client signing = required

    map to guest  = never

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Install samba (ubuntu) [#smblinux1]
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code::

    sudo apt install samba

    mkdir /home/<username>/sambashare/

    user$ cat | sudo tee -a /etc/samba/smb.conf <<EOF
    [sambashare]
        comment = Samba on Ubuntu
        path = /home/vagrant/sambashare
        read only = no
        browsable = yes
    EOF

    sudo service smbd restart
    sudo ufw allow samba
    
    sudo smbpasswd -a username

    sudo useradd -s /usr/sbin/nologin test1
    sudopasswd -l test1
    sudo smbpasswd -a test1

.. code::

    log file = /var/log/samba/log.%m

.. [#smblinux1] https://ubuntu.com/tutorials/install-and-configure-samba#3-setting-up-samba


New-SmbMapping : Multiple connections to a server or shared resource by the same user, using more than one user name,
are not allowed. Disconnect all previous connections to the server or shared resource and try again.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Samba 4 Active Directory
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

https://www.jjworld.fr/active-directory-linux-avec-samba-4/
https://www.linux-magazine.com/Online/Features/What-s-New-in-Samba-4
https://www.linux-magazine.com/Issues/2016/191/Samba-4


--------------------------------
Windows Samba server 
--------------------------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Get Samba server configuration on Windows [#1]_
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


.. code::

    Get-SmbServerConfiguration | Select EnableSMB1Protocol,EnableSMB2Protocol


.. [#1] https://docs.microsoft.com/en-us/windows-server/storage/file-server/troubleshoot/detect-enable-and-disable-smbv1-v2-v3


^^^^^^^^^^^^^^^^^^^^^^^^^^
Create Samba share
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code::

    $shareTocreate = @{
        FilePath = "C:\DFSRoots\Files"
        ShareName = "files"
    }


    $shares = @($shareTocreate)

    $shareTocreate  | %{ New-Item -Path "$($_.FilePath)" -ItemType Directory -ErrorAction SilentlyContinue}
    $shareTocreate  | %{ Remove-SmbShare  -Name "$($_.ShareName)" -Force:$true }
    $shareTocreate  | %{ New-SmbShare -Name "$($_.ShareName)" -Path "$($_.FilePath)" -FullAccess "Domain Admins" -ChangeAccess "Protected Users" -ReadAccess "Domain Users"  }
    $shareTocreate  | %{ Set-SmbPathAcl -ShareName "$($_.ShareName)"}

    $Domain=$($env:USERDNSDOMAIN).toLower()
    $ComputerFQDN=$("$($env:COMPUTERNAME).$($env:USERDNSDOMAIN)").toLower()


    #More info on DFS
    #https://windowsmasher.wordpress.com/2014/04/01/getting-started-with-dfsn-and-powershell/

    #Delete DFS Root
    Remove-DfsnRoot  -Path "\\$($Domain)\documents"  -Force:$true
    New-DfsnRoot -Path "\\$($Domain)\documents" -TargetPath "\\$($ComputerFQDN)\files" -Type DomainV2

--------------------------------
Samba client Windows
--------------------------------


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Get Samba client configuration on Windows [#1]_
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


.. code::

   Get-SmbClientConfiguration


^^^^^^^^^^^^^^^^^^^^^^^^^^^^
List mounting point
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code::

    net use
    or 
    Get-SmbMapping

^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Create mounting point
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

On windows 10 use New-SmbMapping [#]_

.. code::

    $Credential = Get-Credential
    New-SmbMapping -LocalPath K: -RemotePath \\192.168.95.10\sambashare -UserName $Credential.UserName -Password $Credential.GetNetworkCredential().Password
    explorer K:
    #
    #Remove-SmbMapping -LocalPath "K:" 


.. [#]  https://docs.microsoft.com/en-us/powershell/module/smbshare/new-smbmapping?view=windowsserver2022-ps



=====================================
How to do troubleshooting SMB
=====================================

* Official Samba web site  [samba_doc]_
* Man page [man_smb.conf]_

.. [samba_doc] https://www.samba.org/samba/docs/


.. [man_smb.conf] https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html

---------------------------------------------------
Linux troubleshooting
---------------------------------------------------

PDF version of the Troubleshooting Techniques chapter from the second edition of Sam's Teach Yourself Samba in 24 Hours (Dec. 2001) [#]_

.. [#] https://www.samba.org/samba/docs/Samba24Hc13.pdf

see below the check from teh PDF:

Testparm command help to check samba configuration [#]_ 

.. code::

  testparm

.. [#] ttps://linux.die.net/man/1/testparm


Check that samba server is running

.. code::

    ps -ef | grep smbd

   # -N for anonymious connection
   smbclient -L 127.0.0.1 -N


   smbclient //192.168.95.10/sambashare -U user%password

Check that nmbd is running (NetBIOS name server to provide NetBIOS )

.. code::

    ps -ef | grep nmbd

    nmblookup -U 127.0.0.1 __SAMBA__



https://devanswers.co/network-error-problem-windows-cannot-access-hostname-samba/


---------------------------------------------------
Windows 10 Hardening issue
---------------------------------------------------

Starting in Windows 10, version 1709 and Windows Server 2019, SMB2 and SMB3 clients no longer allow the following actions by default:

    Guest account access to a remote server.
    Fall back to the Guest account after invalid credentials are provided.

SMB2 and SMB3 has the following behavior in these versions of Windows:

    Windows 10 Enterprise and Windows 10 Education no longer allow a user to connect to a remote share by using guest credentials by default,
     even if the remote server requests guest credentials.
     
    Windows Server 2019 Datacenter and Standard editions no longer allow a user to connect to a remote share by using guest credentials by default, even if the remote server requests guest credentials.
    Windows 10 Home and Pro are unchanged from their previous default behavior; they allow guest authentication by default.

Cause: This change in default behavior is by design and is recommended by Microsoft for security. [Hardening_Windows_Smb]_ &  [Blog_cannot_access_samba]_

^^^^^^^^^^^^^^^^^^^^^^^^^^
Resolution workaround
^^^^^^^^^^^^^^^^^^^^^^^^^^

If you want to enable insecure guest access, you can configure the following Group Policy settings:

    Open the Local Group Policy Editor (gpedit.msc).
    In the console tree, select Computer Configuration > Administrative Templates > Network > Lanman Workstation.
    For the setting, right-click Enable insecure guest logons and select Edit.
    Select Enabled and select OK.



.. [Blog_cannot_access_samba] https://devanswers.co/network-error-problem-windows-cannot-access-hostname-samba/


.. [Hardening_Windows_Smb] https://docs.microsoft.com/en-US/troubleshoot/windows-server/networking/guest-access-in-smb2-is-disabled-by-default


^^^^^^^^^^^^^^^^^^^^^^^^^^
Resolution fix
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code::

    map to guest  = never

https://askubuntu.com/questions/1117030/how-do-i-configure-samba-so-that-windows-10-doesn-t-complain-about-insecure-gues#1145981



^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Do no use alias for Samba share
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Do not use DNS CNAMEs in the future for file servers [#2]_ .
If you want to still give alternate names to servers, you can do so with the following command:

.. code::

    NETDOM COMPUTERNAME/ADD

This command automatically registers SPNs for the alternate names. 

.. [#2] https://docs.microsoft.com/en-us/troubleshoot/windows-server/networking/dns-cname-alias-cannot-access-smb-file-server-share?source=recommendations


^^^^^^^^^^^^^^^^^^^^^^^^^^^^
List environment variable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get-Item -Path Env:


^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Reload NetBios share_name
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code::

    #List all the netbios name
    nbstat -n

    #as admin
    nbstat -RR


^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Delete the ARP cache
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code::
    
    ARP -d

^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Delete kerberos cache
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code::

#list the kerberos ticket
klist 
#purge the kerberos ticket

KLIST /PURGE


^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Capture trafick [#3]_
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code::

    Netsh trace start capture=yes tracefile=capturefile.etl report=yes maxsize=500mb
    #Netsh trace stop


 conversion from ETL to PCAP [#4]_

.. code::

    ./etl2pcapng.exe capturefile.etl out.pcapng

        IF: medium=eth                  ID=0    IfIndex=14      VlanID=0
        IF: medium=eth                  ID=1    IfIndex=19      VlanID=0
        IF: medium=eth                  ID=2    IfIndex=20      VlanID=0
        IF: medium=eth                  ID=3    IfIndex=23      VlanID=0
        IF: medium=eth                  ID=4    IfIndex=27      VlanID=0


.. [#3] https://msandbu.org/network-packet-trace-with-netsh-and-analysis-with-wireshark/
.. [#4] https://github.com/microsoft/etl2pcapng



