# Maintainer: Antonin Décimo <antonin dot decimo at gmail dot com>
# Contributor: AndyRTR <andyrtr@archlinux.org>

pkgname=xorg-xwayland-git
pkgver=24.0.99.1.r942.g596db5424
pkgrel=1
arch=('x86_64')
license=('custom')
groups=('xorg')
url="https://xorg.freedesktop.org"
pkgdesc="Run X clients under Wayland (git version)"
depends=('nettle' 'libegl' 'libepoxy' 'systemd-libs' 'libxfont2'
         'pixman' 'xorg-server-common' 'libxcvt' 'libglvnd')
makedepends=('meson' 'git'
             'xorgproto-git' 'xtrans'
             'pixman' 'libxkbfile' 'libxfont2' 'dbus'
             'xorg-font-util'
             'wayland' 'wayland-protocols'
             'libdrm' 'libepoxy'
             'systemd'
             'egl-wayland'
)
source=("git+https://gitlab.freedesktop.org/xorg/xserver.git#branch=xwayland-24.1")
sha256sums=('SKIP')
provides=('xorg-xwayland' 'xorg-server-xwayland' 'xorg-server-xwayland-git')
conflicts=('xorg-xwayland' 'xorg-server-xwayland' 'xorg-server-xwayland-git')
replaces=('xorg-server-xwayland-git')

pkgver() {
  cd xserver
  # replace latest tag with version from meson
  local _meson_ver=`grep -m 1 version meson.build | cut -d\' -f 2`
  # cutting off 'xorg.server.' prefix that presents in the git tag
  local _git_ver=`git describe --long --tags | sed 's/^xorg.server.//;s/\([^-]*-g\)/r\1/;s/-/./g'`
  local _git_tag=`git describe --tags --abbrev=0 | sed 's/^xorg.server.//'`
  printf "${_git_ver/$_git_tag/$_meson_ver}"
}

build() {
  # Since pacman 5.0.2-2, hardened flags are now enabled in makepkg.conf
  # With them, module fail to load with undefined symbol.
  # See https://bugs.archlinux.org/task/55102 / https://bugs.archlinux.org/task/54845
#  export CFLAGS=${CFLAGS/-fno-plt}
#  export CXXFLAGS=${CXXFLAGS/-fno-plt}
#  export LDFLAGS=${LDFLAGS/,-z,now}

  arch-meson xserver build \
    -D ipv6=true \
    -D xvfb=false \
    -D xnest=false \
    -D xcsecurity=true \
    -D xorg=false \
    -D xephyr=false \
    -D xwayland=true \
    -D xwin=false \
    -D xquartz=false \
    -D glamor=true \
    -D udev=true \
    -D systemd_logind=true \
    -D suid_wrapper=true \
    -D xkb_dir=/usr/share/X11/xkb \
    -D xkb_output_dir=/var/lib/xkb

  # Print config
  meson configure build
  ninja -C build
}

package() {

  # bin + manpage + .pc file
  install -m755 -Dt "${pkgdir}"/usr/bin build/hw/xwayland/Xwayland
  install -m644 -Dt "${pkgdir}"/usr/share/man/man1 build/hw/xwayland/Xwayland.1
  install -m644 -Dt "${pkgdir}"/usr/lib/pkgconfig build/meson-private/xwayland.pc

  # license
  install -m644 -Dt "${pkgdir}/usr/share/licenses/${pkgname}" xserver/COPYING
}
