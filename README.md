# HOWTO fork CoreOS

… and incorporate a customized kernel **into the current beta channel 766.3.0**. NOTE: This description is very specific and most likely not the solution to your problem. Sorry 'bout that. :)

## Preparation 1.0

1. Create a droplet/instance/machine whatever that's as fast as possible. With a 20 core machine from DigitalOcean the process took roughly one and a half hour and another half hour to bzip2, copy, bunzip and dump the image to disk. We used Ubuntu 14.04 as a base but ymmv.
2. Install the usual tools: git, htop, curl, screen/tmux/...
3. Create a local user and give hin full sudo rights. Make sure both ssh and sudo work flawlessly.
4. Run you preferred detachable terminal multiplexer. Honestly, you don't want an unstable network to fuck up the build in the 90th+ minute.

## Preparation 2.0

Completely taken from [Modifying CoreOS](https://coreos.com/os/docs/latest/sdk-modifying-coreos.html):

1. configure git

		git config --global user.email "you@example.com"
		git config --global user.name "Your Name"
2. install repo

		mkdir ~/bin
		export PATH="$PATH:$HOME/bin"
		curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
		chmod a+x ~/bin/repo

## Bootstrap the CoreOS SDK (1)

Partially taken from Steps stolen directly from [Modifying CoreOS](https://coreos.com/os/docs/latest/sdk-modifying-coreos.html):

1. make workdir
	
		cd ~; mkdir coreos; cd coreos

2. Initialize repo. From here on we deviate from [Modifying CoreOS](https://coreos.com/os/docs/latest/sdk-modifying-coreos.html)

		repo init -b build-766 -m release.xml -u https://github.com/coreos/manifest.git
		repo sync

## repo branch

1. start your own repo branch

		repo start uvcvideo src/third_party/coreos-overlay

2. apply some changes. You probably want to deviate from this description here. n.b.: This is from memory, most likely some details are not entirely correct.

		cd src/third_party/coreos-overlay
		git pull <wherever>/coreos-overlay.gitbundle uvcvideo
		cd -

## Bootstrap the CoreOS SDK (2)

1. donwload and enter the SDK

		./chromite/bin/cros_sdk --enter
		./set_shared_user_password.sh

2. fetch the linux kernel. TODO: Do we actually need this?

		sudo mkdir -p /usr/src/linux; cd /usr/src; sudo chown -R <user>.<group> linux
		git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git linux
		cd linux; git checkout checkout linux-4.1.y; cd ~/coreos
		export KERNEL_DIR=/usr/src/linux/

3. setup a board root fs and build the image:

		./setup_board --default --board=amd64-usr --force && ./build_packages && ./build_image dev

## Bare Metal Installation

1. locate, bzip2 and copy the file `coreos_developer_image.bin` to a machine with access to the target drive.
2. write the image to disk

		bunzip2 --stdout coreos_developer_image.bin.bz2 | sudo dd of=<one shot to get this right!>

3. good luck with your first boot!
