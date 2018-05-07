# Zonemaster Engine packaging

How to build and upload deb packages to packages1.


## Setup variables

This instruction uses two environment variables.

 * VERSION - Upstream version.
 * REV - Packaging revision. Reset to "iis1" for each new VERSION.

For releases from the develop branch a suffix must be appended to the
VERSION variable. Use the pattern "V-N-C" where V is the last upstream
release version, N is the number of commits since that release and C is
the shortsha of the latest commit. E.g. "1.0.2-13-8e093fc".

    VERSION=2.0.6
    REV=iis1


## Download dist tarball

This step is only applicable when packaging a proper upstream release.
Otherwise follow the upstream instructions for building the dist tarball.

    wget http://search.cpan.org/CPAN/authors/id/Z/ZN/ZNMSTR/Zonemaster-Engine-v${VERSION}.tar.gz


## Create original tarball

    tar xzf Zonemaster-Engine-v${VERSION/%-*/}.tar.gz
    mv Zonemaster-Engine-v${VERSION/%-*/} libzonemaster-engine-perl-${VERSION}
    tar czf ../libzonemaster-engine-perl_${VERSION}.orig.tar.gz libzonemaster-engine-perl-${VERSION}
    rm -rf libzonemaster-engine-perl-${VERSION}
    rm -f Zonemaster-Engine-v${VERSION/%-*/}.tar.gz


## Extract original tarball

    tar xzf ../libzonemaster-engine-perl_${VERSION}.orig.tar.gz --strip-components 1 --exclude .gitignore


## Build source package

    dpkg-buildpackage -S -k92388F75


## Build binary packages

    sudo DIST=precise cowbuilder --build ../libzonemaster-engine-perl_${VERSION}-${REV}.dsc


## Upload packages

    sudo -ubuild dput packages1-pdt ~/packages/libzonemaster-engine-perl_${VERSION}-${REV}_amd64.changes


## Tag release

    git tag ${VERSION}-${REV}
    git push --tags
