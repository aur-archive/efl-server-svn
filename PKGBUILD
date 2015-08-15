pkgname=('efl-server-svn')
pkgver=80222
pkgrel=2
pkgdesc="EFLs are a serie of libraries for the Enlightenement project. This package will only provides whats needed for a server."
arch=('i686', 'x86_64')
url="http://www.enlightenment.org"
license=('BSD' 'LGPL2.1')
depends=('gnutls' 'glibc' 'libjpeg-turbo')
makedepends=('subversion')
conflicts=('eet' 'eet-svn' 'eina' 'eina-svn' 'embryo' 'embryo-svn'
           'ecore' 'ecore-svn' 'evas' 'evas-svn' 'eio' 'eio-svn')
provides=('eet' 'eet-svn' 'eina' 'eina-svn' 'ecore' 'ecore-svn' 'eio' 'eio-svn')
options=('!libtool' '!emptydirs')
source=()
md5sums=()

_svntrunk="http://svn.enlightenment.org/svn/e/trunk/efl"
_svnmod="efl"


build()
{
  cd $srcdir

  if [ $NOEXTRACT -eq 0 ]; then
    msg "Connecting to $_svntrunk SVN server...."
    if [ -d $_svnmod/.svn ]; then
      (cd $_svnmod && svn up -r $pkgver)
    else
      svn co $_svntrunk --config-dir ./ -r $pkgver $_svnmod
    fi

    msg "SVN checkout done or server timeout"
    msg "Starting make..."

  fi
  cp -r $_svnmod $_svnmod-build
  cd $_svnmod-build

  # We disable all the things we can to speed up compilation a little
  # But it still is too slow due to ecore-x, evas ...
  ./autogen.sh --prefix=/usr                                                  \
               --disable-ecore-x-composite                                    \
               --disable-ecore-x-cursor                                       \
               --disable-ecore-x-damage                                       \
               --disable-ecore-x-dpms                                         \
               --disable-ecore-x-dri                                          \
               --disable-ecore-x-input                                        \
               --disable-ecore-x-randr                                        \
               --disable-ecore-x-render                                       \
               --disable-ecore-x-screensaver                                  \
               --disable-ecore-x-shape                                        \
               --disable-ecore-x-sync                                         \
               --disable-ecore-x-xfixes                                       \
               --disable-ecore-x-xinerama                                     \
               --disable-ecore-x-xprint                                       \
               --disable-ecore-x-xtest
  make || return 1
}

package()
{
   cd $srcdir
   cd $_svnmod-build

   install -Dm644 $srcdir/$_svnmod/COPYING \
   $pkgdir/usr/share/licenses/$pkgname/COPYING

   make DESTDIR="$pkgdir" install

   # We remove all the useless stuff for a damn server!
   rm -r $pkgdir/usr/libexec
   rm -r $pkgdir/usr/lib/{ecore,evas}
   
   if [ -e $pkgdir/usr/lib/libecore_sdl.so ]; then
      rm $pkgdir/usr/lib/libecore_sdl*
      rm $pkgdir/usr/lib/pkgconfig/ecore-sdl.pc
      rm $pkgdir/usr/include/ecore-1/Ecore_Sdl.h
   fi

   if [ -e $pkgdir/usr/lib/libecore_x.so ]; then
      rm $pkgdir/usr/lib/libecore_x*
      rm $pkgdir/usr/lib/pkgconfig/ecore-x.pc
      rm $pkgdir/usr/include/ecore-1/Ecore_X_Atoms.h
      rm $pkgdir/usr/include/ecore-1/Ecore_X_Cursor.h
      rm $pkgdir/usr/include/ecore-1/Ecore_X.h
   fi

   rm $pkgdir/usr/lib/lib{ecore_{evas,fb,imf,input},embryo,evas}*
   rm $pkgdir/usr/lib/pkgconfig/ecore-{evas,fb,imf-evas,imf,input-evas,input}.pc
   rm $pkgdir/usr/lib/pkgconfig/evas.pc
   rm $pkgdir/usr/lib/pkgconfig/evas-{fb,opengl-x11,software-buffer,software-x11,wayland-shm}.pc
   rm $pkgdir/usr/bin/evas*
   rm $pkgdir/usr/include/ecore-1/Ecore_{Evas,Fb,IMF_Evas,IMF,Input_Evas,Input}.h
   rm -r $pkgdir/usr/include/evas-1/
   rm -r $pkgdir/usr/share/evas

   rm $pkgdir/usr/lib/*.a
   rm -r $pkgdir/usr/include
   rm -r $pkgdir/usr/lib/pkgconfig
   rm -r $pkgdir/usr/share/embryo
   rm -r $pkgdir/usr/bin/embryo_cc

   rm -r $startdir/src/$_svnmod-build
}
