pkgname=(elfutils libelf debuginfod)
pkgbase=elfutils
pkgver=0.193
pkgrel=2
pkgdesc="Handle ELF object files and DWARF debugging information (libraries)"
arch=('x86_64')
url="https://sourceware.org/elfutils/"
license=(
    'GPL-2.0-or-later'
    'LGPL-3.0-or-later'
)
makedepends=(
    'bzip2'
    'curl'
    'gcc-libs'
    'json-c'
    'libarchive'
    'sqlite'
    'zlib'
    'zstd'
    'xz'
)
source=(https://sourceware.org/elfutils/ftp/${pkgver}/${pkgbase}-${pkgver}.tar.bz2)
sha256sums=(7857f44b624f4d8d421df851aaae7b1402cfe6bcdd2d8049f15fc07d3dde7635)

build() {
    cd ${pkgbase}-${pkgver}

    local configure_args=(
        --sysconfdir=/etc
        --program-prefix="eu-"
        --enable-deterministic-archives
        --enable-maintainer-mode
        ${configure_options}
    )

    # fat-lto-objects is required for non-mangled .a files in libelf
    CFLAGS+=" -ffat-lto-objects"

    CFLAGS+=" -g"

    ./configure "${configure_args[@]}"

    make
}

package_elfutils() {
    pkgdesc+=" (utilities)"
    license=('GPL-3.0-or-later')
    depends=(
        'bash'
        'gcc-libs'
        'glibc'
        'libarchive'
        "libelf=${pkgver}"
    )

    cd ${pkgbase}-${pkgver}

    make DESTDIR=${pkgdir} install

    _pick libelf ${pkgdir}/etc/debuginfod
    _pick libelf ${pkgdir}/etc/profile.d/*
    _pick libelf ${pkgdir}/usr/{include,lib64}
    _pick libelf ${pkgdir}/usr/share/man/*/{,lib}elf*
    _pick debuginfod ${pkgdir}/usr/bin/debuginfod*
    _pick debuginfod ${pkgdir}/usr/share/fish/vendor_conf.d/debuginfod.fish
    _pick debuginfod ${pkgdir}/usr/share/man/*/debuginfod*

}

package_libelf() {
    pkgdesc+=" (libraries)"
    license=(
        'GPL-2.0-or-later'
        'LGPL-3.0-or-later'
    )
    depends=(
        'bzip2'
        'curl'
        'glibc'
        'json-c'
        'zlib'
        'zstd'
        'xz'
    )

    mv -v libelf/* ${pkgdir}
}

package_debuginfod() {
    pkgdesc+=" (debuginfod)"
    license=('GPL-3.0-or-later')
    depends=(
        'gcc-libs'
        'glibc'
        'json-c'
        'libarchive'
        "libelf=${pkgver}"
        'sqlite'
        'xz'
    )

    mv -v debuginfod/* ${pkgdir}
}
