# nghttp2_libs
Precompiled libraries for nghttp2

## Build instructions
Instructions are for building under Ubuntu 18.04 and Clang-7 compiler. It's recommended to use Docker container.
nghttp2 depends on Boost so it's required to install Boost libraries as a first step.
nghttp2 also depends on OpenSSL, but it's required to use BoringSSL instead, therefore BoringSSL is build as a second step and then nghttp2 is build with BoringSSL prebuild libraries.

### Install Boost libraries
Download Boost libraries source code (used here: v1.67.0)

* download source archive here: https://www.boost.org/users/history/
* instructions on how to build Boost libraries: https://www.boost.org/doc/libs/1_68_0/more/getting_started/unix-variants.html
* it's recommended to build with Boost.Build like this:

1. Go to directory tools/build
2. Run bootstrap with Clang:
  ```
		./bootstrap.sh --with-toolset=clang-7
  ```
3. In case Clang won't be recognized since bootstrap removes version suffix, do the following:
  ```
		ln -s /usr/bin/clang-7 /usr/bin/clang
		ln -s /usr/bin/clang++-7 /usr/bin/clang++
  ```
4. Install Boost.Build with (you can choose some other dir prefix but make sure for this dir to be visible in PATH env variable):
 ```
		./b2 install --prefix=/usr
 ```
 
* build from sources with Boost.Build (b2 tool) like this:

1. Create build directory or skip this step if boost dir is writable (build dir will be automatically created)
2. Run stage build (this will only build libs, not install them):
  ```
		b2 --build-dir=build toolset=clang-7 stage
  ```
3. Libraries will be installed in stage/lib directory
4. Install all libraries and include header files to desired prefix (b2 --help for more info):
  ```
		b2 --build-dir=build --prefix=/usr toolset=clang-7 install
  ```

### Install BoringSSL
Download BoringSSL source code (used here: commit 752e2c91af5671332649a1b1fbe52dfb4bfef0a8)

* building BoringSSL will require to install CMake and Golang over apt-get
* use src directory for building from sources, instructions here: https://github.com/google/boringssl/blob/master/BUILDING.md
* run the following:

1. ```mkdir build```
2. ```cd build```
3. ```CC=clang CXX=clang++ cmake -DBUILD_SHARED_LIBS=1 ..```
4. ```make```
5. create lib directory in src directory (include is already there)
6. create symlinks for static and shared libraries in lib directory:
  ```
		ln -s ../build/ssl/libssl.a .
		ln -s ../build/ssl/libssl.so .
		ln -s ../build/crypto/libcrypto.a .
		ln -s ../build/crypto/libcrypto.so .
  ```

### Build nghttp2 source code
Download nghttp2 sources from github (used here: commit 15ff52f9fb26e0c97adaef4f2a6ea1cabec814ff)

* build instructions here: https://github.com/nghttp2/nghttp2
* additionally, we need to install nghttp2_asio library: https://nghttp2.org/documentation/libnghttp2_asio.html
* first make sure to install all required packages listed here:
```
	apt-get install g++ make binutils autoconf automake autotools-dev libtool pkg-config \
	  zlib1g-dev libcunit1-dev libssl-dev libxml2-dev libev-dev libevent-dev libjansson-dev \
	  libc-ares-dev libjemalloc-dev libsystemd-dev \
	  cython python3-dev python-setuptools
```
* build steps:

1. ```git submodule update --init```
2. ```autoreconf -i```
3. ```automake```
4. ```autoconf```
5. Run configure with Clang and pre-build boringssl libs and include files (enable boost::asio support and don't build examples, Python binding or executables):
  ```
		CC="clang" \
		CXX="clang++" \
		OPENSSL_LIBS="-L/workspace/boringssl/src/lib -lcrypto -lssl" \
		OPENSSL_CFLAGS="-I/workspace/boringssl/src/include" \
		LIBEVENT_OPENSSL_LIBS="-L/workspace/boringssl/src/lib -lcrypto -lssl" \
		LIBEVENT_OPENSSL_CFLAGS="-I/workspace/boringssl/src/include" \
		./configure \
		--prefix=/usr \
		--enable-asio-lib \
		--enable-examples=no \
		--enable-app=no \
		--disable-python-bindings
  ```
6. ```make```
7. ```make install``` (just to see what we need to copy)
8. copy header files from ```/usr/include/nghttp2```
9. copy libnghttp2 libraries from ```/usr/lib/```
