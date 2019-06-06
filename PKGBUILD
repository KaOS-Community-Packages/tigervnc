pkgname=tigervnc
pkgver=1.9.0
pkgrel=1
_xorgver=1.20.4
pkgdesc="Suite of VNC servers and clients. Based on the VNC 4 branch of TightVNC."
arch=('x86_64')
url="http://www.tigervnc.org"
license=('GPL')
depends=('fltk' 'pam' 'gnutls' 'libjpeg-turbo' 'libxtst' 'pixman' 'xorg-xkb-utils'
	     'xorg-xauth' 'xkeyboard-config' 'mesa' 'xorg-server-utils'
	     'libgl' 'libgcrypt' 'perl' 'libxdamage' 'libxfont2' 'libdrm' )
makedepends=('cmake' 'nasm' 'xorg-font-util' 'xorg-util-macros' 'bigreqsproto'
            'compositeproto' 'damageproto' 'randrproto' 'resourceproto'
            'scrnsaverproto' 'videoproto' 'xcmiscproto' 'xf86vidmodeproto'
            'xtrans' 'glproto' 'dri2proto' 'dri3proto' 'presentproto'
            'imagemagick' 'openjdk')
conflicts=('tightvnc')
source=($pkgname-$pkgver.tar.gz::https://github.com/TigerVNC/tigervnc/archive/v${pkgver}.tar.gz
	ftp://ftp.freedesktop.org/pub/xorg/individual/xserver/xorg-server-${_xorgver}.tar.bz2
	vncserver.service
	vncviewer.desktop)
sha256sums=('f15ced8500ec56356c3bf271f52e58ed83729118361c7103eab64a618441f740'
            'fe0fd493ebe93bfc56bede382fa204458ff5f636ea54d413a5d1bd58e19166ee'
            '80f8fc7598d05e645ae73bc3371bbdededf07136a9f024ce6ebbfe469335b16e'
            '2ada7da1a926d78f11d2dd8ec376ac5877d2ce2bbb57a99526c13d8fcae6ddd7')

prepare() {
  cd "$srcdir"/${pkgname}-${pkgver}
  cd unix/xserver
  cp -r "$srcdir"/xorg-server-${_xorgver}/* .
  patch -Np1 -i ../xserver120.patch
}

build() {
  cd "$srcdir"/${pkgname}-${pkgver}

  cmake -G "Unix Makefiles" \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DBUILD_JAVA=TRUE
  make

  cd unix/xserver
  autoreconf -fiv
  CFLAGS="$CFLAGS -I/usr/include/libdrm" ./configure --prefix=/usr \
	--disable-static --without-dtrace \
	--disable-xorg --disable-xnest --disable-xvfb --disable-dmx \
	--disable-xwin --disable-xephyr --disable-kdrive --disable-xwayland \
	--disable-config-hal --disable-config-udev --with-pic \
	--disable-unit-tests --disable-devel-docs --disable-selective-werror \
	--disable-dri --enable-dri2 --enable-dri3 --enable-glx
  make
}

package() {
  cd "$srcdir"/${pkgname}-${pkgver}
  make DESTDIR="$pkgdir" install
  cd unix/xserver/hw/vnc
  make DESTDIR="$pkgdir" install
  install -Dm0644 "$srcdir"/${pkgname}-${pkgver}/contrib/systemd/user/vncserver@.service \
    "$pkgdir"/usr/lib/systemd/user/vncserver@.service
  install -Dm0644 "$srcdir"/vncserver.service "$pkgdir"/usr/lib/systemd/system/vncserver.service
  install -Dm0644 "$srcdir"/vncviewer.desktop "$pkgdir"/usr/share/applications/vncviewer.desktop
}
