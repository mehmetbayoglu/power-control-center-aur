# Maintainer: mehmetbayoglu <mehmetbayoglu@users.noreply.github.com>
pkgname=power-control-center
pkgver=1.0.0
pkgrel=1
pkgdesc="GUI power management for Linux laptops — CPU (Intel/AMD), GPU (NVIDIA/Arc/AMD), battery, TLP, daemons, sensors"
arch=('any')
url="https://github.com/mehmetbayoglu/power-control-center"
license=('MIT')

depends=(
    'python'
    'tk'        # python-tkinter
    'polkit'    # pkexec for privileged sysfs writes
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
)
sha256sums=('30fe4ddf4d95403a2d044ece9b2fe75dcf1846d1f6d5e697bcd0f5d94fab59be')

prepare() {
    # Validate the script is valid Python before packaging
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
    # ── Main script ──────────────────────────────────────────────────────
    install -Dm755 "$srcdir/$pkgname-$pkgver.py" \
        "$pkgdir/usr/share/$pkgname/$pkgname.py"

    # Wrapper in /usr/bin so it runs from any terminal or launcher
    install -dm755 "$pkgdir/usr/bin"
    cat > "$pkgdir/usr/bin/$pkgname" <<'EOF'
#!/bin/sh
exec python /usr/share/power-control-center/power-control-center.py "$@"
EOF
    chmod 755 "$pkgdir/usr/bin/$pkgname"

    # ── Desktop entry ────────────────────────────────────────────────────
    install -dm755 "$pkgdir/usr/share/applications"
    cat > "$pkgdir/usr/share/applications/$pkgname.desktop" <<'EOF'
[Desktop Entry]
Name=Power Control Center
GenericName=Power Manager
Comment=Manage CPU, GPU, battery, TLP, and power daemons
Exec=power-control-center
Icon=battery
Terminal=false
Type=Application
Categories=System;Settings;HardwareSettings;
Keywords=power;cpu;gpu;battery;tlp;performance;energy;
StartupNotify=true
EOF

    # ── License ──────────────────────────────────────────────────────────
    install -dm755 "$pkgdir/usr/share/licenses/$pkgname"
    cat > "$pkgdir/usr/share/licenses/$pkgname/LICENSE" <<'EOF'
MIT License

Copyright (c) 2025 zmite

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
EOF
}
