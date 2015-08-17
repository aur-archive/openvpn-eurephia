# Maintainer: Konstantin Shabanov <kes.eclipse@gmail.com>
# Contributor: Thomas Bächler <thomas@archlinux.org>

pkgname=openvpn-eurephia
pkgver=2.1.3
pkgrel=2
pkgdesc="An easy-to-use, robust, and highly configurable VPN (Virtual Private Network) patched for using with eurephia"
arch=(i686 x86_64)
url="http://openvpn.net/index.php/open-source.html"
depends=('openssl' 'lzo2')
license=('GPL')
conflicts=('openvpn')
provides=('openvpn')
backup=(usr/share/openvpn/easy-rsa/vars
        usr/share/openvpn/easy-rsa/openssl.cnf
        etc/conf.d/openvpn-tapdev)
source=(http://openvpn.net/release/openvpn-$pkgver.tar.gz
	http://downloads.sourceforge.net/project/eurephia/OpenVPN/OpenVPN%20eurephia%20patches/openvpn-2.1.3_eurephia.patch
        openvpn.rc
        openvpn-tapdev.rc
        openvpn-tapdev.conf)

md5sums=('7486d3e270ba4b033e311d3e022a0ad7'
         'c11bcf5dbde008bd472d88599e90da61'
         'a3809b9727f0c2af2d0770f5c7442db2'
         'd2c48e970088d679dd3c2afd914ff731'
         '722f483c9e3ce2ec66d3301aaf7cf3d5')

build() {
  cd $srcdir/openvpn-$pkgver
  # Build and install openvpn
  patch -Np1 -i ../openvpn-2.1.3_eurephia.patch || return 1
  CFLAGS="$CFLAGS -DPLUGIN_LIBDIR=\\\"/usr/lib/openvpn\\\"" ./configure --prefix=/usr --enable-password-save --mandir=/usr/share/man || return 1
  make || return 1
  make DESTDIR=$pkgdir install || return 1
  install -d -m755 $pkgdir/etc/openvpn
  # Install examples
  install -d -m755 $pkgdir/usr/share/openvpn
  cp -r sample-config-files $pkgdir/usr/share/openvpn/examples || return 1
  find $pkgdir/usr/share/openvpn -type f -exec chmod 644 {} \;
  find $pkgdir/usr/share/openvpn -type d -exec chmod 755 {} \;
  # Install license
  install -D -m644 COPYING $pkgdir/usr/share/licenses/openvpn/COPYING || return 1
  # Build and install plugins
  for plug in auth-pam down-root; do
    cd $srcdir/openvpn-$pkgver/plugin/$plug
    make || return 1 
    install -D -m755 openvpn-$plug.so $pkgdir/usr/lib/openvpn/openvpn-$plug.so || return 1
  done
  # Install easy-rsa
  cd $srcdir/openvpn-$pkgver
  make -C easy-rsa/2.0 install DESTDIR=$pkgdir PREFIX=usr/share/openvpn/easy-rsa || return 1
  # Install rc scripts
  install -D -m755 $srcdir/openvpn.rc $pkgdir/etc/rc.d/openvpn || return 1
  install -D -m755 $srcdir/openvpn-tapdev.rc $pkgdir/etc/rc.d/openvpn-tapdev || return 1
  install -D -m644 $srcdir/openvpn-tapdev.conf $pkgdir/etc/conf.d/openvpn-tapdev || return 1
}
