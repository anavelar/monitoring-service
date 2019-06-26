FROM docker.elastic.co/logstash/logstash:7.1.1

# Removes pipeline configuration files to use a bind volume one
RUN rm -f /usr/share/logstash/pipeline/logstash.conf && \
    rm -f /usr/share/logstash/config/logstash-sample.conf
