# Maintainer: John Schoenick <johns@valvesoftware.com>

# This traces back to the older incarnation of holo/steamos3 that didn't have things split cleanly into packages.
# Everything still in here should be either removed or re-homed to a proper package.

pkgname=jupiter-legacy-support
pkgver=1.142
pkgrel=1
pkgdesc="Legacy jupiter-specific support files that haven't been split to their own package or removed."
arch=(any)
depends=(python3 python-psutil python-aiohttp nvme-cli)
source=(steam-web-debug-portforward.service
        killuserprocesses.conf
        flathub-beta.flatpakrepo
        flatpak-workaround.service
        flatpak-modify-flathub-beta.service
        steamos-prepare-oobe-test
        sudoers.d-wheel-prepare-oobe-test
        org.valve.steamos.jupiter-legacy-support.policy
        black_800x1280.png
        white_800x1280.png)
sha256sums=('a1a2c6cb5ebdba68b79ab90649f894e1136f72af9e4daacbcb71d134383bd797'
            'e34a9dc905771bd99cd04cdf88262481cab7a7808d99dfaa968366fcb1b99a0b'
            '582cae3c9f9d4639f027defafe6fa33bda0a3a4d441290d926ad85a2be0f7206'
            '608a3132b952022282ff1219d61acc627c58500e12033cc223c2327d6ee89c3c'
            'a7b8b21e285dac1f255546d1acc46d4423a1fa0e964153a96118b884001d0648'
            '1875fcfe626ca442249e338eef1d6caa8a80d63b3fded0a8c9fca5bc7d148011'
            '3f3491c7ccf72b62094379495c73e6fdecd182d5aa30072b3b2407e331b96806'
            '27739fb50e5c2dd50e3373b22b5ceabb6eb2f6f34b723794cf9a2f911a483f65'
            '942fbb9436835bdb3a87aa8d73b3461f4cee0bc2f58bfa308eeb1be6b52ccb39'
            'fd55e252b11a0b0d48b7147298f159b0470f29ccb6118a79a5692cc8c4635f5b')

package() {
  # Hacky -- this package isn't proper and just references the salt files it owns.
  # Everything in here needs to be split into proper packaging and this package obsoleted.
  cd "$startdir"/saltfiles/

  install -D -m644 "$srcdir"/black_800x1280.png "$pkgdir"/usr/share/jupiter_testing/black_800x1280.png
  install -D -m644 "$srcdir"/white_800x1280.png "$pkgdir"/usr/share/jupiter_testing/white_800x1280.png

  # flathub workaround and beta repo
  install -D -m644 "$srcdir"/flathub-beta.flatpakrepo "$pkgdir"/etc/flatpak/remotes.d/flathub-beta.flatpakrepo
  install -D -m644 "$srcdir"/flatpak-workaround.service "$pkgdir"/usr/lib/systemd/system/flatpak-workaround.service
  install -d "$pkgdir"/usr/lib/systemd/system/multi-user.target.wants/
  ln -sv ../flatpak-workaround.service "$pkgdir"/usr/lib/systemd/system/multi-user.target.wants/

  install -D -m644 "$srcdir"/flatpak-modify-flathub-beta.service "$pkgdir"/usr/lib/systemd/system/flatpak-modify-flathub-beta.service
  ln -sv ../flatpak-modify-flathub-beta.service "$pkgdir"/usr/lib/systemd/system/multi-user.target.wants/

  # janky OOBE test utility
  install -D -m755 "$srcdir"/steamos-prepare-oobe-test "$pkgdir"/usr/bin/steamos-prepare-oobe-test
  install -D -m440 "$srcdir"/sudoers.d-wheel-prepare-oobe-test "$pkgdir"/etc/sudoers.d/wheel-prepare-oobe-test
  install -D -m755 "$srcdir"/org.valve.steamos.jupiter-legacy-support.policy "$pkgdir"/usr/share/polkit-1/actions/org.valve.steamos.jupiter-legacy-support.policy

  install -D -m644 "$srcdir"/killuserprocesses.conf "$pkgdir"/etc/systemd/logind.conf.d/killuserprocesses.conf

  install -D -m644 "$srcdir"/steam-web-debug-portforward.service "$pkgdir"/etc/systemd/system/steam-web-debug-portforward.service

  install -D -m644 xbindkeysrc "$pkgdir"/etc/xbindkeysrc

  # Stats daemon will be enabled/started by bootstrap

  install -D -m755 -t "$pkgdir"/usr/bin/ usr/bin/*

  install -D -m644 {,"$pkgdir"/}usr/share/X11/xorg.conf.d/41-touchscreenrotate.conf

  install -D -m644 {,"$pkgdir"/}usr/share/xsessions/plasma-steamos-oneshot.desktop
  install -D -m644 {,"$pkgdir"/}usr/share/wayland-sessions/plasma-steamos-wayland-oneshot.desktop

  install -D -m644 {,"$pkgdir"/}etc/sddm.conf.d/steamos.conf
  # install -D -m644 {,"$pkgdir"/}etc/pulse/default.pa

  # FIXME reconcile with grub-steamos
  install -D -m644 etc/default/grub "$pkgdir"/etc/default/grub-legacy

  install -D -m644 {,"$pkgdir"/}etc/systemd/system/NetworkManager.service.d/override.conf
  install -D -m644 {,"$pkgdir"/}etc/systemd/system.conf.d/foxnet-system.conf
  install -D -m644 {,"$pkgdir"/}etc/systemd/logind.conf.d/suspendbutton.conf

  # Workaround -- disable Holo's /var/boot mechanics entirely.  They're broken in a number of ways and are bricking
  # units.  The feature it's trying to enable (DKMS?) isn't even relevant at the moment.
  #
  # Symlinking to /dev/null is actually what `systemctl mask` does. Really.
  ln -sv /dev/null "$pkgdir"/etc/systemd/system/boot.mount
  ln -sv /dev/null "$pkgdir"/etc/systemd/system/steamos-mkvarboot.service
  ln -sv /dev/null "$pkgdir"/etc/systemd/system/steamos-install-grub.service

  # Horrible workaround. We should fix this.
  # (the full bootstrap package should not need this)
  mkdir -m755 -p "$pkgdir"/usr/share/fonts/truetype/ttf-dejavu
  ln -sv ../../TTF/DejaVuSans.ttf "$pkgdir"/usr/share/fonts/truetype/ttf-dejavu/DejaVuSans.ttf

  # Non-saltfiles
  mkdir -p -m755 "$pkgdir"/etc/systemd/system/sound.target.wants
  mkdir -p -m755 "$pkgdir"/etc/systemd/system/multi-user.target.wants
}
