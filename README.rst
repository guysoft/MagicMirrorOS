MagicMirrorOS
=============

An out of the box `Raspberry Pi <http://www.raspberrypi.org/>`_ Raspbian distro that lets you run `MagicMirror <https://github.com/MagicMirrorOrg/MagicMirror>`_ to make an interactive mirror.

Where to get it?
----------------

Download directly from `here <https://gitlab.com/khassel/magicmirroros/-/packages>`_

Variants for arm 32-bit (``armhf`` in image name) and arm 64-bit (``arm64`` in image name) are available.

How to use it?
--------------

#. Use the `Raspberry Pi Imager <https://www.raspberrypi.com/documentation/computers/getting-started.html#raspberry-pi-imager>`_ to install the zipped image to an SD card
#. Use the customization settings of the Raspberry Pi Imager for WiFi, hostname and user settings
#. Boot the Pi from the SD card
#. With the first start the docker images are pulled which takes some time, you can follow this process by executing ``journalctl --user -f``
#. You can change the settings of the MagicMirror in the files located at ``/opt/mm/mounts/``


Docker
------

Under the hood MagicMirrorOS uses `this docker setup <https://gitlab.com/khassel/magicmirror>`_. 

You find the docker setup at ``/opt/mm/`` on your raspberrypi. 
For more information about this setup, how you can start/stop the docker container,
how to see the logs , ..., please refer to the documentation provided there.
 

Requirements
------------
* Raspberrypi all versions should work
* 2A power supply
* Pi 2, 3, 4 & 5. The Raspberry Pi 0/1 is currently not supported.

Features
--------

* Runs `MagicMirror <https://github.com/MagicMirrorOrg/MagicMirror>`_ out-of-the-box


Developing
----------

Requirements
~~~~~~~~~~~~

#. Docker or Vagrant, docker recommended
#. Docker Compose Plugin - recommended if using docker build method, instructions assume you have it
#. Downloaded `Raspbian Lite <https://downloads.raspberrypi.org/raspbian_lite/images/>`_ image.
#. Root privileges for chroot
#. Bash
#. sudo (the script itself calls it, running as root without sudo won't work)

Build MagicMirrorOS
~~~~~~~~~~~~~~~~~~~

MagicMirrorOS can be built using docker running either on an intel or RaspberryPi (supported ones listed).
Build requires about 4.5 GB of free space available.

MagicMirrorOS supports building variants, this setup contains 2 variants for the 2 architectures, ``armhf`` and ``arm64``.

You can build it assuming you already have docker and the docker compose plugin installed issuing the following commands::

    
    variant="armhf"
    git clone https://github.com/guysoft/MagicMirrorOS.git
    cd MagicMirrorOS/src/image
    wget -c --trust-server-names 'https://downloads.raspberrypi.org/raspios_${variant}_latest'
    cd ..
    sudo docker compose up -d
    sudo docker exec -it magicmirroros-build build $variant
    
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
    run_vagrant_build.sh [Variant]
    
Usage
~~~~~

#. If needed, override existing config settings by creating a new file ``src/config.local``. You can override all settings found in ``src/config``. If you need to override the path to the Raspbian image to use for building MagicMirrorOS, override the path to be used in ``ZIP_IMG``. By default, the most recent file matching ``*-raspbian.zip`` found in ``src/image`` will be used.
#. Run ``src/build_dist`` as root.
#. The final image will be created in ``src/workspace``

Customization
~~~~~

#. If you need to rotate the output

   #. edit the file ``/opt/mm/run/.env`` and add e.g the following line ``XRANDR_PARAMS="--output HDMI-1 --rotate left --scale 1.9"`` where 'left' rotates 90 degrees counter clockwise, 'right' rotates 90 degrees clockwise, and 'inverted' rotates 180 degrees, if the scaling is not correct you can use the ``--scale`` parameter.
   #. Restart the docker container by executing ``docker compose up`` in directory ``/opt/mm/run``

#. The setup tries to set the timezone automatically, if you need to change your local timezone:

   #. Find your timezone in the "TZ database name" column on `Wikipedia <https://en.wikipedia.org/wiki/List_of_tz_database_time_zones>`_
   #. ``nano /opt/mm/run/compose.yaml`` and add::

        environment:
          TZ: <your timezone>
        
   #. Restart the docker container by executing ``docker compose up`` in directory ``/opt/mm/run``

Code contribution would be appreciated!
