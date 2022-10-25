# Maintainer: Jan de Groot <jgc@archlinux.org>

pkgname=libdrm
pkgver=2.4.110
pkgrel=2
pkgdesc="Userspace interface to kernel DRM services"
url="https://dri.freedesktop.org/"
arch=(x86_64)
license=('custom')
depends=('libpciaccess')
makedepends=('python-docutils' 'meson')
checkdepends=('cairo')
replaces=('libdrm-new' 'libdrm-nouveau')
source=(https://dri.freedesktop.org/$pkgname/$pkgname-$pkgver.tar.xz{,.sig}
        COPYING)
sha512sums=('52f92ef1fe4c218a1d7dba53ef43334dbfca80e3209afe59f3a32c4bf67473126534e990df07a931a12d46a3b997c21ef17c1c4d8a0c88d44d5c6c040e3b6be3'
            'SKIP'
            'b0ca349b882a4326b19f81f22804fabdb6fb7aef31cdc7b16b0a7ae191bfbb50c7daddb2fc4e6c33f1136af06d060a273de36f6f3412ea326f16fa4309fda660')
#validpgpkeys=('10A6D91DA1B05BD29F6DEBAC0C74F35979C486BE') # David Airlie <airlied@redhat.com>
#validpgpkeys+=('34FF9526CFEF0E97A340E2E40FDE7BE0E88F5E48') # emersion <contact@emersion.fr>
#validpgpkeys+=('E9E246A89E65374EB8355796B8B5FA0EFC1EC2FE') # "Leo Liu <leo.liu@amd.com>"
#validpgpkeys=('899A810900B815807742E6D895A677A230AC4AA9') # Bas Nieuwenhuizen <bas@basnieuwenhuizen.nl>
validpgpkeys=('2617F6F8DEBB0A7A15C405C983FD600E340FB5FF') # Samuel Pitoiset <samuel.pitoiset@gmail.com>

build() {
  arch-meson $pkgname-$pkgver build \
    -Dudev=false
  ninja -C build
}

check() {
  meson test -C build
}

package() {
  DESTDIR="$pkgdir" meson install -C build
  install -Dt "$pkgdir/usr/share/licenses/$pkgname" -m644 COPYING
}
