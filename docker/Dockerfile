# vim: ft=dockerfile
ARG REGISTRY_PREFIX=''
ARG QGIS_VERSION=release

FROM  ${REGISTRY_PREFIX}qgis-platform:${QGIS_VERSION}
MAINTAINER David Marteau <david.marteau@3liz.com>
LABEL Description="QGIS3 WPS service" Vendor="3liz.org" Version="1."

ARG PIP_OPTIONS
ARG BUILD_VERSION

RUN apt-get update && apt-get install -y --no-install-recommends gosu \
     python3-shapely  \
     python3-psutil \
     git \
     make \
     && apt-get clean  && rm -rf /var/lib/apt/lists/* \
     && rm -rf /usr/share/man 

# Create virtualenv for installing server
RUN mkdir -p /opt/local/ \
    && python3 -m venv --system-site-packages /opt/local/pyqgiswps && cd /usr/local/bin \
    && /opt/local/pyqgiswps/bin/pip install -U --no-cache-dir pip setuptools wheel \
    && /opt/local/pyqgiswps/bin/pip install --no-cache-dir \
        plotly \
        simplejson \
        geojson \
        scipy  \
        pandas \
        Jinja2 \ 
        wheel \
        flake8

# RUN add-apt-repository universe
# RUN apt-get install gcc libpq-dev -y
# RUN apt-get install python-dev  python-pip -y
# RUN apt-get install python3-dev python3-pip python3-venv python3-wheel -y

RUN git clone --recurse-submodules https://github.com/ElNiak/py-qgis-wps.git qgis-wps

WORKDIR /qgis-wps

RUN make all

RUN /opt/local/pyqgiswps/bin/pip install --no-cache-dir .   \
    && ln -s /opt/local/pyqgiswps/bin/wpsserver \
    && ln -s /opt/local/pyqgiswps/bin/wpsserver-check \
    && rm -rf /root/.cache /root/.ccache


COPY /docker-entrypoint.sh /
RUN chmod 0755 /docker-entrypoint.sh

EXPOSE 8080

ENV PROCESSING_PROVIDERS_MODULE_PATH=/qgis-wps/qgis-threejs

ENTRYPOINT ["/docker-entrypoint.sh"]


