# One container in each pod

## Define hugepage volume with `hostPath`

```yaml
  - name: dev-hp
    hostPath:
      path: /dev/hugepages
```

## Also define a volume with `hostPath` to store DPDK runtime config files

```yaml
  - name: dpdk-config
    hostPath:
      path: /home/cychong/tmp/dpdk_config
```

## Mount volumes on each container

```yaml
  containers:
  ...
    volumeMounts:
    - mountPath: /hugepages
      name: hugepage
    - mountPath: /var/run/dpdk
      name: dpdk-config
```
![](2022-03-14-dpdk-hugepage-1c-2p-1.png)  
![](2022-03-14-dpdk-hugepage-1c-2p-2.png)  
