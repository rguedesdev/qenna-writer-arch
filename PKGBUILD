pkgname=qenna-writer-git
pkgver=2.5.0.r319.g4015ba1
pkgrel=1
pkgdesc="Editor de escrita Qenna Writer"
arch=('x86_64')
options=('!debug')
url="https://github.com/Pedrovskigg/qenna-writer"
license=('custom:Qenna-Writer')
depends=(
    'qt6-base'
    'qt6-svg'
    'qt6-multimedia'
    'qt6-speech'
    'zlib'
)
makedepends=(
    'cmake'
    'ninja'
    'qt6-tools'
    'python'
)
provides=('qenna-writer')
conflicts=('qenna-writer')

source=(
    'qenna-writer::git+https://github.com/Pedrovskigg/qenna-writer.git'
    'hunspell::git+https://github.com/hunspell/hunspell.git#tag=v1.7.2'
)
sha256sums=('SKIP' 'SKIP')

pkgver() {
    cd "$srcdir/qenna-writer"
    printf '%s.r%s.g%s' \
        "$(cat VERSION)" \
        "$(git rev-list --count HEAD)" \
        "$(git rev-parse --short HEAD)"
}

prepare() {
    # Disponibiliza o código-fonte do Hunspell exigido pelo CMake.
    rm -rf "$srcdir/qenna-writer/third_party/hunspell"
    cp -a "$srcdir/hunspell" "$srcdir/qenna-writer/third_party/hunspell"

    # Corrige a posição do arquivo MOC para compatibilidade com Qt 6.12.
    python3 - "$srcdir/qenna-writer/src/WorldContentEditor.cpp" <<'PY'
import sys
from pathlib import Path

path = Path(sys.argv[1])
content = path.read_text()

include = '#include "WorldContentEditor.moc"\n'
marker = '} // namespace\n\nWorldContentEditor::WorldContentEditor'

if content.count(include) != 1:
    raise SystemExit("Não foi possível localizar exatamente uma inclusão do MOC.")

if marker not in content:
    raise SystemExit("Não foi possível localizar o final do namespace esperado.")

content = content.replace(include, '', 1)
content = content.replace(
    marker,
    '} // namespace\n\n#include "WorldContentEditor.moc"\n\nWorldContentEditor::WorldContentEditor',
    1
)

path.write_text(content)
PY
    # Ajusta o caminho dos recursos para a instalação definitiva no Arch Linux.
    python3 - "$srcdir/qenna-writer/CMakeLists.txt" <<'PY'
import sys
from pathlib import Path

path = Path(sys.argv[1])
content = path.read_text()

old = 'DEV_ASSETS_DIR="${CMAKE_SOURCE_DIR}/src/assets"'
new = 'DEV_ASSETS_DIR="/usr/lib/qenna-writer"'

if content.count(old) != 1:
    raise SystemExit("Não foi possível localizar exatamente uma definição de DEV_ASSETS_DIR.")

path.write_text(content.replace(old, new, 1))
PY

}

build() {
    cmake -S "$srcdir/qenna-writer" -B "$srcdir/build" -G Ninja \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX=/usr
        

    cmake --build "$srcdir/build" --parallel "$(nproc)"
}

package() {
    local appdir="$pkgdir/usr/lib/qenna-writer"

    install -d "$appdir"

    install -Dm755 "$srcdir/build/qenna-writer" \
        "$appdir/qenna-writer"

    # Recursos utilizados pelo aplicativo em tempo de execução.
    for asset in logo fonts spell theme-images thesaurus; do
        if [[ -d "$srcdir/build/$asset" ]]; then
            cp -a "$srcdir/build/$asset" "$appdir/"
        fi
    done

    # Lançador em /usr/bin.
    install -d "$pkgdir/usr/bin"
    cat > "$pkgdir/usr/bin/qenna-writer" <<'LAUNCHER'
#!/bin/sh
exec /usr/lib/qenna-writer/qenna-writer "$@"
LAUNCHER
    chmod 755 "$pkgdir/usr/bin/qenna-writer"

    # Ícone do aplicativo.
    install -Dm644 \
        "$srcdir/qenna-writer/src/assets/logo/app-icon-256.png" \
        "$pkgdir/usr/share/icons/hicolor/256x256/apps/qenna-writer.png"

    # Entrada no menu de aplicativos.
    install -Dm644 /dev/stdin \
        "$pkgdir/usr/share/applications/qenna-writer.desktop" <<'DESKTOP'
[Desktop Entry]
Version=1.0
Type=Application
Name=Qenna Writer
Comment=Editor de escrita
Exec=qenna-writer
Icon=qenna-writer
Terminal=false
Categories=Office;WordProcessor;
StartupNotify=true
DESKTOP

    # Licença original do projeto.
    install -Dm644 "$srcdir/qenna-writer/LICENSE" \
        "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}
