#Maintainer: Andrzej Giniewicz <gginiu@gmail.com>
pkgname=tegra-toolkit
pkgver=1.0r5
_pkgver=1.0
_pkgdate=2012-05-14
_pkgbuild=12749879
pkgrel=2
pkgdesc="The Tegra Android Toolkit, tools, samples and documentation"
license=('custom')
arch=('i686' 'x86_64')
url="http://developer.nvidia.com/category/zone/mobile-development"

depends=('oprofile' 'gprof2dot-git' 'graphviz' 'android-sdk-platform-tools' 'android-udev' 'qt')
makedepends=('java-environment=6')
optdepends=('eog: default image viewer for oprofile-analyze')

[ "$CARCH" = "i686"   ] && _pkgarch="linux"
[ "$CARCH" = "x86_64" ] && _pkgarch="linux-x64"
source=("http://developer.download.nvidia.com/devzone/mobile/$pkgname-$_pkgver-$_pkgarch-$_pkgdate-$_pkgbuild.run"
        "oprofile-analyze.patch" "license")
[ "$CARCH" = "i686"   ] && md5sums=('0246e273b3ae89d2adac60be55688415')
[ "$CARCH" = "x86_64" ] && md5sums=('9ca7b4caf877ce0d2909e8647393c35c')

md5sums+=('65ae4180dfea679bd498394fdc73d691'
          '2ea4dc7733201cd7dbc5552342fa1d26')

options=(!strip)

build() {
  cd "${srcdir}"
  rm -rf "fakebin"
  mkdir "fakebin"
  cd "fakebin"
  touch apt-cache
  chmod a+x apt-cache
  touch apt-get
  chmod a+x apt-get
  echo "#!/bin/bash" > sudo
  echo "\$@" >> sudo
  chmod a+x sudo
  echo "#!/bin/bash" > xterm
  echo "\$2 \$3 \$4" >> xterm
  chmod a+x xterm
  echo "#!/bin/bash" > dpkg
  echo "cp \$2 \"$srcdir/\${2##*/}\"" >> dpkg
  chmod a+x dpkg
  cd ..
  oldPATH=$PATH
  export PATH="$srcdir/fakebin:$PATH"
  unset LD_PRELOAD
  rm -rf NVPACK
  rm -rf instinfo
  for i in {1..12}
  do
    echo >> instinfo
  done
  echo "y" >> instinfo # accept license
  echo "y" >> instinfo #  (oprofile scripts)
  echo "y" >> instinfo #  (samples)
  echo "y" >> instinfo #  (perfhud es)
  echo "y" >> instinfo #  (perf)
  echo "y" >> instinfo #  (documentation)
  echo "y" >> instinfo #  (perfhud es switches)
  echo "y" >> instinfo # information is correct
  echo "$srcdir/NVPACK" >> instinfo # extract to $srcdir
  chmod a+x ./$pkgname-$_pkgver-$_pkgarch-$_pkgdate-$_pkgbuild.run
  cat instinfo | ./$pkgname-$_pkgver-$_pkgarch-$_pkgdate-$_pkgbuild.run --mode text > /dev/null
  export PATH="$oldPATH"
  cd "$srcdir/NVPACK/oprofile"
  patch -p0 < ../../oprofile-analyze.patch
  cd "$srcdir/NVPACK"
  rm -f "docs/NVIDIA Debug Manager for Android NDK.pdf"
  cd "$srcdir"
  [ "$CARCH" = "i686"   ] && ar -x perfhudes_1.9-9_i386.deb
  [ "$CARCH" = "x86_64" ] && ar -x perfhudes_1.9-9_amd64.deb
}

package() {
  cd "$srcdir/NVPACK/oprofile"
  install -m 644 -D "abi/arm_abi" "$pkgdir/usr/share/tegra/abi/arm_abi"
  install -m 755 -D "oprofile-analyze.py" "$pkgdir/usr/bin/oprofile-analyze"
  cd "$srcdir/NVPACK/TDK_Samples"
  install -d "$pkgdir/usr/share/tegra/samples"
  cp -rf tegra_android_native_samples_v10p00/* "$pkgdir"/usr/share/tegra/samples
  cd "$srcdir/NVPACK"
  install -d "$pkgdir/usr/share/tegra/docs"
  cp -rf docs/* "$pkgdir/usr/share/tegra/docs"
  cd "$srcdir/NVPACK/perf/PERF"
  install -d "$pkgdir/usr/share/tegra/docs/perf-patches"
  install -m 644 -D perf "$pkgdir/usr/share/tegra/perf"
  cp -rf patch/* "$pkgdir"/usr/share/tegra/docs/perf-patches
  cd "$srcdir/NVPACK/perfhud_switch"
  install -m 755 -D enable_perfhud.sh "$pkgdir/usr/bin/enable_perfhud"
  install -m 755 -D disable_perfhud.sh "$pkgdir/usr/bin/disable_perfhud"
  cd "$srcdir"
  tar -xzf data.tar.gz -C "$pkgdir"
  [ "$CARCH" = "x86_64" ] && mv "$pkgdir/usr/lib64" "$pkgdir/usr/lib"
  install -m 644 -D license "$pkgdir/usr/share/licenses/$pkgname/license"
}

