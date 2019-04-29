FROM sstarcher/sensu:1.4.3

# Install custom plugins for client local checks
RUN sensu-install -p cpu-checks &&\
    sensu-install -p memory-checks &&\
    sensu-install -p load-checks &&\
    sensu-install -p network-checks &&\
    sensu-install -p uptime-checks &&\
    sensu-install -p disk-checks
