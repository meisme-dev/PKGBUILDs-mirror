# Maintainer: AndyRTR <andyrtr@archlinux.org>
# Maintainer: Tobias Powalowski <tpowa@archlinux.org>
# Contributor: John Proctor <jproctor@prium.net>
# Contributor: dibblethewrecker <dibblethewrecker.at.jiwe.org>
# Contributor: abelstr <abel@pinklf.eu>
# Contributor: Marco Lima <cipparello gmail com>

pkgbase=nfs-utils
pkgname=('nfs-utils' 'nfsidmap')
pkgver=2.6.3
pkgrel=1.1
arch=('x86_64')
url='http://nfs.sourceforge.net'
makedepends=('libevent' 'sqlite' 'rpcsvc-proto' 'device-mapper')
# http://git.linux-nfs.org/?p=steved/nfs-utils.git;a=summary
source=(https://www.kernel.org/pub/linux/utils/${pkgname}/${pkgver}/${pkgname}-${pkgver}.tar.{xz,sign}
        tmpfiles.patch # Holo: systemd-tmpfiles configuration - https://marc.info/?l=linux-nfs&m=168924387828205&w=2
        exports
        sysusers.d)
# https://mirrors.edge.kernel.org/pub/linux/utils/nfs-utils/2.6.3/sha256sums.asc
sha256sums=('38d89e853a71d3c560ff026af3d969d75e24f782ff68324e76261fe0344459e1'
            'SKIP'
            '1c1b087c090fadc48ef2816158f9217cb04fa4d6780927d6bc8481157129d2dc'
            'b8238b74179f7e1626db2b637671ddc17288a1c5b7692954ae6d2fbb1be3788d'
            '8e1b6aa59cf5539b9da13cfec217b3ca7b6c32e08df3b0a204901ed2891b2ded')
validpgpkeys=('E1B71E339E20A10A676F7CB69AFB1D681A125177') # Steve Dickson

prepare() {
  cd "${pkgbase}"-${pkgver}

  local src
  for src in "${source[@]}"; do
    src="${src%%::*}"
    src="${src##*/}"
    [[ $src = *.patch ]] || continue
    echo "Applying patch $src..."
    patch -Np1 < "../$src"
  done
 
  # fix hardcoded sbin/libexec path to our needs
  sed -i "s|sbindir = /sbin|sbindir = /usr/bin|g" utils/*/Makefile.am
  sed -i "s|sbin|bin|" utils/nfsidmap/id_resolver.conf
  sed -i "s|libexec|bin|" tools/nfsrahead/99-nfs.rules
  autoreconf -vfi
}

build() {
  cd "${pkgbase}"-${pkgver}
  ./configure --prefix=/usr \
    --sbindir=/usr/bin \
    --sysconfdir=/etc \
    --libexecdir=/usr/bin \
    --with-statedir=/var/lib/nfs \
    --with-statdpath=/var/lib/nfs/statd \
    --with-statduser=rpcuser \
    --with-start-statd=/usr/bin/start-statd \
    --enable-nfsv4server \
    --enable-gss \
    --without-tcp-wrappers \
    --enable-ipv6 \
    --enable-libmount-mount \
    --enable-mountconfig \
    --with-systemd
  sed -i -e 's/ -shared / -Wl,-O1,--as-needed\0/g' libtool
  make
}

check() {
  cd "${pkgbase}"-${pkgver}
  #make -k check || /bin/true
  make check
}

package_nfs-utils() {

  pkgdesc="Support programs for Network File Systems"
  license=('GPL2')

  backup=(etc/{exports,nfs.conf,nfsmount.conf}
          var/lib/nfs/{etab,rmtab})
  depends=('rpcbind' 'nfsidmap' 'gssproxy' 'libevent' 'device-mapper')
  optdepends=('sqlite: for nfsdcltrack and fsidd usage'
              'python: for rpcctl, nfsiostat, nfsdclnts and mountstats usage')

  cd "${pkgbase}"-${pkgver}
  make DESTDIR="$pkgdir" install
 
  install -D -m 644 utils/mount/nfsmount.conf "$pkgdir"/etc/nfsmount.conf
  install -D -m 644 nfs.conf "$pkgdir"/etc/nfs.conf
  
  install -d -m 755 "$pkgdir"/usr/share/doc/$pkgname
  install -D -m 644 systemd/README "$pkgdir"/usr/share/doc/$pkgname/README.systemd

  # docs
  install -m 644 {NEWS,README} "$pkgdir"/usr/share/doc/$pkgname/

  # empty exports file  
  install -D -m 644 ../exports "$pkgdir"/etc/exports

  # config file for idmappers in newer kernels
  install -D -m 644 utils/nfsidmap/id_resolver.conf "$pkgdir"/etc/request-key.d/id_resolver.conf

  mkdir "$pkgdir"/etc/exports.d
  mkdir -m 555 "$pkgdir"/var/lib/nfs/rpc_pipefs
  mkdir "$pkgdir"/var/lib/nfs/v4recovery

  # systemd sysusers - FS#75536
  install -D -m 644 ../sysusers.d "${pkgdir}"/usr/lib/sysusers.d/rpcuser.conf
  chown -Rv 34:34 "${pkgdir}"/var/lib/nfs/statd
  chmod -R 700 "${pkgdir}"/var/lib/nfs/statd
  chmod 644 "${pkgdir}"/var/lib/nfs/statd/state

  # nfsidmap cleanup
  rm -vrf "$pkgdir"/usr/include #/nfsid*
  rm -vrf "$pkgdir"/usr/lib/libnfsidmap*
  rm -vrf "$pkgdir"/usr/lib/pkgconfig #/libnfsidmap.pc
  rm -v "$pkgdir"/usr/share/man/{man3/nfs4_uid_to_name*,man5/idmapd.conf*}
  rm -rf "$pkgdir"/usr/share/man/man3
}

package_nfsidmap() {

  pkgdesc="Library to help mapping IDs, mainly for NFSv4"
  license=('GPL2')
  backup=(etc/idmapd.conf)
  depends=('libldap' 'krb5')

  cd "${pkgbase}"-${pkgver}
  make -C support  DESTDIR="$pkgdir" install
  
  # part of -utils pkg
  rm -v "$pkgdir"/usr/bin/fsidd
  rmdir -v "$pkgdir"/usr/bin

  # config file  
  install -D -m 644 support/nfsidmap/idmapd.conf "$pkgdir"/etc/idmapd.conf
  # license
  install -Dm644 support/nfsidmap/COPYING $pkgdir/usr/share/licenses/nfsidmap/LICENSE
}
