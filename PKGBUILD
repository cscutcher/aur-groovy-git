# $Id: PKGBUILD 181606 2016-06-29 10:54:52Z arodseth $
# Maintainer: Alexander F Rødseth <xyproto@archlinux.org>
# Contributor: Musikolo <musikoloAThotmailDOTcom>
# Contributor: Ruben Fonseca <fonseka@gmail.com>
# Contributor: Andreas W. Hauser <andy-aur@splashground.de>

pkgname=groovy-git
pkgver=r13422.e5087ce
pkgrel=1
pkgdesc='Programming language based on Java, inspired by Python, Ruby and Smalltalk'
arch=('any')
url='http://groovy-lang.org/'
license=('APACHE')
depends=('bash' 'java-environment')
optdepends=('groovy-docs: html and pdf documentation for Groovy')
makedepends=('gendesk' 'git')
conflicts=('groovy')
source=('groovy-src::git+https://github.com/apache/groovy.git#branch=master'
        "groovy.png::https://raw.githubusercontent.com/apache/incubator-groovy/master/subprojects/groovy-console/src/main/resources/groovy/ui/ConsoleIcon.png")
sha256sums=('SKIP'
            '24e539235b5762b1220f39d7953a52e9b4a458c2be4f66a60219755363ba6dba')

pkgver() {
  cd "groovy-src"
  printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

prepare() {
  # Build dist packages
  pushd "groovy-src"
  ./gradlew dist
  popd

  # Unzip binaries
  rm -rf "groovy-dist" || true
  unzip "groovy-src/target/distributions/apache-groovy-binary-2.5.0-SNAPSHOT.zip" 
  mv groovy-2.5.0-SNAPSHOT "groovy-dist"

  # Generate desktop shortcut
  gendesk -f -n --pkgname "$pkgname" --pkgdesc 'Groovy programming language' \
    --exec 'groovyConsole' --name 'Groovy Console'

  # Set GROOVY_HOME correctly at the top of all the scripts.
  # There are probably better ways to do this, but this works.
  for f in "groovy-dist/bin/"*; do
    sed 's:bin/sh:bin/sh\nGROOVY_HOME=/usr/share/groovy:' -i "$f"
  done

  cd "groovy-dist/embeddable"
  ln -s "groovy-all-2.5.0-SNAPSHOT.jar" "$pkgname-all.jar"
}

package() {
  cd "groovy-dist"

  # Create the directories and package the files
  install -d "$pkgdir/usr/share/groovy" "$pkgdir/usr/bin"
  cp -r lib conf embeddable "$pkgdir/usr/share/groovy"
  cp bin/* "$pkgdir/usr/bin"

  # Remove all DOS/Windows batch files
  find $pkgdir -name '*.bat' -exec rm {} \;

  # Package the license file
  install -Dm644 LICENSE \
    "$pkgdir/usr/share/licenses/$pkgname/LICENSE"

  # Package the desktop shortcut for Groovy Console
  install -Dm644 "$srcdir/groovy.desktop" \
    "$pkgdir/usr/share/applications/groovy.desktop"
  install -Dm644 "$srcdir/groovy.png" \
    "$pkgdir/usr/share/pixmaps/groovy.png"
}

# getver: groovy-lang.org/changelogs.html
# vim:set ts=2 sw=2 et:
