# MagicMirrorOS

An out of the box [Raspberry Pi](http://www.raspberrypi.org/) Raspbian distro that lets you run [MagicMirror²](https://github.com/MagicMirrorOrg/MagicMirror) to make an interactive mirror.


## Requirements
- 2A power supply
- Pi 3, 4 & 5. The Raspberry Pi 0/1/2 is currently not supported, the Pi Zero2w might be working.


## Installation

Variants for 32-bit (`armhf` in image name) and 64-bit (`arm64` in image name) are available.

### Using Raspberry Pi Imager v2

The new Raspberry Pi Imager v2 does not allow customizations (User/Password/SSH/WiFi/...) when loading an image file from disk. As alternative we provide an own content repository.

You find the content repository and a detailed description how to use it [here](https://gitlab.com/khassel/pi-imager).


### Using older versions of Raspberry Pi Imager or other SD-Card Writer

Download the image file from [here](https://gitlab.com/khassel/magicmirroros/-/packages) and use the file with your Image Writer.


## Docker and MagicMirror² files

Under the hood MagicMirrorOS uses [this docker setup](https://gitlab.com/khassel/magicmirror).

You find the docker setup at `/opt/mm/` on your Raspberry Pi. For more information about this setup, how you can start/stop the docker container, how to see the logs , ..., please refer to the [Documentation of this project](https://khassel.gitlab.io/magicmirror/).

After the first start the docker images are pulled which takes some time, you can follow this process by executing `journalctl --user -f`.

You find the custom files (config/css/modules) of MagicMirror² in the directories under `/opt/mm/mounts/`


## Customization

### Rotating the output

- Option 1

  - Edit the file `/opt/mm/run/.env` and add e.g. the following line `RANDR_PARAMS="--output HDMI-A-1 --transform 180"` to rotate the output by 180 degrees, or `RANDR_PARAMS="--output HDMI-A-1 --transform 90"` to rotate the output by 90 degrees, to see all possible options login to the container with `docker exec -it labwc bash` and then you can look at all the options available with `wlr-randr --help`. To get the parameter for `--output` you can call `wlr-randr`, you find the parameter in the first line (in this example `HDMI-A-1`).
  - Restart the docker container by executing `docker compose up` in directory `/opt/mm/run`.

  If you need to change the delay for the wlr-randr options to be applied, e.g. if the display is rotated when MagicMirror² is starting, it can result in a black screen. To avoid this, increase the delay (on slow systems e.g. pi < v4 you have to increase this up to 80s).

  - Edit the file `/opt/mm/run/.env` and add e.g. the following line `RANDR_DELAY=10s` to apply the wlr-randr options after 10 seconds, the default value is 5s.
  - Restart the docker container by executing `docker compose up` in directory `/opt/mm/run`.

- Option 2

  You can use css for rotating. Edit the file `/opt/mm/mounts/css/custom.css` and add the lines provided in [this forum post](https://forum.magicmirror.builders/topic/9707/save-performance-when-rotating-screen-e-g-on-raspberry-pi).

### Changing timezone

The setup tries to set the timezone automatically, if you need to change your local timezone:

- Find your timezone in the "TZ database name" column on [Wikipedia](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)
- `nano /opt/mm/run/compose.yaml` and add:

  ```bash
        environment:
          TZ: <your timezone>
  ```

- Restart the docker container by executing `docker compose up` in directory `/opt/mm/run`.


## Developing

### Requirements

1. Docker or Vagrant, docker recommended
2. Docker Compose Plugin - recommended if using docker build method, instructions assume you have it
3. Downloaded [Raspbian Lite](https://downloads.raspberrypi.org/raspbian_lite/images/) image.
4. Root privileges for chroot
5. Bash
6. sudo (the script itself calls it, running as root without sudo won't work)

### Build MagicMirrorOS

MagicMirrorOS can be built using docker running either on an intel or RaspberryPi (supported ones listed).
Build requires about 4.5 GB of free space available.

MagicMirrorOS supports building variants, this setup contains 2 variants for the 2 architectures, `armhf` and `arm64`.

You can build it assuming you already have docker and the docker compose plugin installed issuing the following commands:

```bash
variant="armhf"
git clone https://github.com/guysoft/MagicMirrorOS.git
cd MagicMirrorOS/src/image
wget -c --trust-server-names 'https://downloads.raspberrypi.org/raspios_${variant}_latest'
cd ..
sudo docker compose up -d
sudo docker exec -it magicmirroros-build build $variant
```

### Building Using Vagrant

There is a vagrant machine configuration to let build MagicMirrorOS in case your build environment behaves differently. Unless you do extra configuration, vagrant must run as root to have nfs folder sync working.

To use it:

```bash
sudo apt-get install vagrant nfs-kernel-server
sudo vagrant plugin install vagrant-nfs_guest
sudo modprobe nfs
cd MagicMirrorOS/src/vagrant
sudo vagrant up
```

After provisioning the machine, its also possible to run a nightly build which updates from devel using:

```bash
cd MagicMirrorOS/src/vagrant
run_vagrant_build.sh [Variant]
```

### Usage

1. If needed, override existing config settings by creating a new file `src/config.local`. You can override all settings found in `src/config`. If you need to override the path to the Raspbian image to use for building MagicMirrorOS, override the path to be used in `ZIP_IMG`. By default, the most recent file matching `*-raspbian.zip` found in `src/image` will be used.
2. Run `src/build_dist` as root.
3. The final image will be created in `src/workspace`

> Code contribution would be appreciated!
