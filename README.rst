MagicMirrorOS
=============

An out of the box `Raspberry Pi <http://www.raspberrypi.org/>`_ Raspbian distro that lets you run `MagicMirror <https://github.com/MichMich/MagicMirror>`_ to make an interactive mirror.

Where to get it?
----------------

Official mirror is `here <http://unofficialpi.org/Distros/MagicMirrorOS>`_

How to use it?
--------------

#. Unzip the image and install it to an SD card `like any other Raspberry Pi image <https://www.raspberrypi.org/documentation/installation/installing-images/README.md>`_
#. Configure your WiFi by editing ``magicmirroros-wpa-supplicant.txt`` at the root of the flashed card when using it like a flash drive
#. Boot the Pi from the SD card
#. Hostname is ``magicmirroros`` (not ``raspberrypi`` as usual), username: ``pi`` and inital password is: ``raspberry``
#. You can change the settings of the MagicMirror in the files located at ``/boot/docker-compose/magicmirror/``


Docker
------

Under the hood MagicMirrorOS uses `this docker setup <https://gitlab.com/khassel/magicmirror>`_. 
At the first start the docker image is pulled which takes some time depending on your hardware, so please be patient ...

You find the docker setup at ``~/magicmirror/`` on your raspberrypi. 
For more information about this setup, how you can start/stop the docker container,
how to see the logs , ..., please refer to the documentation provided there.
 

Requirements
------------
* Raspberrypi all versions should work
* 2A power supply
* Pi 2, 3 & 4. The Raspberry Pi 0/1 is currently not supported.

Features
--------

* Runs `MagicMirror <https://github.com/MichMich/MagicMirror>`_ out-of-the-box


Developing
----------

Requirements
~~~~~~~~~~~~

#. Docker or Vagrant, docker recommended
#. Docker-compose - recommended if using docker build method, instructions assume you have it
#. Downloaded `Raspbian Lite <https://downloads.raspberrypi.org/raspbian_lite/images/>`_ image.
#. Root privileges for chroot
#. Bash
#. sudo (the script itself calls it, running as root without sudo won't work)

Build MagicMirrorOS
~~~~~~~~~~~~~~~~~~~

MagicMirrorOS can be built using docker running either on an intel or RaspberryPi (supported ones listed).
Build requires about 4.5 GB of free space available.
You can build it assuming you already have docker and docker-compose installed issuing the following commands::

    
    git clone https://github.com/guysoft/MagicMirrorOS.git
    cd MagicMirrorOS/src/image
    wget -c --trust-server-names 'https://downloads.raspberrypi.org/raspios_armhf_latest'
    cd ..
    sudo docker-compose up -d
    sudo docker exec -it magicmirroros-build build
    
Building MagicMirrorOS Variants
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MagicMirrorOS supports building variants, which are builds with changes from the main release build. An example and other variants are available in the folder ``src/variants/example``.

To build a variant use::

    sudo docker exec -it magicmirroros-build build [Variant]
    
Building Using Vagrant
~~~~~~~~~~~~~~~~~~~~~~
There is a vagrant machine configuration to let build MagicMirrorOS in case your build environment behaves differently. Unless you do extra configuration, vagrant must run as root to have nfs folder sync working.

To use it::

    sudo apt-get install vagrant nfs-kernel-server
    sudo vagrant plugin install vagrant-nfs_guest
    sudo modprobe nfs
    cd MagicMirrorOS/src/vagrant
    sudo vagrant up

After provisioning the machine, its also possible to run a nightly build which updates from devel using::

    cd MagicMirrorOS/src/vagrant
    run_vagrant_build.sh
    
To build a variant on the machine simply run::

    cd MagicMirrorOS/src/vagrant
    run_vagrant_build.sh [Variant]

Usage
~~~~~

#. If needed, override existing config settings by creating a new file ``src/config.local``. You can override all settings found in ``src/config``. If you need to override the path to the Raspbian image to use for building MagicMirrorOS, override the path to be used in ``ZIP_IMG``. By default, the most recent file matching ``*-raspbian.zip`` found in ``src/image`` will be used.
#. Run ``src/build_dist`` as root.
#. The final image will be created in ``src/workspace``

Code contribution would be appreciated!
