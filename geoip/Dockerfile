FROM sebp/elk

ENV ES_HOME /opt/elasticsearch
WORKDIR ${ES_HOME}

RUN CONF_DIR=/etc/elasticsearch gosu elasticsearch bin/elasticsearch-plugin \
            install ingest-geoip

