FROM daturafarms/slackdock
MAINTAINER Ted Ford <daturafarms@gmail.com>

RUN slackpkg update
RUN slackpkg -batch=on -default_answer=y install httpd apr-util sqlite cyrus-sa>
RUN chmod +x /etc/rc.d/rc.httpd
EXPOSE 80
VOLUME ["/var/www","/etc/httpd","/var/log/httpd"]
ENTRYPOINT ["sh","-c", "/usr/sbin/httpd -DFOREGROUND"]
# docker build -t {your_name}/slackdock-httpd
# docker run -p 6666:80 daturafarms/slackdock-httpd 
