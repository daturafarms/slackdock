#slackdocker, written by Ted Ford


FROM daturafarms/slackdock
MAINTAINER daturafarms <daturafarms@gmail.com>
ARG DEV_UID=1000
ARG DEV_GID=1000
#ARG DEV_USER=limited
ARG REPO="http://slackware.osuosl.org/slackware64-current/"
RUN echo "${REPO}" > /etc/slackpkg/mirrors
#RUN chmod -R 777 /tmp
#RUN yes y | slackpkg update && \
#   slackpkg install-new && \
#    slackpkg upgrade-all -default_answer=yes -batch=yes && \
#    slackpkg install -default_answer=yes -batch=yes a/* ap/* n/* d/* k/* kde*/ l/* y/*

#RUN groupadd -g "${DEV_GID}" "${DEV_USER}" ; \
#    useradd -m -u "${DEV_UID}" -g "${DEV_GID}" -G wheel "${DEV_USER}" && \
#    sed -ri 's/^# (%wheel.*NOPASSWD.*)$/\1/' /etc/sudoers

#install slackpkg+, adds extra repositories to the built-in package system
#RUN wget http://ufpr.dl.sourceforge.net/project/slackpkgplus/slackpkg%2B-1.7.0-noarch-13mt.txz && installpkg slackpkg+-1.7.0-noarch-13mt.txz && rm slackpkg+-1.7.0-noarch-13mt.txz

#add repositories
#RUN echo "MIRRORPLUS['slackonly']=https://slackware.nl/people/alien/sbrepos/" >> /etc/slackpkg/slackpkgplus.conf
#RUN sed -i '/^REPOPLUS/s/)/slackonly )/' /etc/slackpkg/slackpkgplus.conf

# install dependencies
#RUN yes y | slackpkg update gpg && slackpkg update
RUN slackpkg update
RUN \	
	slackpkg -batch=on -default_answer=y install sasl rsync-* glibc-* nettle libtasn1 perl p11-kit lzlib gd && \
	slackpkg -batch=on -default_answer=y install autoconf automake binutils gettext-tools intltool kernel-headers libtool m4 make libmpc gcc gcc-[0-9] gcc-g[+] && \
	slackpkg -batch=on -default_answer=y install pcre zlib libxslt libxml2 shadow ruby ca-certificates xxHash guile nghttp2 gc git php gdbm brotli libxslt libX11 libxcb libXau libXdmcp xtrans libXau libXt libSM libICE libXpm libxcb && \
	slackpkg -batch=on -default_answer=y install aaa_libraries fontconfig freetype graphite2 harfbuzz libXpm libwebp libxcb libxml2 y/* nano vim bzip2 glib2 libjpeg-turbo libpng libtiff pcre xz zlib zstd && \
	slackpkg -batch=on -default_answer=y install e2fsprogs libnsl e2fsprogs util-linux && \
	ln -s /lib64/liblzma.so.5 /lib64/liblz4.so.1 && \
	ldconfig
#RUN echo "REPO_NAME=SBo-git" >> ${ROOT}/root/.sbopkg.conf && \
#	echo "REPO_BRANCH=15.0" >> ${ROOT}/root/.sbopkg.conf

# update sbopkg and build
RUN yes y | \
	mkdir -p /tmp/install && cd /tmp/install && \
	wget -c https://github.com/sbopkg/sbopkg/releases/download/0.38.2/sbopkg-0.38.2-noarch-1_wsr.tgz && \
	installpkg sbopkg-0.38.2-noarch-1_wsr.tgz && \
	mkdir -p /var/lib/sbopkg/SBo/15.0 && \
	mkdir -p /var/lib/sbopkg/queues && \
	mkdir -p /var/cache/sbopkg && \
	mkdir -p /var/log/sbopkg && \
	mkdir -p /tmp/SBo && \
	sbopkg -r -e continue
## temporary, use following to add in needed packages for testing
  RUN \
	sbopkg -B -i sbotools -e continue && \
	sbosnap fetch
RUN \
	sboinstall -r GeoIP && \
	groupadd -g 8080 www-data && \
	useradd -g 8080 -u 8080 -M -s /bin/false www-data && \
	USE_GEOIP=yes NGINXUSER=www-data NGINXGROUP=www-data sboinstall -r nginx && \
	chown -R www-data:www-data /var/lib/nginx

# make rc.ngnix executable
RUN chmod +x /etc/rc.d/rc.nginx

# copy config file
COPY ./nginx.conf /etc/nginx/nginx.conf

# mount directories
VOLUME ["/etc/nginx", "/etc/nginx/conf.d", "/var/lib/nginx"]

# port 80 for now
EXPOSE 80

# start nginx
ENTRYPOINT /etc/rc.d/rc.nginx start
USER "${DEV_USER}"
ENV HOME /home/"${DEV_USER}"
WORKDIR /home/"${DEV_USER}"

CMD bash -l
