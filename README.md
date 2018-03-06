# ELK USG

On your Linux machine where you have [docker](https://www.docker.com/) and [go](https://golang.org/).

## Clone the repository
```bash
git clone https://github.com/caglar10ur/elk-usg.git ~/elk-usg
```

## Build the docker container
```bash
docker build -t elk-geoip ~/elk-usg/geoip/
```

## Build beats for MIPS64 and put them under ~/elk-usg/
```bash

mkdir -p ~/go/src/github.com/elastic/

git clone -b v6.2.2 https://github.com/elastic/beats.git ~/go/src/github.com/elastic/beats
pushd  ~/go/src/github.com/elastic/beats/filebeat
GOOS=linux GOARCH=mips64 go build -o ~/elk-usg/filebeat/filebeat
popd

pushd  ~/go/src/github.com/elastic/beats/metricbeat
GOOS=linux GOARCH=mips64 go build -o ~/elk-usg/metricbeat/metricbeat
popd
```

## Start the container
```bash
docker run -p 5601:5601 -p 9200:9200 -e LOGSTASH_START=0 -e TZ="America/Los_Angeles" -d --name elk-usg elk-geoip
```

## Copy ~/elk-usg to USG
```bash
scp -pr ~/elk-usg/ admin@192.168.1.1:
```

## Register metricbeat template and dashboard (change ELK_HOST to your hostname)
```bash
ELK_HOST=snow.skynet
docker run --link elk-usg:$ELK_HOST docker.elastic.co/beats/metricbeat:6.2.2 setup --template -E output.elasticsearch.hosts=["$ELK_HOST:9200"]
docker run --link elk-usg:$ELK_HOST docker.elastic.co/beats/metricbeat:6.2.2 setup --dashboards -E output.elasticsearch.hosts=["$ELK_HOST:9200"] -E setup.kibana.host=$ELK_HOST:5601
```

## SSH to USG
```bash
ssh 192.168.1.1 -l admin
```

## Edit filebeat.yml and metricbeat.yml (change ELK_HOST to your hostname)
```bash
ELK_HOST=snow.skynet
sed -i -e "s:snow.skynet:$ELK_HOST:g" /home/admin/elk-usg/filebeat/filebeat.yml
sed -i -e "s:snow.skynet:$ELK_HOST:g" /home/admin/elk-usg/metricbeat/metricbeat.yml
```

## Start beats
```bash
nohup /home/admin/elk-usg/filebeat/filebeat run -c /home/admin/elk-usg/filebeat/filebeat.yml >/dev/null 2>&1 &
nohup /home/admin/elk-usg/metricbeat/metricbeat run -c /home/admin/elk-usg/metricbeat/metricbeat.yml >/dev/null 2>&1 &
```
