cd ~/
git clone https://github.com/snapcore/snapd.git
cd snapd
cd ~/snapd
sudo apt build-dep .
cd packaging/fedora
sudo dnf install -y rpmdevtools
sudo dnf install -y $(rpmspec -q --buildrequires snapd.spec)
sudo dnf install glibc-static.i686 glibc-devel.i686
cd ~/snapd
go get ./... && ./get-deps.sh
sudo snap install snapcraft
snapcraft --channel=latest/stable
snap install --dangerous snapd_*.snap
snapcraft remote-build --build-for=armhf,s390x,arm64
sudo unsquashfs -d custom-core core_<rev>.snap
sudo unsquashfs -d custom-snapd snapd-custom.snap
sudo cp ./custom-core/meta meta-core-backup
sudo cp -r ./custom-snapd/* ./custom-core/
sudo rm -r ./custom-core/meta/
sudo cp ./meta-core-backup ./custom-core/
sudo snap pack custom-core
cd ~/snapd
mkdir -p /tmp/build
go build -o /tmp/build/snap ./cmd/snap
cd ~/snapd
mkdir -p /tmp/build
go build -o /tmp/build/snapd ./cmd/snapd
sudo apt-get install gcc-arm-linux-gnueabihf
arm-linux-gnueabihf-gcc -v
:
--with-arch=armv7-a
--with-fpu=vfpv3-d16
--with-float=hard
--with-mode=thumb
cd ~/
git clone https://github.com/seccomp/libseccomp
cd libseccomp
./autogen.sh
./configure --host=arm-linux-gnueabihf --prefix=${HOME}/libseccomp/build
make && make install
export CC=arm-linux-gnueabihf-gcc
export CGO_ENABLED=1
export CGO_LDFLAGS="-L${HOME}/libseccomp/build/lib"
export GOOS=linux
export GOARCH=arm
export GOARM=7
readelf -h /tmp/build/snapd
:
Class:                             ELF32
OS/ABI:                            UNIX - System V
Machine:                           ARM
readelf -A /tmp/build/snap-seccomp
:
File Attributes
  Tag_CPU_name: "7-A"
  Tag_CPU_arch: v7
  Tag_FP_arch: VFPv3-D16
  sudo apt-get install python3-yamlordereddictloader dbus-x11
  ./run-checks
  go test -check.f $testname
  go test -v -check.vv
  $ sudo apt update && sudo apt install -y qemu-kvm autopkgtest
$ curl https://storage.googleapis.com/snapd-spread-tests/spread/spread-amd64.tar.gz | tar -xz -C <target-directory>
$ mkdir -p ~/.spread/qemu
$ cd ~/.spread/qemu
$ autopkgtest-buildvm-ubuntu-cloud -r <release-short-name>
$ mv autopkgtest-<release-short-name>-amd64.img ubuntu-<release-version>-64.img  
$ autopkgtest-buildvm-ubuntu-cloud -r trusty --post-command='sudo apt-get install -y --install-recommends linux-generic-lts-xenial && update-grub'
$ mv autopkgtest-trusty-amd64.img ubuntu-14.04-64.img
$ adt-buildvm-ubuntu-cloud -r xenial
$ mv adt-<release-name>-amd64-cloud.img ubuntu-<release-version>-64.img
$ ./run-spread <spread-args>
$ ./tests/build-test-snapd-snap
$ NO_REBUILD=1 ./run-spread <spread-args>
$ ./run-spread -v qemu:ubuntu-18.04-64
$ ./run-spread -reuse qemu:ubuntu-18.04-64
sudo systemctl stop snapd.service snapd.socket
sudo SNAPD_DEBUG=1 SNAPD_DEBUG_HTTP=3 ./snapd
systemctl --user enable snapd.session-agent.socket
./mkversion.sh
cd cmd/
autoreconf -i -f
./configure --prefix=/usr --libexecdir=/usr/lib/snapd --enable-nvidia-multiarch --with-host-arch-triplet="$(dpkg-architecture -qDEB_HOST_MULTIARCH)"
