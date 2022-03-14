# How to share hugepage between containers or pods in kubernetes

## Multi-process example of DPDK application

To have multiple DPDK applicatios which shares hugepage, they should use the same DPDK runtime config files and hugepage map files. Especially, hugepage map files should be created where hugetlbfs is mounted.

The primary process which initialize the DPDK EAL is responsible for them. And the secondary processes use the same files.

Please refer to [Multi-process Sample Application](https://doc.dpdk.org/guides/sample_app_ug/multi_process.html) of [dpdk.org](https://dpdk.org)

## How to claim hugepage from the pod

To claim hugepage from the Pod, `emptyDir` `Hugepages` volume should be declared.

```yaml
  volumes:
  - name: hugepage
    emptyDir:
      medium: HugePages
```

And `resources` of each container should declare required amount of hugepages
```yaml
    resources:
      limits:
        hugepages-2Mi: 10Mi
```


## one-container-in-a-single-pod

- One container in a single pod
- Mount `hugetlbfs` where hugepage map files of DPDK applications are located
- No need to mount `/var/run/dpdk` as `/var' is present already.

```yaml
  volumes:
  - name: hugepage
    emptyDir:
      medium: HugePages
```

```yaml
    volumeMounts:
    - mountPath: /hugepages
      name: hugepage
```

## two-containers-in-a-single-pod

### `hugetblfs`
- This is where hugepage map files of DPDK aplication are located
- This is backed with 'emptyDir' volume type and shared betweens the containers in the same pod

```yaml
  volumes:
  - name: hugepage
    emptyDir:
      medium: HugePages
```

```yaml
    volumeMounts:
    - mountPath: /hugepages
      name: hugepage
```

### DPDK Runtime config files

- The path where these files are located is fixed - `/var/run/dpdk`
- `emptyDir` is used to create a volume where both containers can acsess

```yaml
  volumes:
  - name: dpdk-config
    emptyDir:
```

```yaml
    volumeMounts:
    - mountPath: /var/run/dpdk
      name: dpdk-config
```

## one-container-in-each-pod

Two pods has a single container on each.

### `hugetblfs`
- This location should be backed with `hostPath` where `hugetlbfs` is mounted in node
- No need to create hugepage volume with `emptyDir`

From node, 
```
$ grep hugetlbfs /proc/mounts
hugetlbfs /dev/hugepages hugetlbfs rw,relatime,pagesize=2M 0 0
```

```yaml
  volumes:
  - name: dev-hp
    hostPath:
      path: /dev/hugepages
```

```yaml
    volumeMounts:
    - mountPath: /dev/hugepages
      name: dev-hp
```


### DPDK Runtime config files
  - This also shoule be mounted from `hostPath`



## `/dev/hugepages' or somewhere else(ex, `/mnt/huge`)

`/dev/hugepages` is mount point of **default hugepage size**
If non-default size of hugepage is required, create another mount point with `pagesize` option
```
mkdir /mnt/huge
mount -t hugetlbfs pagesize=1GB /mnt/huge
```

Please see the details from DPDK `sys_reqs.rst`

```
Using Hugepages with the DPDK
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If secondary process support is not required, DPDK is able to use hugepages
without any configuration by using "in-memory" mode.
Please see :doc:`linux_eal_parameters` for more details.

If secondary process support is required,
mount points for hugepages need to be created.
On modern Linux distributions, a default mount point for hugepages
is provided by the system and is located at ``/dev/hugepages``.
This mount point will use the default hugepage size
set by the kernel parameters as described above.

However, in order to use hugepage sizes other than the default, it is necessary
to manually create mount points for those hugepage sizes (e.g. 1GB pages).

To make the hugepages of size 1GB available for DPDK use,
following steps must be performed::

    mkdir /mnt/huge
    mount -t hugetlbfs pagesize=1GB /mnt/huge

The mount point can be made permanent across reboots, by adding the following line to the ``/etc/fstab`` file::

    nodev /mnt/huge hugetlbfs pagesize=1GB 0 0
```
