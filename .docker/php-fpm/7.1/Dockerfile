FROM php:7.1-fpm

ARG DOCKER_USER
ARG DOCKER_USER_ID

RUN apt-get update -qq \
    && apt-get install -y --no-install-recommends \
        gettext-base openssh-client wget git make cmake libssl-dev libssl-doc libxml2-dev zlib1g-dev libcurl3-dev sudo libpq-dev \
    && pecl install xdebug \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && docker-php-ext-install -j$(nproc) pdo pdo_pgsql zip bcmath mbstring xml curl \
    && curl -sS http://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && composer -q global require "hirak/prestissimo:^0.3" \
    && composer -q global require "phing/phing:^2.16" \
    && ln -s /root/.composer/vendor/phing/phing/bin/phing /usr/bin/phing \
    && apt-get clean \
    && mkdir /nginx \
    && rm -rf /var/lib/apt/lists/* \
    && cd -

# Copy config
COPY ./config/ /docker/
ADD config/custom-php.ini /usr/local/etc/php/conf.d/custom-php.ini


# Add local user
RUN useradd --shell /bin/bash -u ${DOCKER_USER_ID} -o -c "" -m ${DOCKER_USER} \
    && adduser ${DOCKER_USER} sudo \
    && echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers \
    && export HOME=/home/${DOCKER_USER} \
    && mv /docker/.bashrc /home/${DOCKER_USER}/.bashrc \
    && chown ${DOCKER_USER}:${DOCKER_USER} /home/${DOCKER_USER}/.bashrc

ENTRYPOINT ["/bin/bash", "/docker/entrypoint.sh"]
CMD ["php-fpm"]
