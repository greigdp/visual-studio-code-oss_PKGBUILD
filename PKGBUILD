# Maintainer: Michael Hansen <zrax0111 gmail com>

pkgname=visual-studio-code-oss
pkgdesc='Visual Studio Code for Linux, Open Source version'
pkgver=0.10.9
pkgrel=1
arch=('i686' 'x86_64')
url='https://code.visualstudio.com/'
license=('MIT')
makedepends=('npm' 'gulp' 'python2')
depends=('gtk2' 'gconf')
conflicts=('vscode-oss')
provides=('vscode-oss')

source=("https://github.com/Microsoft/vscode/archive/${pkgver}.tar.gz"
        "${pkgname}.desktop"
        'fix_super.patch'
        'product_json.patch')
sha1sums=('94c6fa5db974d26e769e1f80a24f996424de7041'
          '2039174831d5483050471d69e0765b684cbeeefe'
          'd51635bd1e9568eb31224170b10d48154dcc2c8c'
          '4a7f41876a016ef8d969fb81c7b1b4bc7b1e026b')

case "$CARCH" in
    i686)
        _vscode_arch=ia32
        ;;
    x86_64)
        _vscode_arch=x64
        ;;
    *)
        # Needed for mksrcinfo
        _vscode_arch=DUMMY
        ;;
esac

prepare() {
    cd "${srcdir}/vscode-${pkgver}"

    patch -p1 -i "${srcdir}/fix_super.patch"
    patch -p1 -i "${srcdir}/product_json.patch"
}

build() {
    cd "${srcdir}/vscode-${pkgver}"

    # npm 3.x flattens modules, which the package tasks can't handle yet
    ./scripts/npm.sh install 'npm@2'
    export PATH="${srcdir}/vscode-${pkgver}/node_modules/.bin:$PATH"

    ./scripts/npm.sh install
    gulp vscode-linux-${_vscode_arch}
}

package() {
    install -m 0755 -d "${pkgdir}/opt/VSCode-OSS"
    cp -r "${srcdir}/VSCode-linux-${_vscode_arch}"/* "${pkgdir}/opt/VSCode-OSS"

    # Include symlink in system bin directory
    install -m 0755 -d "${pkgdir}/usr/bin"
    ln -s '/opt/VSCode-OSS/Code - OSS' "${pkgdir}/usr/bin/${pkgname}"

    # Add .desktop file
    install -D -m644 "${srcdir}/${pkgname}.desktop" \
            "${pkgdir}/usr/share/applications/${pkgname}.desktop"

    # Install license file
    install -D -m644 "${srcdir}/VSCode-linux-${_vscode_arch}/resources/app/LICENSE.txt" \
            "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}
