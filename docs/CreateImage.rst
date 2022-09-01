
------------------------
Create disque image
------------------------

.. code::

    dd if=/dev/vda bs=4M conv=sparse | pv -s 25G | gzip > /mnt/tmp_disk/ubuntu.gz


------------------------
Create ISO image
------------------------

* ``-V`` Specifies  the  volume  ID (volume name or label)
* ``-R`` Generate SUSP and RR records using the Rock Ridge protocol to further describe the files on the ISO9660 filesystem.
* ``-J`` Generate  Joliet  directory  records in addition to regular ISO9660 filenames.  This is primarily useful when the discs are to be used on Windows machines.  Joliet
filenames are specified in Unicode and each path component can be up to 64 Unicode characters long.  Note that Joliet is not a standard —  only  Microsoft  Windows
and Linux systems can read Joliet extensions.  For greater portability, consider using both Joliet and Rock Ridge extensions.

.. code::

    genisoimage -o backup.iso -V BACKUP -R -J RobotScripts/

.. code::
    losetup /dev/loop0 test.iso

Delete loop

.. code::
    
    losetup -d /dev/loop0