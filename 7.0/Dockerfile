FROM centos/s2i-base-centos7

# This image provides an Apache+PHP environment for running PHP
# applications.

EXPOSE 8080
EXPOSE 8443

# Description
# This image provides an Apache 2.4 + PHP 7.0 environment for running PHP applications.
# Exposed ports:
# * 8080 - alternative port for http

ENV PHP_VERSION=7.0 \
    PHP_VER_SHORT=70 \
    NAME=mage

ENV SUMMARY="Platform for building and running PHP $PHP_VERSION applications" \
    DESCRIPTION="PHP $PHP_VERSION available as container is a base platform for \
building and running various PHP $PHP_VERSION applications and frameworks. \
PHP is an HTML-embedded scripting language. PHP attempts to make it easy for developers \
to write dynamically generated web pages. PHP also offers built-in database integration \
for several commercial and non-commercial database management systems, so writing \
a database-enabled webpage with PHP is fairly simple. The most common use of PHP coding \
is probably as a replacement for CGI scripts."

LABEL summary="${SUMMARY}" \
      description="${DESCRIPTION}" \
      io.k8s.description="${DESCRIPTION}" \
      io.k8s.display-name="Apache 2.4 with PHP ${PHP_VERSION}" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,${NAME},${NAME}${PHP_VER_SHORT},${NAME}${PHP_VER_SHORT}" \
      io.openshift.s2i.scripts-url="image:///usr/libexec/s2i" \
      io.s2i.scripts-url="image:///usr/libexec/s2i" \
      name="morozovgroup/${NAME}-${PHP_VER_SHORT}-centos7" \
      com.redhat.component="${NAME}${PHP_VER_SHORT}-container" \
      version="${PHP_VERSION}" \
      help="For more information visit https://github.com/sclorg/s2i-${NAME}-container" \
      usage="s2i build https://github.com/morozov-group/s2i-php-container.git --context-dir=${PHP_VERSION}/test/test-app morozovgroup/${NAME}-${PHP_VER_SHORT}-centos7 sample-server" \
      maintainer="Morozov Group <tech@morozov.group>"

# Install Apache httpd and PHP and nodejs 10
RUN yum install -y epel-release yum-utils && \
    yum install -y http://rpms.remirepo.net/enterprise/remi-release-7.rpm http://yum.newrelic.com/pub/newrelic/el5/x86_64/newrelic-repo-5-3.noarch.rpm && \
    yum-config-manager --enable epel && \
    yum-config-manager --enable remi && \
    yum-config-manager --enable remi-php70 && \
    yum remove -y ${NODEJS_SCL}\* && \
    curl -sL https://rpm.nodesource.com/setup_12.x | bash - && \
    INSTALL_PKGS="php php-cli php-mysqlnd  php-pgsql php-bcmath \
                  php-pecl-imagick php-pecl-memcached \
                  php-gd php-intl php-ldap php-mbstring php-pdo \
                  php-process php-soap php-opcache php-xml \
                  php-pear newrelic-php5 php-pecl-zip \
                  php-gmp php-pecl-apcu php-mcrypt mod_ssl nodejs msmtp" && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS --nogpgcheck && \
    rpm -V $INSTALL_PKGS && \
    yum clean all -y

ENV PHP_CONTAINER_SCRIPTS_PATH=/usr/share/container-scripts/php/ \
    APP_DATA=${APP_ROOT}/src \
    PHP_DEFAULT_INCLUDE_PATH=/usr/share/pear \
    PHP_SYSCONF_PATH=/etc \
    PHP_HTTPD_CONF_FILE=php.conf \
    HTTPD_CONFIGURATION_PATH=${APP_ROOT}/etc/conf.d \
    HTTPD_MAIN_CONF_PATH=/etc/httpd/conf \
    HTTPD_MAIN_CONF_D_PATH=/etc/httpd/conf.d \
    HTTPD_VAR_RUN=/var/run/httpd \
    HTTPD_DATA_PATH=/var/www \
    HTTPD_DATA_ORIG_PATH=/var/www \
    HTTPD_VAR_PATH=/var

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Copy extra files to the image.
COPY ./root/ /

RUN rm -rf /tmp/.newrelic.sock && \
    chmod 777 ${PHP_SYSCONF_PATH}/php.ini && \
    npm install -g gulp-cli grunt-cli && npm cache clean --force

# Reset permissions of filesystem to default values
RUN /usr/libexec/container-setup && rpm-file-permissions && touch /opt/app-root/etc/scl_enable

USER 1001

# Set the default CMD to print the usage of the language image
CMD $STI_SCRIPTS_PATH/usage
