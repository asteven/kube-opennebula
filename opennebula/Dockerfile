FROM ubuntu:18.04 as builder

# Install build requirements
RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections \
 && apt-get update \
 && apt-get install -y pbuilder debhelper ubuntu-dev-tools bash-completion \
        bison default-jdk flex javahelper libxmlrpc3-client-java \
        libxmlrpc3-common-java libxml2-dev ruby scons \
        ruby-ronn npm rename curl \
 && apt-get clean

# Retrive sources
ARG RELEASE=5.6.2
RUN curl -L https://github.com/OpenNebula/one/archive/release-${RELEASE}.tar.gz | tar xzf - \
 && mv /one-release-${RELEASE} /opennebula-${RELEASE}

# Build manual pages
RUN cd /opennebula-${RELEASE}/share/man \
 && ./build.sh

# Build sunstone dependencies
RUN cd /opennebula-${RELEASE}/src/sunstone/public \
 && npm install -g bower grunt grunt-cli \
 && npm install \
 && bower install --allow-root --config.interactive=false \
 && grunt sass \
 && grunt requirejs \
 && rm -rf node_modules/

# Create sources archive
RUN tar -czf /opennebula-${RELEASE}.tar.gz /opennebula-${RELEASE}

# Download packages scripts
RUN curl -L https://github.com/OpenNebula/packages/archive/master.tar.gz | tar xzf - \
 && mv packages-master packages

# Build packages
RUN /packages/ubuntu1804.sh opennebula-${RELEASE}.tar.gz
