FROM sstarcher/sensu:1.4.3

# Necessary for sensu local checks
RUN sensu-install -p cpu-checks && \
    sensu-install -p memory-checks && \
    sensu-install -p load-checks && \
    sensu-install -p network-checks && \
    sensu-install -p disk-checks && \
    sensu-install -p io-checks && \
    sensu-install -p uptime-checks

# Necessary to execute sensu io-checks
RUN apt-get update && \
    apt-get install sysstat -y

# Necessary to send logs to server
RUN apt-get -y install rsyslog && \
    rm /etc/rsyslog.conf

# precisa tambem configurar para iniciar no boot
# nao starta sozinho
