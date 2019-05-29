A distributed Monitoring Service build as a Docker Swarm service for Hadoop and Spark monitoring as well as general monitoring.

Containerized services (WIP):

- [x] Sensu (server, client, api and uchiwa);
- [x] Redis;
- [x] InfluxDB;
- [x] Grafana;
- [ ] Collectd;
- [ ] ElasticSearch;
- [ ] Logstash;
- [ ] Kibana;
- [x] RabbitMQ as a transport layer;
- [ ] Riemann;

##### Dashboards

Os dashboards são gerados automaticamente. Para modificar um dashboard, clique em salvar e substitua o json
desse dashboard na pasta

```
grafana/provisioning/dashboards
```

pelo novo conteúdo json que aparece ao clicar em salvar.

---

###### Configurações necessárias no ambiente (environment variables, etc):

* Para notificações de queda e volta por email:
    Preencher valores no arquivo _services/sensu/server/conf/handlers/mailer.json_.

---

###### Configurações opcionais

* Todos os serviços estão com user, senha, vhost etc padrões. O que não é padrão está nos arquivos e environment variables escritos aqui.
