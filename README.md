A distributed Monitoring Service build as a Docker Swarm service for Hadoop and
Spark monitoring as well as general monitoring.

Containerized services (WIP):

- [x] Sensu (server, client, api and uchiwa);
- [x] Redis;
- [ ] InfluxDB;
- [ ] Grafana;
- [ ] Collectd;
- [ ] ElasticSearch;
- [ ] Logstash;
- [ ] Kibana;
- [x] RabbitMQ as a transport layer;
- [ ] Riemann;

###### Observações ao executar

* **Não apagar a pasta sensu**: A pasta sensu está mapeada para o sensu-server e contém definições de handlers e outros. Apesar de ser criada automaticamente, os arquivos dentro dela ainda não são (por serem variáveis), então não vamos deletar essa pasta para não precisar refazer os arquivos.

* **Server demora 2min a subir total temporariamente**. O server tá demorando 2 minutos pra subir, isso pela instalação em tempo de execução do plugin do mailer. Uma consequência disso é que os clientes demoram um pouco a aparecem do dashboard do Uchiwa depois que sobem porque o server ainda não tá de pé direito. A próxima issue é justamente isso, colocar essa instalação dentro do Dockerfile e fazer build. 

---

###### Pendentes para o ambiente de produção:

* **Para notificações de queda por e-mail ativadas**: o arquivo *mailer.conf*, arquivo com as configurações do handler mailer que trata quedas, que fica dentro da pasta sensu, precisa ser preenchido com os valores definidos:
  * Preencher o e-mail para receber os avisos de queda.
  * Preencher usuário e senha SMTP certos.
  * **Opcional**: Alterar o tempo para notificar: atualmente está 120 segundos após a queda. Esse tempo está em sensu/sensu-build/templates/client.json.tmpl, o número em frente a CLIENT\_KEEPALIVE\_CRITICAL. Como checa de 20 em 20 segundos, recomendado pelo menos 60 segundos para notificar.

###### Pendentes opcionais

Todos os serviços estão com user, senha, vhost etc padrões. Podemos mudar depois.

