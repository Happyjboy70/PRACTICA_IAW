FROM httpd:2.4
RUN sed -i 's/Listen 80/Listen 8080/g' /usr/local/apache2/conf/httpd.conf
RUN echo "Include conf/extra/alpha-docs.conf" >> /usr/local/apache2/conf/httpd.conf
RUN echo "Include conf/extra/beta-docs.conf" >> /usr/local/apache2/conf/httpd.conf
RUN echo "Include conf/extra/default-catchall.conf" >> /usr/local/apache2/conf/httpd.conf
