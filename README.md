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

###### Observações ao executar

* **Sensu-Server demora 2min a subir por completo -> Clients demoram esse tempo para aparecerem no dashboard sensu**. O servidor sensu está demorando 2 minutos pra subir, isso pela instalação em tempo de execução do plugin mailer. Uma consequência disso é que os clientes demoram um pouco a aparecem no dashboard Uchiwa depois de ativos (porque o servidor ainda não está up).

---

###### Configurações necessárias no ambiente (environment variables, etc):

* Para notificações de queda e volta por email:
    Preencher valores no arquivo _services/sensu/server/conf/handlers/mailer.json_.

* Environment Variables:
  Para o InfluxDB:
  * INFLUXDB_ADMIN_USER
  * INFLUXDB_ADMIN_PASSWORD

---

###### Configurações opcionais

* Todos os serviços estão com user, senha, vhost etc padrões. Para o InfluxDB, todas as variáveis são 'sensu'.
