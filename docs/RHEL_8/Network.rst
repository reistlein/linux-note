----------------
Network Redhat8 
----------------


.. code::

    [admin@test ~]$ vi /etc/sysconfig/network-scripts/ifcfg-ens3
    TYPE=Ethernet
    PROXY_METHOD=none
    BROWSER_ONLY=no
    BOOTPROTO=dhcp
    DEFROUTE=yes
    IPV4_FAILURE_FATAL=no
    IPV6INIT=yes
    IPV6_AUTOCONF=yes
    IPV6_DEFROUTE=yes
    IPV6_FAILURE_FATAL=no
    IPV6_ADDR_GEN_MODE=stable-privacy
    NAME=ens3
    UUID=6af7189c-a1ff-465a-b21d-d9ea9a11a7f9
    DEVICE=ens3
    ONBOOT=yes

Wrong configuration (not found why)

    .. code::


    [admin@localhost ~]$ sudo vi /etc/sysconfig/network-scripts/ifcfg-ens3
    TYPE=Ethernet
    PROXY_METHOD=none
    BROWSER_ONLY=no
    BOOTPROTO=none
    DEFROUTE=yes
    IPV4_FAILURE_FATAL=no
    IPV6INIT=no
    IPV6_AUTOCONF=no
    IPV6_DEFROUTE=no
    IPV6_FAILURE_FATAL=no
    IPV6_ADDR_GEN_MODE=stable-privacy
    NAME=ens3
    UUID=997d5bdf-910d-4960-b659-583fd79646a9
    DEVICE=ens3
    ONBOOT=yes
    IPADDR=192.168.1.16
    PREFIX=24
    GATEWAY=192.168.1.1
    DNS1=192.168.1.1
    IPV6_PRIVACY=no
    ~
    ~


    systemctl restart NetworkManager
    


    Register Redhat (online)

    [admin@pc-56 ~]$ sudo subscription-manager  register
    Registering to: subscription.rhsm.redhat.com:443/subscription
    Username: Username@Mail.com
    Password:
    The system has been registered with ID: 5f3815e6-1ee9-4577-a6b1-d99a188de4d8
    The registered system name is: pc-56.home
    

    [admin@pc-56 ~]$ sudo subscription-manager attach --auto
    Installed Product Current Status:
    Product Name: Red Hat Enterprise Linux for x86_64
    Status:       Subscribed
    

    sudo subscription-manager repos --list

    https://developers.redhat.com/blog/2018/02/13/red-hat-cdk-nested-kvm/


    rhel-atomic-7-cdk-3.9-rpms

    sudo yum install cdk-minishift docker-machine-kvm

    sudo  subscription-manager repos --enable rhel-atomic-7-cdk-3.9-rpms
    sudo subscription-manager repos --enable rhel-7-server-devtools-rpms


    