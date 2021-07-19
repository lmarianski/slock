# Maintainer: Gaetan Bisson <bisson@archlinux.org>
# Contributor: Sergej Pupykin <pupykin.s+arch@gmail.com>
# Contributor: Sebastian A. Liem <sebastian at liem dot se>

pkgname=slock-git
_pkgname=slock
pkgver=20170325.35633d4
pkgrel=1
pkgdesc='Simple X display locker'
url='http://tools.suckless.org/slock'
arch=('i686' 'x86_64' 'armv7h')
license=('MIT')
makedepends=('git')
depends=('libxrandr')
source=('git://git.suckless.org/slock'
	https://tools.suckless.org/slock/patches/foreground-and-background/slock-foreground-and-background-20210611-35633d4.diff
	https://tools.suckless.org/slock/patches/dpms/slock-dpms-1.4.diff
	https://tools.suckless.org/slock/patches/control-clear/slock-git-20161012-control-clear.diff
	slock-dwmlogo-dpms-fix.diff
)
sha256sums=('SKIP'
            '6d2366d733a27e63a9261284d16ffffaa8fb3e0a578d170613ee6d9cea1aad93'
            '12af3ae1522d2423fb55d462896e1392797fd4073006a30a99d1b679535dbadf'
            'a6f248895aadeab6820c73ec428c1cfefc6fc55afe96d446e868a40d113647cc'
            'df1de60b36420c18e191da18d811eac6b84342733951b31e806d4def7e210ef6')

provides=("${_pkgname}")
conflicts=("${_pkgname}")

pkgver() {
	cd "${srcdir}/${_pkgname}"
	git log -1 --format='%cd.%h' --date=short | tr -d -
}

prepare() {
	cd "${srcdir}/${_pkgname}"
	sed '/group =/s/nogroup/nobody/' -i config.def.h

	patch --input "$srcdir/slock-dpms-1.4.diff" || echo
	patch --input "$srcdir/slock-foreground-and-background-20210611-35633d4.diff" || echo
	patch --input "$srcdir/slock-dwmlogo-dpms-fix.diff" || echo
	patch --input "$srcdir/slock-git-20161012-control-clear.diff" || echo

	sed \
		-e 's/CPPFLAGS =/CPPFLAGS +=/g' \
		-e 's/CFLAGS =/CFLAGS +=/g' \
		-e 's/LDFLAGS =/LDFLAGS +=/g' \
		-i config.mk

	# This package provides a mechanism to provide a custom config.h. Multiple
	# configuration states are determined by the presence of two files in
	# $BUILDDIR:
	#
	# config.h  config.def.h  state
	# ========  ============  =====
	# absent    absent        Initial state. The user receives a message on how
	#                         to configure this package.
	# absent    present       The user was previously made aware of the
	#                         configuration options and has not made any
	#                         configuration changes. The package is built using
	#                         default values.
	# present                 The user has supplied his or her configuration. The
	#                         file will be copied to $srcdir and used during
	#                         build.
	#
	# After this test, config.def.h is copied from $srcdir to $BUILDDIR to
	# provide an up to date template for the user.
	if [ -e "$BUILDDIR/config.h" ]
	then
		cp "$BUILDDIR/config.h" .
	elif [ ! -e "$BUILDDIR/config.def.h" ]
	then
		msg='This package can be configured in config.h. Copy the config.def.h '
		msg+='that was just placed into the package directory to config.h and '
		msg+='modify it to change the configuration. Or just leave it alone to '
		msg+='continue to use default values.'
		echo "$msg"
	fi
	cp config.def.h "$BUILDDIR"	
}

build() {
	cd "${srcdir}/${_pkgname}"
	make X11INC=/usr/include/X11 X11LIB=/usr/lib/X11
}

package() {
	cd "${srcdir}/${_pkgname}"
	make PREFIX=/usr DESTDIR="${pkgdir}" install
	install -Dm644 LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}
