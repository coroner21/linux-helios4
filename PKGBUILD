# ARMv7 Helios4
# Repository: https://github.com/coroner21/linux-helios4
# Maintainer: coroner21

buildarch=4

pkgbase=linux-helios4
_srcname=linux-5.2
_kernelname=${pkgbase#linux}
_desc="ARMv7 Helios4"
pkgver=5.2.5
pkgrel=1
rcnver=5.2.5
rcnrel=armv7-x3
arch=('armv7h')
url="http://www.kernel.org/"
license=('GPL2')
makedepends=('kmod' 'inetutils' 'bc' 'git' 'dtc')
options=('!strip')
source=("http://www.kernel.org/pub/linux/kernel/v5.x/${_srcname}.tar.xz"
        "http://www.kernel.org/pub/linux/kernel/v5.x/patch-${pkgver}.xz"
        "http://rcn-ee.com/deb/sid-armhf/v${rcnver}-${rcnrel}/patch-${rcnver%.0}-${rcnrel}.diff.gz"
        '0001-ARM-atags-add-support-for-Marvell-s-u-boot.patch'
        '0002-ARM-atags-fdt-retrieve-MAC-addresses-from-Marvell-bo.patch'
        '0003-SMILE-Plug-device-tree-file.patch'
        '0004-fix-mvsdio-eMMC-timing.patch'
        '0005-net-smsc95xx-Allow-mac-address-to-be-set-as-a-parame.patch'
        '0006-set-default-cubietruck-led-triggers.patch'
        '0007-exynos4412-odroid-set-higher-minimum-buck2-regulator.patch'
        '0008-ARM-dove-enable-ethernet-on-D3Plug.patch'
        'config'
        'linux.preset'
        '60-linux.hook'
        '90-linux.hook'
	'https://raw.githubusercontent.com/armbian/build/master/patch/kernel/mvebu-next/91-01-libata-add-ledtrig-support.patch'
	'https://raw.githubusercontent.com/armbian/build/master/patch/kernel/mvebu-next/91-02-Enable-ATA-port-LED-trigger.patch'
	'92-mvebu-gpio-remove-hardcoded-timer-assignment.patch'
	'https://raw.githubusercontent.com/armbian/build/master/patch/kernel/mvebu-next/92-mvebu-gpio-add_wake_on_gpio_support.patch'
	'https://raw.githubusercontent.com/armbian/build/master/patch/kernel/mvebu-next/94-helios4-dts-add-wake-on-lan-support.patch')
md5sums=('ddf994de00d7b18395886dd9b30b9262'
         '0bfed20acab24d45232983b54339a3c9'
         'ef32c9bf05aeff66073c09255a526b32'
         '2200b611940b67e43be161055c48e092'
         '74ee346c9c71145fa84f1a9c7f4bbea0'
         'e052bf1c5d21e6cd2890109dbbbd788d'
         '8dd34e9cf68282a9683a7dbce92f517c'
         '4ef1f863914c829334c1f35f1c8294e9'
         '1d66498df88eca2360ff3d3a640add48'
         '5761a8951e9dcb01b4f631c0d92f2e03'
         'd2dee481fdde72aea9111e93c090bd69'
         'a9cccb64b3c8ecf61fb4a833e3f371ed'
         '86d4a35722b5410e3b29fc92dae15d4b'
         'ce6c81ad1ad1f8b333fd6077d47abdaf'
         '3e2a512f8da5db5fe9f17875405e56a3'
         'a637eedf6a13d4c9f8cd024c13f6b62a'
         '779c884ae9854df32e8ced0e86359649'
         '5aa8f19e3d2e23e475282525f36fe454'
         'b338409db059f5a38bc333372223f1cc'
         '5876ccfe05a07b64661556ea4fae4b59')

prepare() {
  cd "${srcdir}/${_srcname}"
  # extract RCN-patch
  gzip -df ../patch-${rcnver%.0}-${rcnrel}.diff.gz

  # add upstream patch
  git apply --whitespace=nowarn ../patch-${pkgver}

  # RCN patch
  git apply ../patch-${rcnver%.0}-${rcnrel}.diff

  # ALARM patches
  git apply ../0001-ARM-atags-add-support-for-Marvell-s-u-boot.patch
  git apply ../0002-ARM-atags-fdt-retrieve-MAC-addresses-from-Marvell-bo.patch
  git apply ../0003-SMILE-Plug-device-tree-file.patch
  git apply ../0004-fix-mvsdio-eMMC-timing.patch
  git apply ../0005-net-smsc95xx-Allow-mac-address-to-be-set-as-a-parame.patch
  git apply ../0006-set-default-cubietruck-led-triggers.patch
  git apply ../0007-exynos4412-odroid-set-higher-minimum-buck2-regulator.patch
  git apply ../0008-ARM-dove-enable-ethernet-on-D3Plug.patch
  patch -Np1 < ../91-01-libata-add-ledtrig-support.patch
  patch -Np1 < ../91-02-Enable-ATA-port-LED-trigger.patch
  patch -Np1 < ../92-mvebu-gpio-remove-hardcoded-timer-assignment.patch
  patch -Np1 < ../92-mvebu-gpio-add_wake_on_gpio_support.patch
  patch -Np1 < ../94-helios4-dts-add-wake-on-lan-support.patch

  cat "${srcdir}/config" > ./.config

  # add pkgrel to extraversion
  sed -ri "s|^(EXTRAVERSION =)(.*)|\1 \2-${pkgrel}|" Makefile

  # don't run depmod on 'make install'. We'll do this ourselves in packaging
  sed -i '2iexit 0' scripts/depmod.sh
}

build() {
  cd "${srcdir}/${_srcname}"

  # get kernel version
  make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- prepare

  # load configuration
  # Configure the kernel. Replace the line below with one of your choice.
  #make menuconfig # CLI menu for configuration
  #make nconfig # new CLI menu for configuration
  #make xconfig # X-based configuration
  #make oldconfig # using old config from previous kernel version
  # ... or manually edit .config

  # Copy back our configuration (use with new kernel version)
  #cp ./.config ../${pkgbase}.config

  ####################
  # stop here
  # this is useful to configure the kernel
  #msg "Stopping build"
  #return 1
  ####################

  #yes "" | make config

  # build!
  make -j9 ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- zImage modules dtbs
}

_package() {
  pkgdesc="The Linux Kernel and modules - ${_desc}"
  depends=('coreutils' 'linux-firmware' 'kmod' 'mkinitcpio>=0.7')
  backup=("etc/mkinitcpio.d/${pkgbase}.preset")
  provides=('kernel26' "linux=${pkgver}")
  conflicts=('linux')
  replaces=('linux-mvebu' 'linux-udoo' 'linux-sun4i' 'linux-sun5i' 'linux-sun7i' 'linux-usbarmory' 'linux-wandboard' 'linux-clearfog')
  install=${pkgname}.install

  cd "${srcdir}/${_srcname}"

  KARCH=arm

  # get kernel version
  _kernver="$(make kernelrelease)"
  _basekernel=${_kernver%%-*}
  _basekernel=${_basekernel%.*}

  mkdir -p "${pkgdir}"/{boot,usr/lib/modules}
  make INSTALL_MOD_PATH="${pkgdir}/usr" modules_install
  make ARCH=arm INSTALL_DTBS_PATH="${pkgdir}/boot/dtbs" dtbs_install
  cp arch/$KARCH/boot/zImage "${pkgdir}/boot/zImage"

  # make room for external modules
  local _extramodules="extramodules-${_basekernel}${_kernelname}"
  ln -s "../${_extramodules}" "${pkgdir}/usr/lib/modules/${_kernver}/extramodules"

  # add real version for building modules and running depmod from hook
  echo "${_kernver}" |
    install -Dm644 /dev/stdin "${pkgdir}/usr/lib/modules/${_extramodules}/version"

  # remove build and source links
  rm "${pkgdir}"/usr/lib/modules/${_kernver}/{source,build}

  # now we call depmod...
  depmod -b "${pkgdir}/usr" -F System.map "${_kernver}"

  # sed expression for following substitutions
  local _subst="
    s|%PKGBASE%|${pkgbase}|g
    s|%KERNVER%|${_kernver}|g
    s|%EXTRAMODULES%|${_extramodules}|g
  "

  # install mkinitcpio preset file
  sed "${_subst}" ../linux.preset |
    install -Dm644 /dev/stdin "${pkgdir}/etc/mkinitcpio.d/${pkgbase}.preset"

  # install pacman hooks
  sed "${_subst}" ../60-linux.hook |
    install -Dm644 /dev/stdin "${pkgdir}/usr/share/libalpm/hooks/60-${pkgbase}.hook"
  sed "${_subst}" ../90-linux.hook |
    install -Dm644 /dev/stdin "${pkgdir}/usr/share/libalpm/hooks/90-${pkgbase}.hook"
}

_package-headers() {
  pkgdesc="Header files and scripts for building modules for linux kernel - ${_desc}"
  provides=("linux-headers=${pkgver}")
  conflicts=('linux-headers')
  replaces=('linux-mvebu-headers' 'linux-sun4i-headers' 'linux-sun5i-headers' 'linux-sun7i-headers' 'linux-usbarmory-headers' 'linux-wandboard-headers' 'linux-clearfog-headers')

  install -dm755 "${pkgdir}/usr/lib/modules/${_kernver}"

  cd "${srcdir}/${_srcname}"
  install -D -m644 Makefile \
    "${pkgdir}/usr/lib/modules/${_kernver}/build/Makefile"
  install -D -m644 kernel/Makefile \
    "${pkgdir}/usr/lib/modules/${_kernver}/build/kernel/Makefile"
  install -D -m644 .config \
    "${pkgdir}/usr/lib/modules/${_kernver}/build/.config"

  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/include"

  for i in acpi asm-generic config crypto drm generated keys linux math-emu \
    media net pcmcia scsi soc sound trace uapi video xen; do
    cp -a include/${i} "${pkgdir}/usr/lib/modules/${_kernver}/build/include/"
  done

  # copy arch includes for external modules
  mkdir -p ${pkgdir}/usr/lib/modules/${_kernver}/build/arch/$KARCH
  cp -a arch/$KARCH/include ${pkgdir}/usr/lib/modules/${_kernver}/build/arch/$KARCH/
  for i in dove exynos omap2; do
    mkdir -p ${pkgdir}/usr/lib/modules/${_kernver}/build/arch/$KARCH/mach-${i}
    cp -a arch/$KARCH/mach-${i}/include ${pkgdir}/usr/lib/modules/${_kernver}/build/arch/$KARCH/mach-${i}/
  done
  for i in omap orion samsung versatile; do
    mkdir -p ${pkgdir}/usr/lib/modules/${_kernver}/build/arch/$KARCH/plat-${i}
    cp -a arch/$KARCH/plat-${i}/include ${pkgdir}/usr/lib/modules/${_kernver}/build/arch/$KARCH/plat-${i}/
  done

  # copy files necessary for later builds, like nvidia and vmware
  cp Module.symvers "${pkgdir}/usr/lib/modules/${_kernver}/build"
  cp -a scripts "${pkgdir}/usr/lib/modules/${_kernver}/build"

  # fix permissions on scripts dir
  chmod og-w -R "${pkgdir}/usr/lib/modules/${_kernver}/build/scripts"
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/.tmp_versions"

  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/arch/${KARCH}/kernel"

  cp arch/${KARCH}/Makefile "${pkgdir}/usr/lib/modules/${_kernver}/build/arch/${KARCH}/"

  cp arch/${KARCH}/kernel/asm-offsets.s "${pkgdir}/usr/lib/modules/${_kernver}/build/arch/${KARCH}/kernel/"

  # add dm headers
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/md"
  cp drivers/md/*.h "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/md"

  # add inotify.h
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/include/linux"
  cp include/linux/inotify.h "${pkgdir}/usr/lib/modules/${_kernver}/build/include/linux/"

  # add wireless headers
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/net/mac80211/"
  cp net/mac80211/*.h "${pkgdir}/usr/lib/modules/${_kernver}/build/net/mac80211/"

  # add dvb headers for external modules
  # in reference to:
  # http://bugs.archlinux.org/task/11194
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/include/config/dvb/"
  cp include/config/dvb/*.h "${pkgdir}/usr/lib/modules/${_kernver}/build/include/config/dvb/"

  # add dvb headers for http://mcentral.de/hg/~mrec/em28xx-new
  # in reference to:
  # http://bugs.archlinux.org/task/13146
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/dvb-frontends/"
  cp drivers/media/dvb-frontends/lgdt330x.h "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/dvb-frontends/"
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/i2c/"
  cp drivers/media/i2c/msp3400-driver.h "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/i2c/"

  # add dvb headers
  # in reference to:
  # http://bugs.archlinux.org/task/20402
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/usb/dvb-usb"
  cp drivers/media/usb/dvb-usb/*.h "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/usb/dvb-usb/"
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/dvb-frontends"
  cp drivers/media/dvb-frontends/*.h "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/dvb-frontends/"
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/tuners"
  cp drivers/media/tuners/*.h "${pkgdir}/usr/lib/modules/${_kernver}/build/drivers/media/tuners/"

  # add xfs and shmem for aufs building
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/fs/xfs"
  mkdir -p "${pkgdir}/usr/lib/modules/${_kernver}/build/mm"

  # copy in Kconfig files
  for i in $(find . -name "Kconfig*"); do
    mkdir -p "${pkgdir}"/usr/lib/modules/${_kernver}/build/`echo ${i} | sed 's|/Kconfig.*||'`
    cp ${i} "${pkgdir}/usr/lib/modules/${_kernver}/build/${i}"
  done

  chown -R root.root "${pkgdir}/usr/lib/modules/${_kernver}/build"
  find "${pkgdir}/usr/lib/modules/${_kernver}/build" -type d -exec chmod 755 {} \;

  # strip scripts directory
  find "${pkgdir}/usr/lib/modules/${_kernver}/build/scripts" -type f -perm -u+w 2>/dev/null | while read binary ; do
    case "$(file -bi "${binary}")" in
      *application/x-sharedlib*) # Libraries (.so)
        /usr/bin/strip ${STRIP_SHARED} "${binary}";;
      *application/x-archive*) # Libraries (.a)
        /usr/bin/strip ${STRIP_STATIC} "${binary}";;
      *application/x-executable*) # Binaries
        /usr/bin/strip ${STRIP_BINARIES} "${binary}";;
    esac
  done

  # remove unneeded architectures
  rm -rf "${pkgdir}"/usr/lib/modules/${_kernver}/build/arch/{alpha,arc,arm26,arm64,avr32,blackfin,c6x,cris,frv,h8300,hexagon,ia64,m32r,m68k,m68knommu,metag,mips,microblaze,mn10300,openrisc,parisc,powerpc,ppc,s390,score,sh,sh64,sparc,sparc64,tile,unicore32,um,v850,x86,xtensa}
}

pkgname=("${pkgbase}")
for _p in ${pkgname[@]}; do
  eval "package_${_p}() {
    _package${_p#${pkgbase}}
  }"
done
