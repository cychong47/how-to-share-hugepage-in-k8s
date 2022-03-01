# how-to-share-hugepage-in-k8s : WIP
How to share hugepage in kubernetes - between containers and pods


## one-container-in-a-single-pod:

- Run one container a single pod
- hugepage is mounted as `/hugepages`

## two-containers-in-a-single-pod

- Run twon containers in a single pod
- hugetblfs is mounted at `/hugepages`
  - This is where hugepage file is created
  - This can be backed with 'emptyDir'
- one addition volume should be mounted at '/var/run/dpdk`
  - This path is used to store dpdk config files
  - This also can be backed with `emptyDir`

## one-container-in-each-pod

- Run twon containers in two single pods(one container in a pod)
- hugetblfs is mounted at `/hugepages`
  - This is where hugepage file is created
  - This location should be backed from hostPath to be shared between pods
- one addition volume should be mounted at '/var/run/dpdk`
  - This path is used to store dpdk config files
  - This also shoule be from `hostPath` to be shared between pods



