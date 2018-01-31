BootStrap: docker
From: centos:7

%runscript
    exec echo "The runscript is the containers default runtime command!"

%environment
  export PREFIX_INSTALLATION=/opt/craig
  export CRAIG_HOME=$PREFIX_INSTALLATION
  export SAMTOOLS_HOME=/opt/samtools
  export REGTOOLS_HOME=/opt/regtools
  export PATH=$CRAIG_HOME/bin:$CRAIG_HOME/perl/bin:$CRAIG_HOME/python/bin:$REGTOOLS_HOME/build:$SAMTOOLS_HOME/bin:$PATH
  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$CRAIG_HOME/lib

%labels
   AUTHOR mheiges@uga.edu

%post
  source /.singularity.d/env/90-environment.sh

  WORKDIR=/tmp
  
  yum install -y --disableplugin=fastestmirror epel-release && \
  yum clean all
  yum update -y --disableplugin=fastestmirror

  # build tools that will be uninstalled after compile to reduce image
  # size
  yum install -y --disableplugin=fastestmirror \
    autoconf \
    automake \
    boost-regex \
    bzip2 \
    bzip2-devel \
    cmake \
    gcc \
    gcc-c++ \
    git \
    libtool \
    make \
    ncurses-devel \
    python2-pip \
    xz-devel \
    zlib-devel
  yum_txn_id=$(yum history list  |  sed -n '/^---/{n;p}' | awk '{print $1}')

  cd $WORKDIR
  if [[ -e sparsehash ]]; then
    pushd sparsehash
    git reset -- .
    git clean -f -x -d -- .
    git pull
    popd
  else
    git clone https://github.com/sparsehash/sparsehash.git
  fi

  cd sparsehash && \
    ./configure && \
    make install

  # regtools
  # https://regtools.readthedocs.io/en/latest/
  if [[ -e $REGTOOLS_HOME ]]; then
    pushd $REGTOOLS_HOME
    git reset -- .
    git clean -f -x -d -- .
    git pull
    popd
  else
    git clone https://github.com/griffithlab/regtools $REGTOOLS_HOME
  fi
  cd $REGTOOLS_HOME
  mkdir build
  cd build/
  cmake ..
  make

  cd $WORKDIR
  if [[ -e CraiG ]]; then
    pushd CraiG
    git reset -- .
    git clean -f -x -d -- .
    git pull
    popd
  else
    git clone https://github.com/mheiges/CraiG.git
    git checkout ebrc
  fi

  cd CraiG  && \
    ./autogen.sh  && \
    ./configure --prefix="$PREFIX_INSTALLATION" CXXFLAGS="$CXXFLAGS -std=c++11" --enable-opt=no --enable-mpi=no && \
    make && make install && make installcheck

  cd $WORKDIR
  curl -LO https://gigenet.dl.sourceforge.net/project/samtools/samtools/1.7/samtools-1.7.tar.bz2
  rm -rf samtools-1.7
  tar xf samtools-1.7.tar.bz2
  pushd samtools-1.7
  ./configure --prefix=/opt/samtools
  make
  make install
  popd

  pip install numpy

  # uninstall build tools
  #yum history undo -y $yum_txn_id

  # packages that persist in final image
  yum install -y --disableplugin=fastestmirror \
    python \
    perl

  yum clean all && rm -rf /var/cache/yum
  
  if [[ "$WORKDIR" != "/tmp" ]]; then
    rm -rf $WORKDIR
  fi