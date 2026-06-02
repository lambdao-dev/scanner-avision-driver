# Maintainer: len <len@lambdao.dev>
# Repackages Avision's official .deb (SANE backend for Avision scanners,
# incl. FB5100) into a native, pacman-tracked Arch package.
pkgname=scanner-driver-avision
pkgver=0.1.0.26090
pkgrel=1
pkgdesc="SANE backend for Avision scanners (FB5100 and others), repackaged from Avision's .deb"
arch=('x86_64')
url="https://www.avision.com/en/shop/flatbed-scanner/fb5100/#tab-downloads"
license=('custom')
depends=('sane' 'libusb' 'libusb-compat' 'libstdc++' 'libgcc' 'libgomp')
provides=('sane-backend-avision-adv')
options=('!strip')
backup=(
    'etc/sane.d/DeviceList.conf'
    'etc/sane.d/OptDefault_avision_adv.ini'
    'etc/sane.d/OptSetAuto_avision_adv.ini'
    'etc/sane.d/ScaleImage_avision_adv.ini'
    'etc/sane.d/avision_adv_en-us.lang'
    'etc/sane.d/avision_adv_zh-cn.lang'
    'etc/sane.d/avision_adv_zh-tw.lang'
    'etc/sane.d/dll.d/avision_adv'
    'etc/udev/rules.d/99-avision_sane.rules'
)

# Avision distributes the driver as a tarball via a Dropbox direct link
# (reached from the "Downloads" tab of the url above). makepkg downloads it
# and renames it to a stable local filename.
_deb="scanner-driver-avision_0.1.0-26090_amd64.deb"
_tarball="scanner-driver-avision_deb64_${pkgver}_20260331.tar.gz"
source=("${_tarball}::https://www.dropbox.com/scl/fi/7veh5qvegiqji0eerrobn/scanner-driver-avision_deb64_0.1.0.26090_20260331.tar.gz?rlkey=0s4w2zb7senh7307qyi13e5q8&dl=1")
sha256sums=('26a8271f1b96f0d7f79d90408f485fbd2f80de01751b0255cc24e700bc265677')
noextract=("${_tarball}")

package() {
    cd "${srcdir}"
    bsdtar -xf "${_tarball}"
    bsdtar --no-same-owner -xf "${_deb}"
    bsdtar --no-same-owner -xf data.tar.gz -C "${pkgdir}"

    # Keep Avision's expected /opt path available, but store ELF payloads in
    # Arch's library hierarchy.
    install -dm755 "${pkgdir}/usr/lib/${pkgname}"
    mv "${pkgdir}/opt/apps/${pkgname}/sane" "${pkgdir}/usr/lib/${pkgname}/"
    rmdir "${pkgdir}/opt/apps/${pkgname}" "${pkgdir}/opt/apps" "${pkgdir}/opt"
    install -dm755 "${pkgdir}/opt/apps"
    ln -sf "/usr/lib/${pkgname}" "${pkgdir}/opt/apps/${pkgname}"

    # Pick the libusb-1.0 build of the SANE meta-backend (matches Avision's
    # postinst behaviour on a libusb-1.0 system) and expose it to SANE.
    install -dm755 "${pkgdir}/usr/lib/sane"
    local _backend="/usr/lib/${pkgname}/sane/libsane-avision_adv.so.1.0.22_1.0"
    ln -sf "${_backend}" "${pkgdir}/usr/lib/sane/libsane-avision_adv.so.1"
    ln -sf "${_backend}" "${pkgdir}/usr/lib/sane/libsane-avision_adv.so"

    install -dm755 "${pkgdir}/etc/sane.d/dll.d"
    printf 'avision_adv\n' > "${pkgdir}/etc/sane.d/dll.d/avision_adv"
}
