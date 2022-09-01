=======================
How to configure Linux
=======================

-----------------------
How to change keyboard
----------------------

Ref [Tutorial_FR_ItConnect]_

.. [Tutorial_FR_ItConnect] https://www.it-connect.fr/passer-le-clavier-en-azerty-sur-centos-7-rhel-7/
Ici, les valeurs à observer pour le clavier sont le "VC Keymap" et le "X11 Layout" qui sont toutes deux en "us". Nous allons donc vouloir les passer en "fr", pour cela, regardons les keymap disponibles avec la commande suivante :




localectl status


localectl list-keymaps | grep fr
localectl list-x11-keymap-layouts

localectl list-keymaps | grep fr


localectl --no-convert set-x11-keymap fr


localectl set-keymap fr-azerty
localectl set-x11-keymap fr
echo $LANG

localectl set-locale en_US.utf8
localectl set-locale LANG=en_GB.utf8


https://docs.fedoraproject.org/en-US/Fedora/16/html/System_Administrators_Guide/sect-Configuring_the_Language_and_Keyboard-Layouts.html


---------------------------
Prepare to create live-CD
--------------------------

sudo dnf install livecd-tools

---------------------------
TODO
--------------------------

Ref. https://docs.docker.com/storage/storagedriver/overlayfs-driver/

DOCKER_OPTS="--storage-driver=overlay2"


https://fedoraproject.org/wiki/LiveOS_image
https://fedoraproject.org/wiki/LiveOS_image/overlay


https://slai.github.io/posts/customising-ubuntu-live-isos-with-docker/
https://stackoverflow.com/questions/49314556/how-to-build-my-own-custom-ubuntu-iso-with-docker


https://www.osbuild.org/news/2020-06-01-how-to-ostree-anaconda.html

{
sudo yum -y   install osbuild osbuild-ostree osbuild-composer composer-cli podman
sudo systemctl start osbuild-composer.socket
sudo yum -y install cockpit cockpit-composer
sudo systemctl start cockpit
firefox http://127.0.0.1:9090 &


 systemctl enable lorax-composer.socket
systemctl enable cockpit.socket
}


https://www.golinuxcloud.com/cockpit-image-builder-custom-rhel-iso-linux/