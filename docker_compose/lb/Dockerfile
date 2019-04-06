FROM debian:jessie
RUN apt-get update
RUN apt-get install -y apache2
RUN touch /var/log/apache2/error.log
EXPOSE 80
ENTRYPOINT /etc/init.d/apache2 start && tail -f /var/log/apache2/access.log