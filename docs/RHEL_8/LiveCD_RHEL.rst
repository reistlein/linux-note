----------------
Live CD RedHat
----------------

Reference [Fedora-EPEL]_ & [Redhat-KB44483]_

.. code::

    yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
    yum install livecd-tools

on RHEL 8 it is required to also enable the codeready-builder-for-rhel-8-*-rpms repository since EPEL packages may depend on packages from it:


.. code::

    subscription-manager repos --enable "codeready-builder-for-rhel-8-$(arch)-rpms"



.. [Fedora-EPEL]  https://fedoraproject.org/wiki/EPEL#How_can_I_use_these_extra_packages.3F


.. [Redhat-KB44483]  https://access.redhat.com/solutions/44483


livecd-iso-to-disk --home-size-mb 2048 Fedora-Workstation-Live-x86_64-34-1.1.iso /dev/sdX


If necessary, you can have livecd-iso-to-disk re-partition and re-format the target stick:

# livecd-iso-to-disk --format --reset-mbr --overlay-size-mb 4096 rhel-8.4-x86_64-dvd.iso /dev/sda



livecd-iso-to-disk --overlay-size-mb 4096 rhel-8.4-x86_64-dvd.iso /dev/sda