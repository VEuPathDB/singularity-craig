BootStrap: docker
From: centos:7

%runscript
    exec echo "The runscript is the containers default runtime command!"

%environment
  export PREFIX_INSTALLATION=/opt/craig
  export CRAIG_HOME=$PREFIX_INSTALLATION
  export PATH=$CRAIG_HOME/bin:$CRAIG_HOME/perl/bin:$CRAIG_HOME/python/bin:$PATH
  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$CRAIG_HOME/lib

%labels
   AUTHOR mheiges@uga.edu

%post
  source /.singularity.d/env/90-environment.sh
  WORKDIR=/tmp

  yum install -y --disableplugin=fastestmirror epel-release && \
    yum clean all && \
    yum update -y --disableplugin=fastestmirror && \
    yum install -y --disableplugin=fastestmirror \
      python git make gcc gcc-c++ autoconf automake libtool \
      doxygen boost-regex python2-pip && \
    yum clean all && rm -rf /var/cache/yum

  rm -rf $WORKDIR/CraiG
  rm -rf $WORKDIR/sparsehash

  cd $WORKDIR
  git clone https://github.com/sparsehash/sparsehash.git

  cd sparsehash && \
    ./configure && \
    make install

  cd $WORKDIR
  git clone https://github.com/axl-bernal/CraiG.git

  cd CraiG  && pwd && ls -l && \
    ./autogen.sh  && \
    ./configure --prefix="$PREFIX_INSTALLATION" CXXFLAGS="$CXXFLAGS -std=c++11" --enable-opt=no --enable-mpi=no && \
    make && make install && make installcheck

  pip install numpy

  rm -rf $WORKDIR/CraiG
  rm -rf $WORKDIR/sparsehash
