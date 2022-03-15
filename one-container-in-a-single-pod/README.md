# A single container in a pod

## Define hugepage volume with `emptyDir`

```yaml
  volumes:
  - name: hugepage
    emptyDir:
      medium: HugePages
```

## Mount hugepage on a container

```yaml
  containers:
  ...
    volumeMounts:
    - mountPath: /hugepages
      name: hugepage
```
