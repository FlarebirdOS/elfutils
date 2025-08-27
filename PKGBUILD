pkgname=libelf
pkgbase=elfutils
pkgver=0.193
pkgrel=1
pkgdesc="Handle ELF object files and DWARF debugging information (libraries)"
arch=('x86_64')
url="https://sourceware.org/elfutils/"
license=('GPL-2.0-or-later OR LGPL-3.0-or-later')
depends=('glibc' 'xz' 'zstd')
source=(https://sourceware.org/elfutils/ftp/${pkgver}/${pkgbase}-${pkgver}.tar.bz2)
sha256sums=(7857f44b624f4d8d421df851aaae7b1402cfe6bcdd2d8049f15fc07d3dde7635)

build() {
    cd ${pkgbase}-${pkgver}

    local configure_args=(
        --disable-debuginfod
        --enable-libdebuginfod=dummy
        ${configure_options}
    )

    # fat-lto-objects is required for non-mangled .a files in libelf
    CFLAGS+=" -ffat-lto-objects"

    CFLAGS+=" -g"

    ./configure "${configure_args[@]}"

    make
}

package() {
    cd ${pkgbase}-${pkgver}

    make DESTDIR=${pkgdir} -C libelf install
   
    install -vDm644 config/libelf.pc ${pkgdir}/usr/lib64/pkgconfig/libelf.pc
}
