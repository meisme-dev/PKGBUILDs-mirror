# Holo Maintainer: Emil Velikov <emil.l.velikov@gmail.com>
# Maintainer: David Runge <dvzrv@archlinux.org>
# Contributor: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>

pkgname=alsa-ucm-conf
pkgver=1.2.9
pkgrel=1.1 # Holo: remove Jupiter symlink
pkgdesc="ALSA Use Case Manager configuration (and topologies)"
arch=(any)
url="https://alsa-project.org/"
license=(BSD)
source=(https://www.alsa-project.org/files/pub/lib/$pkgname-$pkgver.tar.bz2{,.sig})
sha512sums=('3aced4d4df76044a5525f1217851e05f7ad177389d2008e3e3dd0fb4d2cf72cd0897b140659a7729cc37d5b737c2d0cf5b607df4f576647044accd560d8cb87b'
            'SKIP')
b2sums=('fe71790aa8658ab12378baa55f95a03aaebd3eb0aef2535b65b89884de6cb404a75b44fd238d2089e0a005ca81720f3c44d1912452908e97aa75fcda513908cf'
        'SKIP')
validpgpkeys=('F04DF50737AC1A884C4B3D718380596DA6E59C91') # ALSA Release Team (Package Signing Key v1) <release@alsa-project.org>

package() {
  cd $pkgname-$pkgver
  install -vdm 755 "$pkgdir/usr/share/alsa/"
  cp -av ucm2 "$pkgdir/usr/share/alsa/"
  install -vDm 644 LICENSE -t "$pkgdir/usr/share/licenses/$pkgname"
  install -vDm 644 README.md -t "$pkgdir/usr/share/doc/$pkgname"
  install -vDm 644 ucm2/README.md -t "$pkgdir/usr/share/doc/$pkgname/ucm2"

  # Holo: there missmatch between the kernel driver, firmware module and ucm
  # sequence needed. Drop the symlink, so our custom ucm gets picked.
  #
  # XXX: Add bug link/number once we have one with all the details.
  rm "$pkgdir/usr/share/alsa/ucm2/conf.d/acp5x/Valve-Jupiter-1.conf"
}
