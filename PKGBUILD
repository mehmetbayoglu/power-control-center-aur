# Maintainer: mehmetbayoglu <mehmetbayoglu@users.noreply.github.com>
pkgname=power-control-center
pkgver=1.1.3
pkgrel=1
pkgdesc="GUI power management for Linux laptops — CPU (Intel/AMD), GPU (NVIDIA/Arc/AMD), battery, TLP, daemons, sensors"
arch=('any')
url="https://github.com/mehmetbayoglu/power-control-center"
license=('MIT')

depends=(
    'python'
    'tk'
    'polkit'
)

optdepends=(
    'power-profiles-daemon: profile switching (EPP, platform profile, turbo)'
    'tlp: peripheral power management + coexist mode with ppd'
    'thermald: Intel thermal daemon control'
    'auto-cpufreq: automatic CPU frequency/governor optimizer'
    'nvidia-utils: NVIDIA GPU power limit, stats, and runtime PM'
    'nvtop: GPU monitor (launched from GPU tab)'
    'turbostat: CPU turbostat sampling (CPU tab)'
    'stress-ng: CPU stress testing (Tools tab)'
    's-tui: terminal stress/monitoring TUI (Tools tab)'
    'powertop: power consumption analysis and auto-tune (Tools tab)'
    'lm_sensors: hardware sensor readings (Sensors tab)'
    'upower: battery status, energy rate, cycle count'
    'acpi: battery detail output'
)

source=(
    "$pkgname-$pkgver.py::https://raw.githubusercontent.com/mehmetbayoglu/power-control-center/v$pkgver/power-control-center.py"
    "$pkgname.desktop"
)
sha256sums=(
    '0cce0680381f7af0742a23683e89832f8ba6dff277321b9aa954be08d5871e54'
    '085eea02ff51ff70b93e1842349be02f081a44e2104c26176c6a9d9f42b52cca'
)

prepare() {
    python -c "
import ast, sys
try:
    ast.parse(open('$srcdir/$pkgname-$pkgver.py').read())
except SyntaxError as e:
    print(f'Syntax error: {e}', file=sys.stderr)
    sys.exit(1)
"
}

package() {
    # Main script
    install -Dm755 "$srcdir/$pkgname-$pkgver.py" \
        "$pkgdir/usr/share/$pkgname/$pkgname.py"

    # Wrapper so it launches from any terminal or app launcher
    install -dm755 "$pkgdir/usr/bin"
    cat > "$pkgdir/usr/bin/$pkgname" <<'EOF'
#!/bin/sh
exec python /usr/share/power-control-center/power-control-center.py "$@"
EOF
    chmod 755 "$pkgdir/usr/bin/$pkgname"

    # Desktop entry
    install -Dm644 "$srcdir/$pkgname.desktop" \
        "$pkgdir/usr/share/applications/$pkgname.desktop"

    # Polkit rule — allows wheel group to launch via pkexec without a password
    install -Dm644 /dev/stdin \
        "$pkgdir/usr/share/polkit-1/rules.d/49-$pkgname.rules" <<'EOF'
polkit.addRule(function(action, subject) {
    if (action.id == "org.freedesktop.policykit.exec" &&
        subject.isInGroup("wheel")) {
        return polkit.Result.YES;
    }
});
EOF

    # License
    install -Dm644 /dev/stdin "$pkgdir/usr/share/licenses/$pkgname/LICENSE" <<'EOF'
MIT License - Copyright (c) 2025 mehmetbayoglu
EOF
}
