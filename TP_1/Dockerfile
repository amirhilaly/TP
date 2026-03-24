FROM ubuntu

RUN apt update -y
RUN apt install -y apache2

COPY apache_conf.conf /etc/apache2/apache2.conf
COPY index.html /var/www/html/index.html

CMD [ "/usr/sbin/apache2", "-D", "FOREGROUND" ]