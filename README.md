# kcloud-setup

## 1. Setup

1. Login to K-pre/post cloud via ssh.

```sh
$ ssh ubuntu@10.9.x.x
```

2. Create a working directory under home directory.

```sh
$ cd
$ mkdir Work
```

3. Create a github directory under the working directory.

```sh
$ cd Work
$ mkdir GitHub
$ cd GitHub
```

## 2. Required Package Installation

1. Move to the github directory.

```sh
$ cd ~/Work/GitHub
```

2. Download package installer for KVS-based in-situ vis programs.

```sh
$ git clone https://github.com/naohisas/kcloud-setup.git
$ cd kcloud-setup
```

3. Install packages using the installer.

```sh
$ ./setup_packages.sh
* type 'Y' for each confirmation
```

## 3. OSMesa Installation

1. Create install target directories for OSMesa and LLVM under "~/local".

```sh
$ cd
$ mkdir local
$ cd local
$ mkdir osmesa_swr
$ mkdir llvm
```

2. Move to the github directory.

```sh
$ cd ~/Work/GitHub
```

3. Download OSMesa installer.

```sh
$ git clone https://github.com/devernay/osmesa-install.git
$ cd osmesa-install
```

4. Modify the install-script 'osmesa-install.sh' as follows.

```
a) Specify the install target directory of OSMesa.
osmesaprefix="${OSMESA_PREFIX:-${HOME}/local/osmesa_swr}

b) Specify the driver of 4. (1:swrast, 2:softpipe, 3:llvmpipe, 4:swr)
osmesadriver=${OSMESA_DRIVER:-4}

c) Disable 'mangled'. (1:compile mangled OSMesa or 0:not)
mangled=0

d) Specify the install target directory for LLVM.
llvmprefix="${LLVM_PREFIX:-${HOME}/local/llvm}

e) Enable 'buildllvm'. (1:download and build LLVM or 0:not)
buildllvm="${LLVM_BUILD:-1}
```

~~f) Modify the URLs for downloading OSMesa at line 376 as follows.
curl $curlopts -O "ftp://ftp.freedesktop.org/pub/mesa/mesa-${mesaversion}.tar.gz" || curl $curlopts -O "ftp://ftp.freedesktop.org/pub/mesa/${mesaversion}/mesa-${mesaversion}.tar.gz" || curl $curlopts -O "ftp://ftp.freedesktop.org/pub/mesa/older-versions/`echo ${mesaversion} | cut -d '.' -f 1`.x/mesa-${mesaversion}.tar.gz"~~

5. Compile and install OSMesa.

```sh
$ mkdir build
$ cd build
$ ../osmesa-install.sh
```

## 4. KVS Installation

1. Create install target directories for two types of KVS under "~/local".

```sh
$ cd ~/local
$ mkdir kvs.osmesa
$ mkdir kvs.egl
```

2. Set environment variables reuqired to compile KVS.

```
export KVS_DIR=${HOME}/local/kvs
export PATH=${PATH}:${KVS_DIR}/bin
export KVS_OSMESA_DIR=${HOME}/local/osmesa_swr
export KVS_OSMESA_LINK_LIBRARY="-lOSMesa32 -lz `~/local/llvm/bin/llvm-config --libs` `~/local/llvm/bin/llvm-config --ldflags` -lrt -ldl -lpthread -lm"
```

3. Move to the github directory.

```sh
$ cd ~/Work/GitHub
```

4. Download KVS.

```sh
$ git clone https://github.com/naohisas/KVS.git
$ cd KVS
```

5. Modify kvs.conf for compiling KVS with SupportOSMesa.

```
KVS_ENABLE_OPENGL     = 1
KVS_ENABLE_GLU        = 0
KVS_ENABLE_GLEW       = 0
KVS_ENABLE_OPENMP     = 1
KVS_ENABLE_DEPRECATED = 0

KVS_SUPPORT_CUDA      = 0
KVS_SUPPORT_GLUT      = 0
KVS_SUPPORT_OPENCV    = 0
KVS_SUPPORT_QT        = 0
KVS_SUPPORT_PYTHON    = 1
KVS_SUPPORT_EGL       = 0
KVS_SUPPORT_OSMESA    = 1
```

6. Compile and install the KVS with SupportOSMesa.

```sh
$ KVS_DIR=~/local/kvs.osmesa make
$ KVS_DIR=~/local/kvs.osmesa make install
```

7. Modify kvs.conf for compiling KVS with SupportEGL.

```
KVS_ENABLE_OPENGL     = 1
KVS_ENABLE_GLU        = 0
KVS_ENABLE_GLEW       = 0
KVS_ENABLE_OPENMP     = 1
KVS_ENABLE_DEPRECATED = 0

KVS_SUPPORT_CUDA      = 0
KVS_SUPPORT_GLUT      = 0
KVS_SUPPORT_OPENCV    = 0
KVS_SUPPORT_QT        = 0
KVS_SUPPORT_PYTHON    = 1
KVS_SUPPORT_EGL       = 1
KVS_SUPPORT_OSMESA    = 0
```

8. Compile and install the KVS with SupportEGL.

```sh
$ KVS_DIR=~/local/kvs.egl make rebuild
$ KVS_DIR=~/local/kvs.egl make install
```

9. Create KVS directory as a symbolic link.

```
$ cd ~/local

a) Link kvs to kvs.osmesa if you want to use OSMesa-based KVS.
$ ln -s kvs.osmesa kvs

b) Link kvs to kvs.egl if you want to use EGL-based KVS.
$ ln -s kvs.egl kvs
```
