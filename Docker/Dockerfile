FROM arm32v6/alpine

ENV TZ UTC

RUN apk add --no-cache \
	apache2 php7-apache2 \
	php7 php7-curl php7-gmp php7-intl php7-mbstring php7-xml php7-zip \
	php7-ctype php7-dom php7-fileinfo php7-iconv php7-json php7-session php7-simplexml php7-xmlreader php7-zlib \
	php7-pdo_sqlite php7-pdo_mysql php7-pdo_pgsql

RUN mkdir -p /var/www/FreshRSS /run/apache2/
WORKDIR /var/www/FreshRSS

COPY . /var/www/FreshRSS
COPY ./Docker/*.Apache.conf /etc/apache2/conf.d/

RUN rm -f /etc/apache2/conf.d/languages.conf /etc/apache2/conf.d/info.conf \
		/etc/apache2/conf.d/status.conf /etc/apache2/conf.d/userdir.conf && \
	sed -r -i "/^\s*LoadModule .*mod_(alias|autoindex|negotiation|status).so$/s/^/#/" \ 
		/etc/apache2/httpd.conf && \
	sed -r -i "/^\s*#\s*LoadModule .*mod_(deflate|expires|headers|mime|setenvif).so$/s/^\s*#//" \ 
		/etc/apache2/httpd.conf && \
	sed -r -i "/^\s*(CustomLog|ErrorLog|Listen) /s/^/#/" \
		/etc/apache2/httpd.conf && \
	echo "17,37 * * * * su apache -s /bin/sh -c 'php /var/www/FreshRSS/app/actualize_script.php' 2>> /proc/1/fd/2 > /tmp/FreshRSS.log" >> \
		/var/spool/cron/crontabs/root

ENV COPY_SYSLOG_TO_STDERR On
ENV CRON_MIN ''
ENTRYPOINT ["./Docker/entrypoint.sh"]

EXPOSE 80
CMD ([ -z "$CRON_MIN" ] || crond -d 6) && \
	exec httpd -D FOREGROUND
