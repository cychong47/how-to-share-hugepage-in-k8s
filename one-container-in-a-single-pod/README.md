# A single container in a pod

## Define hugepage volume with `emptyDir`

```yaml
  volumes:
  - name: hugepage
    emptyDir:
      medium: HugePages
```

## DPDK runtime config

Nothing to do as `/var/run` is already exist in container filesystem

## Mount hugepage on a container

```yaml
  containers:
  ...
    volumeMounts:
    - mountPath: /hugepages
      name: hugepage
```
![](2022-03-14-dpdk-hugepage-1c-1p-1.png)  
![](2022-03-14-dpdk-hugepage-1c-1p-2.png)  
![](2022-03-14-dpdk-hugepage-1c-1p-3.png)  
![](2022-03-14-dpdk-hugepage-1c-1p-4.png)  
