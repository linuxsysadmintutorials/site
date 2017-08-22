Title: Use overlayfs as the storage driver in docker on CentOS 7.1
Date: 2015-12-12
Status: published

Ensure you have docker installed.

```
# yum install -y docker
```

Set the docker storage driver to overlay by editing
/etc/sysconfig/docker-storage and setting the DOCKER_STORAGE_OPTIONS
variable.

```
DOCKER_STORAGE_OPTIONS= --storage-driver=overlay
```

Restart docker.

```
# systemctl restart docker
```

Confirm that the storage driver is set to overlay and the backing
filesystem is extfs.

```
# docker info
...

Storage Driver: overlay
 Backing Filesystem: extfs
```
