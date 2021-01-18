pkgname=tigervnc
pkgver=1.11.0
pkgrel=1
_xorgver=1.20.10
pkgdesc="Suite of VNC servers and clients. Based on the VNC 4 branch of TightVNC."
arch=('x86_64')
url="http://www.tigervnc.org"
license=('GPL')
depends=('fltk' 'pam' 'gnutls' 'libjpeg-turbo' 'libxtst' 'libxfont2' 'pixman'
	 'xorg-xauth' 'xkeyboard-config'
	 'libgl' 'libgcrypt' 'perl' 'libxdamage' 'xorg-xkb-utils' 'xorg-server-utils')
makedepends=('cmake' 'nasm' 'xorg-font-util' 'xorg-util-macros' 'bigreqsproto'
	     'compositeproto' 'damageproto' 'randrproto' 'resourceproto'
	     'scrnsaverproto' 'videoproto' 'xcmiscproto' 'xf86vidmodeproto'
	     'xtrans' 'glproto' 'dri2proto' 'dri3proto' 'presentproto'
	     'mesa' 'imagemagick')
optdepends=('mesa: for OpenGL functionality in Xvnc')
source=("$pkgname-$pkgver.tar.gz::https://github.com/TigerVNC/tigervnc/archive/v${pkgver}.tar.gz"
	"ftp://ftp.freedesktop.org/pub/xorg/individual/xserver/xorg-server-${_xorgver}.tar.bz2"
)
md5sums=('07f5e217f288c515effb083896e65054'
         '8cf8bd1f33e3736bc8dd279b20a32399'
         )

prepare() {
  cd ${srcdir}/${pkgname}-${pkgver}
  cd unix/xserver
  cp -r ${srcdir}/xorg-server-${_xorgver}/* .
  tmpstr=`echo ${_xorgver:0:4} | sed 's/\.//g'`
  patch -p1 < ../xserver$tmpstr.patch
}

build() {
    cd ${srcdir}/${pkgname}-${pkgver}
  cmake -G "Unix Makefiles" -DCMAKE_INSTALL_PREFIX=/usr
  make -j${nproc}
  cd unix/xserver
  autoreconf -fiv
  export CFLAGS="$CFLAGS -I/usr/include/libdrm"
  
  ./configure --prefix=/usr \
	--disable-static --without-dtrace \
	--disable-xorg --disable-xnest --disable-xvfb --disable-dmx \
	--disable-xwin --disable-xephyr --disable-kdrive --disable-xwayland \
	--disable-config-hal --disable-config-udev --with-pic \
	--disable-unit-tests --disable-devel-docs --disable-selective-werror \
	--disable-dri --enable-dri2 --enable-dri3 --enable-glx --enable-glx-tls
  make -j${nproc}
}

package() {
  cd ${srcdir}/${pkgname}-${pkgver}
  make DESTDIR=${pkgdir} install
  cd unix/xserver/hw/vnc
  make DESTDIR=${pkgdir} install
}
