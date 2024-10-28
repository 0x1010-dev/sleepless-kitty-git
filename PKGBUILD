# Maintainer: Fabio 'Lolix' Loli <fabio.loli@disroot.org> -> https://github.com/FabioLolix
# Contributor: timescam <rex.ky.ng at gmail dot com>
# Contributor: Maximilian Kindshofer <maximilian@kindshofer.net>

pkgbase=sleepless-kitty-git
pkgname=(kitty-git kitty-terminfo-git kitty-shell-integration-git)
pkgver=0.36.4.r100.g8b7cd98a0
pkgrel=1
epoch=1
pkgdesc="Modern, hackable, featureful, OpenGL based terminal emulator"
arch=(i686 x86_64)
url="https://sw.kovidgoyal.net/kitty/"
license=(GPL3)
depends=(python wayland libx11 dbus lcms2 fontconfig
  libxkbcommon-x11 librsync xxhash) #python-pygments
makedepends=(git python-setuptools libxinerama libxcursor libxrandr libxkbcommon mesa
  wayland-protocols python-sphinx python-sphinx-copybutton
  ttf-nerd-fonts-symbols-mono freetype2 libxi libgl libcanberra simde
  python-sphinx-inline-tabs python-sphinxext-opengraph python-sphinx-furo go)
source=(
  "git+https://github.com/kovidgoyal/kitty.git"
  "remove-window-suspended-check.patch"
)
sha256sums=('SKIP'
  '85574837459882a8d559209550d480f82ec2d7b57bfc6f2b5665b8dc7c56ac28')

prepare() {
  cd "${srcdir}/${pkgname%-git}"
  patch -Np1 -i ../remove-window-suspended-check.patch
}

pkgver() {
  cd "${srcdir}/${pkgname%-git}"
  git describe --long --tags --exclude nightly | sed 's/^v//;s/\([^-]*-g\)/r\1/;s/-/./g'
}

build() {
  cd "${srcdir}/${pkgname%-git}"
  export CGO_CPPFLAGS="${CPPFLAGS}"
  export CGO_CFLAGS="${CFLAGS}"
  export CGO_CXXFLAGS="${CXXFLAGS}"
  export CGO_LDFLAGS="${LDFLAGS}"
  export GOFLAGS="-buildmode=pie -trimpath -ldflags=-linkmode=external -mod=readonly -modcacherw"
  python setup.py linux-package --update-check-interval=0
}

package_kitty-git() {
  depends+=(kitty-terminfo-git kitty-shell-integration-git)
  optdepends=('imagemagick: viewing images with icat')
  provides=(kitty)
  conflicts=(kitty)

  cd "${srcdir}/${pkgname%-git}"

  cp -r linux-package "${pkgdir}"/usr

  # completions
  python __main__.py + complete setup bash | install -Dm644 /dev/stdin "${pkgdir}"/usr/share/bash-completion/completions/kitty
  python __main__.py + complete setup fish | install -Dm644 /dev/stdin "${pkgdir}"/usr/share/fish/vendor_completions.d/kitty.fish
  # doesn't know how to http://zsh.sourceforge.net/Doc/Release/Completion-System.html#Autoloaded-files
  # so we write our own header
  {
    echo "#compdef kitty"
    python __main__.py + complete setup zsh
  } | install -Dm644 /dev/stdin "${pkgdir}"/usr/share/zsh/site-functions/_kitty

  install -Dm644 "${pkgdir}"/usr/share/icons/hicolor/256x256/apps/kitty.png "${pkgdir}"/usr/share/pixmaps/kitty.png

  rm -r "$pkgdir"/usr/share/terminfo
  rm -r "$pkgdir"/usr/lib/kitty/shell-integration

  install -Dm644 docs/generated/conf/kitty.conf "${pkgdir}"/usr/share/doc/${pkgname}/kitty.conf
}

package_kitty-terminfo-git() {
  pkgdesc="Terminfo for kitty, an OpenGL-based terminal emulator"
  depends=(ncurses)
  arch=(any)
  provides=(kitty-terminfo)
  conflicts=(kitty-terminfo)

  mkdir -p "$pkgdir/usr/share/terminfo"
  tic -x -o "$pkgdir/usr/share/terminfo" kitty/terminfo/kitty.terminfo
}

package_kitty-shell-integration-git() {
  pkgdesc='Shell integration scripts for kitty, an OpenGL-based terminal emulator'
  depends=()
  arch=(any)
  provides=(kitty-shell-integration)
  conflicts=(kitty-shell-integration)

  mkdir -p "$pkgdir/usr/lib/kitty/"
  cp -r "$srcdir/kitty/shell-integration" "$pkgdir/usr/lib/kitty/"
}
