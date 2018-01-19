
# Singularity Recipe for CraiG

## CraiG Source

https://github.com/axl-bernal/CraiG


## Build image

The [`vagrant-rpmbuild`](https://github.com/EuPathDB/vagrant-rpmbuild)
virtual machine provisions Singularity suitable for building.

```
$ sudo singularity build craig.simg Singularity 
```

##

$ singularity pull --name craig.simg shub://mheiges/singularity-craig

## Exec

```
$ singularity exec --bind /eupath craig.simg ls  /eupath
```