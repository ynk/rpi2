# Docker on Raspberry Pi 2 with ArchLinux

### Install

- Check config requirements by running docker script on the raspberry `curl -L https://raw.githubusercontent.com/docker/docker/master/contrib/check-config.sh | CONFIG=.config /bin/bash`

- Download the latest docker binary from [this repo](https://github.com/umiddelb/armhf/tree/master/bin/arm7), add `chmod +x docker` and `mv docker /usr/bin`. 

- Check the kernel version with `cat /proc/version`. Must be over 3.10 in order to run docker. If not, `pacman -Syu && reboot`

- Install dependencies `pacman -S bridge-utils btrfs-progs lxc` (the others should be shipped with archlinux: device-mapper, iproute2, sqlite, systemd). As installing `lxc` will install python, it may complain about already having it, so just `rm /usr/bin/python`.

- Try to run docker in daemon mode `docker -d`. Should work :)

### Troubleshooting

- Check cgroups hierarchy with `cat /proc/cgroups`. Must not be empty and rather look something similar to [this](https://github.com/tianon/cgroupfs-mount/blob/master/cgroupfs-mount#L44-L52).

- Make sure the bridge module is loaded by `modeprobe bridge`. **If not, `reboot` should do it.**

### Resources

- prebuild docker binary for arm7 found on [umiddelb's armhf repo](https://github.com/umiddelb/armhf/wiki/Installing,-running,-using-docker-on-armhf-(ARMv7)-devices#see-whats-missing)
- `check-config` script discovered from [umiddelb's wiki](https://github.com/umiddelb/armhf/wiki/Installing,-running,-using-docker-on-armhf-(ARMv7)-devices#see-whats-missing)
- docker general dependencies and `cgroups` structure found on [docker's generic installation documentation](http://docs.docker.com/installation/binaries/)
- archlinux dependencies found on [docker's installation documentation for archlinux](http://docs.docker.com/installation/archlinux/)
- `modeprobe bridge` troubleshooting found on [docker's issue tracker](https://github.com/docker/docker/issues/6853) for archlinux
