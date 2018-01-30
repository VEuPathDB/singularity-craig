BootStrap: docker
From: centos:7

%runscript
    exec echo "The runscript is the containers default runtime command!"

%environment
  export PREFIX_INSTALLATION=/opt/craig
  export CRAIG_HOME=$PREFIX_INSTALLATION
  export REGTOOLS_HOME=/opt/regtools
  export PATH=$CRAIG_HOME/bin:$CRAIG_HOME/perl/bin:$CRAIG_HOME/python/bin:$REGTOOLS_HOME/build:$PATH
  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$CRAIG_HOME/lib

%labels
   AUTHOR mheiges@uga.edu

%post
  source /.singularity.d/env/90-environment.sh

  WORKDIR=/scratch
  mkdir $WORKDIR
  
  yum install -y --disableplugin=fastestmirror epel-release && \
  yum clean all
  yum update -y --disableplugin=fastestmirror

  # build tools that will be uninstalled after compile to reduce image
  # size
  yum install -y --disableplugin=fastestmirror \
    autoconf \
    automake \
    boost-regex \
    cmake \
    gcc \
    gcc-c++ \
    git \
    libtool \
    make \
    python2-pip \
    zlib-devel
  yum_txn_id=$(yum history list  |  sed -n '/^---/{n;p}' | awk '{print $1}')

  # packages that persist in final image
  yum install -y --disableplugin=fastestmirror \
    python

  cd $WORKDIR
  git clone https://github.com/sparsehash/sparsehash.git

  cd sparsehash && \
    ./configure && \
    make install

  # regtools
  # https://regtools.readthedocs.io/en/latest/
  git clone https://github.com/griffithlab/regtools $REGTOOLS_HOME
  cd $REGTOOLS_HOME
  mkdir build
  cd build/
  cmake ..
  make

  cd $WORKDIR
  git clone https://github.com/axl-bernal/CraiG.git

  cd CraiG  && pwd && ls -l && \
    ./autogen.sh  && \
    ./configure --prefix="$PREFIX_INSTALLATION" CXXFLAGS="$CXXFLAGS -std=c++11" --enable-opt=no --enable-mpi=no && \
    make && make install && make installcheck

  pip install numpy

  # uninstall build tools
  yum history undo -y $yum_txn_id
  yum clean all && rm -rf /var/cache/yum
  rm -rf $WORKDIR