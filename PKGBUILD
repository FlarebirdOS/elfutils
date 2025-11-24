pkgname=(
    elfutils
    libelf
    debuginfod
)
pkgbase=elfutils
pkgver=0.194
pkgrel=3
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
    'git'
    'json-c'
    'libarchive'
    'libmicrohttpd'
    'sqlite'
    'xz'
    'zlib'
    'zstd'
)
source=(git+https://sourceware.org/git/elfutils.git#tag=${pkgbase}-${pkgver})
sha256sums=(1e02d817ff6b689c10ad0595d2b942dd7b32af9098b3c72be29f1adb6d09ee55)

prepare() {
    cd ${pkgbase}

    # fix issue with /etc/profile.d/debuginfod.sh for zsh:
    # https://gitlab.archlinux.org/archlinux/packaging/packages/elfutils/-/issues/2
    git cherry-pick -n 00cb3efe36337f27925dbff9b2e7d97c7df95bf8

    # remove failing test due to missing glibc debug package during test: https://bugs.archlinux.org/task/74875
    sed -e 's/run-backtrace-native.sh//g' -i tests/Makefile.am

    autoreconf -fiv
}

build() {
    cd ${pkgbase}

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

    cd ${pkgbase}

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
        'libmicrohttpd'
        'sqlite'
        'xz'
    )

    mv -v debuginfod/* ${pkgdir}
}
