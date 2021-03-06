#
# $Id$
#
VERSION=0.4.13
SUITE=precise
KEY=$(shell if test `id -nu` = wjg; then echo BA4B79B2; else echo 063FFBAE; fi)

all: translations

install: all
	$(MAKE) dist; \
	cd dist; \
	tar xvf uckflow-$(VERSION).tar.gz; \
	cd uckflow-$(VERSION); \
	sudo python ./setup.py install

# Description of python to dpkg conversion: http://pypi.python.org/pypi/stdeb
# The next needs the following packages installed:
#	sudo apt-get install python-pip python-distutils-extra python-setuptools
# plus it needs the python debian package generator installed by:
#	sudo pip install stdeb
sdist: all
	rm -rf dist
	sed 's/@SUITE@/$(SUITE)/g' <stdeb.cfg.tmpl >/tmp/stdeb.cfg
	sed 's/@VERSION@/$(VERSION)/g' <setup.py.tmpl >setup.py
	python setup.py sdist
	cd dist; py2dsc -x /tmp/stdeb.cfg uckflow-$(VERSION).tar.gz
	cd dist/deb_dist/uckflow-$(VERSION); \
		dpkg-buildpackage -S -rfakeroot -k$(KEY)

upload: sdist
	cd dist/deb_dist; dput ppa:uck-team/uck-unstable *.changes

dist: all
	sed 's/@SUITE@/$(SUITE)/g' <stdeb.cfg.tmpl >/tmp/stdeb.cfg
	sed 's/@VERSION@/$(VERSION)/g' <setup.py.tmpl >setup.py
	python setup.py sdist
	cd dist; py2dsc -x /tmp/stdeb.cfg uckflow-$(VERSION).tar.gz
	cd dist/deb_dist/uckflow-$(VERSION); \
		dpkg-buildpackage -rfakeroot -uc -us
	mv dist/deb_dist/*.deb .
	mv dist/uckflow*.tar.gz .

clean:
	rm -f src/*.bak src/*.py[co] src/uckFlow.glade.h uckFlow.glade.h
	rm -f glademessage.pot stdeb.cfg setup.py
	sudo rm -rf dist locale

distclean: clean
	rm -f *.gz *.deb

messages.pot: src/uckFlow.glade uck-flow src/*.py
	xgettext -L python -k_ -kN_ -o messages.pot uck-flow src/*.py
	intltool-extract --type=gettext/glade src/uckFlow.glade
	mv src/uckFlow.glade.h .
	xgettext -k_ -kN_ -o glademessages.pot *.h
	echo "" >>messages.pot
	sed <glademessages.pot >>messages.pot '1,/^$$/d'
	rm -f uckFlow.glade.h glademessages.pot
	# Add new language: "LANG=xx_YY msginit"
	# Merge changes: "msgmerge -U xx.po messages.pot"

translations:
	for i in de; do \
		mkdir -p locale/$$i/LC_MESSAGES/; \
		msgfmt $$i.po -o locale/$$i/LC_MESSAGES/uckFlow.mo; \
	done

FRC:
