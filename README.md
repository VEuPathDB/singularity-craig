
# Singularity Recipe for CraiG

## CraiG Source

https://github.com/axl-bernal/CraiG


## Build image

The [`vagrant-rpmbuild`](https://github.com/EuPathDB/vagrant-rpmbuild)
virtual machine provisions Singularity suitable for building.

Typically you should remove any existing image so you get clean build.

```
$ rm -f craig.simg
$ sudo singularity build craig.simg Singularity 
```

##

$ singularity pull --name craig.simg shub://mheiges/singularity-craig

## Exec

```
$ singularity exec --bind /eupath craig.simg ls  /eupath
```