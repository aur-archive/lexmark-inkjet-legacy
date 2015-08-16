# Mantainer: Simone Sclavi 'Ito' <darkhado@gmail.com>
## mirror for lexmark-inkjet-legacy-wJRE-1.0-1.i386.rpm.sh.tar.gz:
## http://dl.dropbox.com/u/7141888/lexmark-inkjet-legacy-wJRE-1.0-1.i386.rpm.sh.tar.gz
##
## mirror for lexmark-inkjet-legacy-wJRE-1.0-1.x86_64.rpm.sh.tar.gz:
## http://dl.dropbox.com/u/7141888/lexmark-inkjet-legacy-wJRE-1.0-1.x86_64.rpm.sh.tar.gz
pkgname=lexmark-inkjet-legacy
pkgver=1.0_1
pkgrel=8
pkgdesc='Inkjet drivers for Lexmark printers & multifunction'
arch=('x86_64' 'i686')
url="http://www.lexmark.com"
license=('custom')
depends=('cups' 'sane' 'xorg-xmessage' 'libxtst' 'libusb-compat')
makedepends=('rpmextract') 
optdepends=('xsane: graphical scanning frontend')
case "$CARCH" in
    i686) __arch='i386'; _lx_md5=('08749f85671a31023fdee9e961b0a0ea');;
    x86_64) __arch='x86_64'; _lx_md5=('21c6a4ec437568373e69173057907549');;
esac
source=("http://downloads.lexmark.com/downloads/cpd/$pkgname-wJRE-${pkgver/_/-}.${__arch}.rpm.sh.tar.gz"
        umf.sh
        umf-legacy.service)
md5sums=( $_lx_md5
        924c64002efd95ee91e2d6f822c7aa5f
        fee274fd1b752336d383e7552d45973a)
install=lx.install
package() {
    ./$pkgname-wJRE-${pkgver/_/-}.${__arch}.rpm.sh --noexec --target lexmark-printer-drivers
    cd lexmark-printer-drivers

    mkdir -p $pkgdir/usr/share/licenses/$pkgname
    cp files_extra/license* $pkgdir/usr/share/licenses/$pkgname

    bsdtar xf instarchive_all --lzma

    mkdir lexmark-inkjet-legacy
    rpmextract.sh $pkgname-wJRE-${pkgver/_/-}.${__arch}.rpm
    rpmextract.sh lexmark-legacy-wsu-${pkgver/_/-}.${__arch}.rpm
    mv usr $pkgname

    if [ "$CARCH" == i686 ]; then
        rm -rf ${pkgname}/usr/local/lexmark/legacy/lib64
        rm -rf ${pkgname}/usr/local/lexmark/wsu_legacy/lib64
    fi

    cd ${pkgname}/usr/local/lexmark/legacy/etc/
    mkdir -p $pkgdir/usr/share/cups/model/Lexmark
    cp *.ppd $pkgdir/usr/share/cups/model/Lexmark
    cd ../../..
    mkdir -p $pkgdir/usr/share/lexmark
    cp -a lexmark $pkgdir/usr/share
    cd lexmark/legacy/bin
    install -Dm755 lxhcp $pkgdir/usr/lib/cups/backend/lxhcp
    
    mkdir -p $pkgdir/usr/bin
    mkdir -p $pkgdir/etc/udev/rules.d
    ##remove obsolete udev rules	
    sed -i '/SYSFS/d' ${pkgdir}/usr/share/lexmark/legacy/etc/99-lexmarklegacy-10.rules
    sed -i '/SYSFS/d' ${pkgdir}/usr/share/lexmark/wsu_legacy/etc/99-lexmarklegacy-util.rules

    ln -s /usr/share/lexmark/legacy/bin/.scripts/lxtoolbox $pkgdir/usr/bin/lxtoolbox
    ln -s /usr/share/lexmark/legacy/bin/.scripts/postinstall.sh $pkgdir/usr/bin/lx-postinstall.sh  
    ln -s /usr/share/lexmark/legacy/etc/99-lexmarklegacy-10.rules $pkgdir/etc/udev/rules.d/99-lexmarklegacy-10.rules
    ln -s /usr/share/lexmark/wsu_legacy/etc/99-lexmarklegacy-util.rules $pkgdir/etc/udev/rules.d/99-lexmarklegacy-util.rules
    
    ##dbus
    mkdir -p $pkgdir/etc/dbus-1/session.d
    ln -s /usr/share/lexmark/legacy/etc/umf.conf $pkgdir/etc/dbus-1/session.d/umf-legacy.conf
    install -Dm644 $srcdir/umf-legacy.service $pkgdir/usr/share/dbus-1/services/umf-legacy.service
    install -Dm755 $srcdir/umf.sh $pkgdir/usr/share/lexmark/legacy/bin/.scripts/umf.sh

    ##sane
    install -Dm644 ../etc/LexmarkLegacy_1_0_0.conf $pkgdir/etc/sane.d/LexmarkLegacy_1_0_0.conf
    install -Dm644 ../lib/libsane-LexmarkLegacy_1_0_0.so.1.0.18 $pkgdir/usr/lib/sane/libsane-LexmarkLegacy_1_0_0.so.1.0.18 
    ln -s /usr/lib/sane/libsane-LexmarkLegacy_1_0_0.so.1.0.18 $pkgdir/usr/lib/sane/libsane-LexmarkLegacy_1_0_0.so 
    ln -s /usr/lib/sane/libsane-LexmarkLegacy_1_0_0.so.1.0.18 $pkgdir/usr/lib/sane/libsane-LexmarkLegacy_1_0_0.so.1 

    ## desktop
    cd ../etc
    sed -i -e  's#/usr/local/lexmark/legacy/bin/.scripts/lxtoolbox#/usr/bin/lxtoolbox#' \
    -e 's#/usr/local/lexmark/legacy/etc/lxtoolbox_48x48.png#/usr/share/pixmaps/lxtoolbox_48x48.png#' \
    lxtoolbox.desktop
    install -Dm644 lxtoolbox.desktop $pkgdir/usr/share/applications/lxtoolbox.desktop
    install -Dm644 lxtoolbox_48x48.png  $pkgdir/usr/share/pixmaps/lxtoolbox_48x48.png
    
    ##fix owner and path
    chown -R root:root $pkgdir/usr
    find $pkgdir -type f -execdir sed -i 's#/usr/local/#/usr/share/#g' {} \;
}

