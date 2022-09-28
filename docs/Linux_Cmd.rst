===================================
Usefull linux command
===================================

---------------------------
10. List package installed
---------------------------

Debian/Ubuntu

.. code::

    apt list --installed
    sudo apt list update

RedHat/Suse

.. code::
    
    rpm -qa


Flatpack

.. code::

    flatpak list
    flatpak update

As of now, there are two main universal package systems: Snap and Flatpak. 
Both function in similar fashion, but one is found by default on Ubuntu-based systems (Snap) and one on Fedora-based systems (Flatpak).

.. [#] https://www.linux.com/training-tutorials/how-install-and-use-flatpak-linux/


-------------------------
20. Dos2unix equivalent
-------------------------

.. code::

    sed -i 's/\r//' filename