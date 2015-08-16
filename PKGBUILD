# Maintainer: Filip Brcic <brcha@gna.org>

_qt_module=qtscript
pkgname="mingw-w64-qt5-${_qt_module}"
pkgver=5.0.2
pkgrel=1
arch=('any')
pkgdesc="Qt5 for Windows -- QtScript module (mingw-w64)"
depends=('mingw-w64-qt5-qtbase')
makedepends=('mingw-w64-gcc')
options=(!strip !buildflags)
license=('GPL3' 'LGPL')
url="http://qt-project.org/"
_pkgfqn="${_qt_module}-opensource-src-${pkgver}"
source=("http://releases.qt-project.org/qt5/${pkgver}/submodules/${_pkgfqn}.tar.xz")
md5sums=('929db18c2f4f66cb2ba204076f9f0c22')

_architectures="i686-w64-mingw32 x86_64-w64-mingw32"

build()
{
  cd ${srcdir}/${_pkgfqn}

  for _arch in ${_architectures}; do
    mkdir -p build-${_arch} && pushd build-${_arch}

    ${_arch}-qmake-qt5 ../${_qt_module}.pro
    make

    popd
  done
}

package() {
  cd ${srcdir}/${_pkgfqn}

  for _arch in ${_architectures}; do
    pushd build-${_arch}

    make INSTALL_ROOT=$pkgdir install

    # Rename the .a files to .dll.a as they're actually import libraries and not static libraries
    for FN in ${pkgdir}/usr/${_arch}/lib/*.a
    do
      FN_NEW=$(echo $FN | sed s/'.a$'/'.dll.a'/)
      mv $FN $FN_NEW
    done

    # The .dll's are installed in both bindir and libdir
    # One copy of the .dll's is sufficient
    rm -f ${pkgdir}/usr/${_arch}/lib/*.dll

    ${_arch}-strip --strip-unneeded ${pkgdir}/usr/${_arch}/bin/*.dll
    ${_arch}-strip --strip-unneeded ${pkgdir}/usr/${_arch}/lib/*.dll.a

    popd
  done

  # .prl files aren't interesting for us
  find ${pkgdir} -name "*.prl" -delete
}
