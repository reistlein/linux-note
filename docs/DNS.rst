=======================
How to DNS works
=======================

.. code::

    /etc/resolv.conf

^^^^^^^
POP-OS 
^^^^^^^

.. code::

    vi /etc/systemd/resolved.conf.d/dns_servers.conf

/run/systemd/resolve/stub-resolv.conf
.. [#] https://wiki.archlinux.org/title/Systemd-resolved



man systemd-resolved.service


/usr/lib/systemd/resolv.conf

 A static file /usr/lib/systemd/resolv.conf is provided that lists the 127.0.0.53 DNS stub (see above) as only DNS server. This file may be symlinked from /etc/resolv.conf in
           order to connect all local clients that bypass local DNS APIs to systemd-resolved. This file does not contain any search domains.
