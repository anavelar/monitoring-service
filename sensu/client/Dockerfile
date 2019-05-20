FROM sstarcher/sensu:1.4.3

# Install custom plugins for client local checks
RUN sensu-install -p cpu-checks && \
    sensu-install -p memory-checks && \
    sensu-install -p load-checks && \
    sensu-install -p network-checks && \
    sensu-install -p disk-checks && \
    sensu-install -p io-checks && \
    sensu-install -p uptime-checks

# Install for using in io-checks
RUN apt-get update && \
    apt-get install sysstat -y
