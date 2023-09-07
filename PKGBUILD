# Maintainer: AndyRTR <andyrtr@archlinux.org>
# Contributor: Mantas Mikulėnas <grawity@gmail.com>

pkgname=gssproxy
pkgver=0.9.1
pkgrel=2.1
pkgdesc="GSSAPI Proxy"
arch=(x86_64)
url="https://github.com/gssapi/gssproxy"
license=('custom: MIT')
depends=('libverto-module-base' 'popt' 'ding-libs' 'systemd-libs')
makedepends=('libxslt' 'systemd' 'docbook-xsl' 'doxygen'  'po4a')
options=('emptydirs' 'makeflags')
backup=(etc/gssproxy/{gssproxy,24-nfs-server,80-httpd,99-network-fs-clients}.conf
        etc/gss/mech.d/proxymech.conf)
source=(https://github.com/gssapi/gssproxy/releases/download/v$pkgver/$pkgname-$pkgver.tar.gz
        systemd-add-StateDirectory-to-gssproxy.service.patch) # Holo: create /var/lib/gssproxy/* if missing - https://github.com/gssapi/gssproxy/pull/79
# https://github.com/gssapi/gssproxy/releases/download/v0.9.1/gssproxy-0.9.1.tar.gz.sha512sum.txt
sha512sums=('767c4e73d5240a43a0586e836e7dc199b8baf0adbc5a1a2cbb6ffc6f0a7ca574a4fc541ca474ef696d78956724eb24c465760f680e7170f8d77d056513b6226b'
            'f292f2a6f99f35b499ce625705a595eed4f76ddcf26fc0049b7892264ad646085ada1512a6cbccb7c4e578e8bdc33e5ad41a3c02ed96992a84b2c44935480709')

prepare() {
  cd "$srcdir/gssproxy-$pkgver"
  # delete unneeded service dependency
  sed -i "/# GSSPROXY will not be started until syslog is/d" systemd/gssproxy.service.in
  sed -i "s/syslog.target //" systemd/gssproxy.service.in
  
  local src
  for src in "${source[@]}"; do
    src="${src%%::*}"
    src="${src##*/}"
    [[ $src = *.patch ]] || continue
    echo "Applying patch $src..."
    patch -Np1 < "../$src"
  done

  autoreconf -vfi
}

build() {
  cd gssproxy-$pkgver
  ./configure --prefix=/usr \
    --sysconfdir=/etc  \
    --with-pubconf-path=/etc/gssproxy \
    --sbindir=/usr/bin \
    --localstatedir=/var \
    --without-selinux \
    --with-initscript=systemd \
    --with-gpp-default-behavior=REMOTE_FIRST
  sed -i -e 's/ -shared / -Wl,-O1,--as-needed\0/g' libtool
  make
}

check() {
  cd "$pkgname"-$pkgver
  make test_proxymech
}


package() {
  cd gssproxy-$pkgver
  make DESTDIR=$pkgdir install
  
  # cleanup empty directories
  rm -rf "$pkgdir"/usr/include
  rm -rf "$pkgdir"/usr/share/doc

  # install default config files
  install -m644 examples/gssproxy.conf "$pkgdir"/etc/gssproxy/gssproxy.conf
  # nfs services
  install -m644 examples/24-nfs-server.conf "$pkgdir"/etc/gssproxy/24-nfs-server.conf
  install -m644 examples/99-network-fs-clients.conf "$pkgdir"/etc/gssproxy/99-network-fs-clients.conf
  # httpd service / use Arch UID/GID http/33 (by pkg filesystem)
  install -m644 examples/80-httpd.conf "$pkgdir"/etc/gssproxy/80-httpd.conf
  sed -i -e "s:euid = apache:euid = http:" "$pkgdir"/etc/gssproxy/80-httpd.conf

#  install -Dm644 examples/mech  "$pkgdir"/etc/gss/mech.d/gssproxy.conf
  
  # FS#51574
  install -m700 -d ${pkgdir}/var/lib/gssproxy/rcache

  install -m755 -d ${pkgdir}/usr/share/licenses/$pkgname
  install -m644 COPYING ${pkgdir}/usr/share/licenses/$pkgname/
}
