# ARMv7 Helios4
# Repository: https://github.com/coroner21/linux-helios4
# Maintainer: coroner21

buildarch=4

pkgbase=linux-helios4
_srcname=linux-5.4
_kernelname=${pkgbase#linux}
_desc="ARMv7 Helios4"
pkgver=5.4.51
pkgrel=1
arch=('armv7h')
url="http://www.kernel.org/"
license=('GPL2')
makedepends=('kmod' 'inetutils' 'bc' 'git' 'dtc')
options=('!strip')
source=("http://www.kernel.org/pub/linux/kernel/v5.x/${_srcname}.tar.xz"
        "http://www.kernel.org/pub/linux/kernel/v5.x/patch-${pkgver}.xz"
        'config'
        'linux.preset'
        '60-linux.hook'
        '90-linux.hook'
        '91-01-libata-add-ledtrig-support.patch'
        '91-02-Enable-ATA-port-LED-trigger.patch'
        '92-mvebu-gpio-remove-hardcoded-timer-assignment.patch'
        '92-mvebu-gpio-add_wake_on_gpio_support.patch'
        '94-helios4-dts-add-wake-on-lan-support.patch'
	'800-Add_Armada_38x_support_for_clk-cpu.patch'
	'801-Use_shorter_register_definition_in_pmsu_c.patch'
	'802-Made_the_dynamic_frequency_scaling_support_more_generic.patch'
	'803-Armada_38x_Add_dynamic_frequency_scaling_support_in_pmsu.patch'
	'804-Update_Armada_38x_DT_for_dynamic_frequency_scaling.patch')
md5sums=('ce9b2d974d27408a61c53a30d3f98fb9'
         '0513c6ce889123c98f06c507f0e2517a'
         '554b92936cc2f59b3975f84f759f39ed'
         '86d4a35722b5410e3b29fc92dae15d4b'
         'ce6c81ad1ad1f8b333fd6077d47abdaf'
         '3e2a512f8da5db5fe9f17875405e56a3'
         'f2d6724b72663f251c7ccdaa3c205dc8'
         'b9a900b7da3c9a1a9d4b8d86db3f7c94'
         '64545be23c2e30d9e06fc0809eed05dc'
         '955982bda46fa0955b2dd5ea152421d2'
         '5876ccfe05a07b64661556ea4fae4b59'
         'ee5b72655f4e2f2dcd82744047d5b326'
         'dda2f8d133be50e6ae2a57cd5fcce92d'
         'bc24f08b79adb1e1bd82e836c0ac3b32'
         '160be0eee4a7c7b51fd3a5b115ff4322'
         '11dd8dc3ab1836bd7f9c78547d70a7dc')

prepare() {
  cd "${srcdir}/${_srcname}"

  # add upstream patch
  patch -Np1 < ../patch-${pkgver}

  # Patches specifically for helios4
  patch -Np1 < ../91-01-libata-add-ledtrig-support.patch
  patch -Np1 < ../91-02-Enable-ATA-port-LED-trigger.patch
  patch -Np1 < ../92-mvebu-gpio-remove-hardcoded-timer-assignment.patch
  patch -Np1 < ../92-mvebu-gpio-add_wake_on_gpio_support.patch
  patch -Np1 < ../94-helios4-dts-add-wake-on-lan-support.patch
  patch -Np1 < ../800-Add_Armada_38x_support_for_clk-cpu.patch
  patch -Np1 < ../801-Use_shorter_register_definition_in_pmsu_c.patch
  patch -Np1 < ../802-Made_the_dynamic_frequency_scaling_support_more_generic.patch
  patch -Np1 < ../803-Armada_38x_Add_dynamic_frequency_scaling_support_in_pmsu.patch
  patch -Np1 < ../804-Update_Armada_38x_DT_for_dynamic_frequency_scaling.patch

  cat "${srcdir}/config" > ./.config

  # add pkgrel to extraversion
  sed -ri "s|^(EXTRAVERSION =)(.*)|\1 \2-${pkgrel}|" Makefile

  # don't run depmod on 'make install'. We'll do this ourselves in packaging
  sed -i '2iexit 0' scripts/depmod.sh
}

build() {
  cd "${srcdir}/${_srcname}"

  # get kernel version
  make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabihf- prepare

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
  make -j9 ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabihf- zImage modules dtbs
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

pkgname=("${pkgbase}")
for _p in ${pkgname[@]}; do
  eval "package_${_p}() {
    _package${_p#${pkgbase}}
  }"
done
