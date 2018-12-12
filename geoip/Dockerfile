FROM sebp/elk:651

ENV ES_HOME /opt/elasticsearch
WORKDIR ${ES_HOME}

RUN yes | CONF_DIR=/etc/elasticsearch gosu elasticsearch bin/elasticsearch-plugin \
            install ingest-geoip

