-----------------
Command Iptables
-----------------

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Install iptables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

sudo systemctl stop firewalld
sudo systemctl disable firewalld
systemctl mask firewalld

yum install iptables-services
systemctl start iptables
systemctl enable iptables

^^^^^^^^^^^^^^^
Ping
^^^^^^^^^^^^^^^
Windows:
ping -t <ip>

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Configuration file:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
/etc/sysconfig/iptables

List all the tables in iptables
.. code::
    iptables -t filter --list
    iptables -t nat --list
    #specialized packet alteration (QOS bits in the TCP header)
    iptables -t mangle --list
    #configuration excemptions
    iptables -t raw --list

^^^^^^^^^^^^^^^
Delete rules
^^^^^^^^^^^^^^^

iptables -D INPUT -s {IP_ADDRESS} -j DROP

^^^^^^^^^^^^^^^
Appends rules
^^^^^^^^^^^^^^^

iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Allow Ping from Outside to Inside
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

(see [RULES-CMP]_)


iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT
iptables -A OUTPUT -p icmp --icmp-type echo-reply -j ACCEPT

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
How to block PING to your server with an error message?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In this way you can partially block the PING with an error message ‘Destination Port Unreachable’. 
Add the following iptables rules to block the PING with an error message. (Use REJECT as Jump to target)
iptables --flush
iptables -A INPUT -p icmp --icmp-type echo-request -j REJECT


.. [RULES-CMP] https://www.crybit.com/iptables-rules-for-icmp/

^^^^^^^^^^^^^^^^^^^^^^^^
Enable Iptables LOG
^^^^^^^^^^^^^^^^^^^^^^^^
We can simply use following command to enable logging in iptables.

iptables -A INPUT -j LOG

Change Iptables LOG File Name

To change iptables log file create a file in  /etc/rsyslog.d/ as following and then restart rsyslog service

.. code ::
    
    echo "kern.warning /var/log/iptables.log" > /etc/rsyslog.d/iptables.conf

    systemctl restart rsyslog

^^^^^^^^^^^^
IP
^^^^^^^^^^^^

.. code::

    ip address del 192.168.2.223/24 dev eth1
    ip address add 192.168.2.223/24 dev eth1
    ip addr show dev eth0

^^^^^^^^^^^^
Test
^^^^^^^^^^^^

/host/01_setup_offline.sh
