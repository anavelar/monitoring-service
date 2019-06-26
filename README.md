A Monitoring Service for Cloud Monitoring, build as a Docker Stack Service.

Containerized services (WIP):

- [x] Local Metrics and Application data Collector: Sensu;
- [x] Metrics and Application data Database: InfluxDB;
- [x] Metrics and Application data Dashboards: Grafana;
- [x] Local Logs collector: Rsyslog
- [x] Logs receiving, treatment and pasing: Logstash;
- [x] Logs Database and Search Engine: ElasticSearch;
- [x] Logs UI for Search and Visualization: Kibana;


The Monitoring Service works in a client - server way:

* A dedicated server runs the monitoring service, having all components unified in a single docker compose stack.

* Monitored entities (monitored hosts, cloud's component hosts) send their local collected data 
to the server. A Sensu agent and a rsyslog daemon on the host collect these data and send to the monitoring server.
Directory _monitored-hosts_ here has client/monitored host nedeed agent and configuration.

##### Para ativar a monitoração

```
$ docker-compose up -d
```

##### Configurações recomendadas

Todos os serviços estão com user, senha, vhost etc padrões da aplicação.
O que não é padrão está documentado aqui e explicitado nos arquivos de configuração
e variáveis do docker-compose principal.

* É recomendado escolher um tempo de expiração para o armazenamento dos dados coletados (meses, semanas, etc).
Caso não seja escolhido nenhum dado nunca será automaticamente removido com o tempo. Porém o espaço em disco
para o armazenamento será crescente e pode até preencher todo o espaço em disco.

Para definir um tempo de expiração:
(No exemplo, está sendo definido como 3 semanas. Para escolher outro tempo, mudar o número 3 pelo desejado e
w para week, d para day, etc - notações conforme padrões InfluxQL na documentação online do InfluxDB 1.7.)

* Lista os containers:

```
$ docker ps
```

* A partir dos containers listados, copia a CONTAINER ID do container influxdb (o que tem a imagem _influxdb:1.7.6_
na coluna ao lado da ID).

* Entra no conatiner InfluxDB:
Onde se substitui <CONTAINER ID> pelo número copiado do primeiro passo, retirando os <> e deixando só a ID:

```
$ docker exec -it <CONTAINER ID> bash
```

* Acessa o InfluxDB de dentro do container:

```
$ influx
```

* Altera o tempo de expiração dos dados:

```
$ CREATE RETENTION POLICY sensurp ON sensu DURATION 3w REPLICATION 1 SHARD DURATION 1d DEFAULT
```

* Remove o tempo antigo (opciona sugerido):

```
$ DROP RETENTION POLICY autogen ON sensu
```

* Sai do InfluxDB:

```
$ exit
```

* Sai do container InfluxDB:

```
$ exit
```

##### Personalizações

* Para ativar notificações de queda e retorno de hosts monitorados por e-mail:
  Preencher valores no arquivo _services/sensu/server/conf/handlers/mailer.json_.

* Automatizar Novos Dashboards e Dashboards alterados
  Ao alterar Dashboards existentes ou ao criar novos Dashboards no Grafana, isso pode ser salvo para ser automatizado,
  de modo que a personalização não se perca se houver problema com os dados / disco / volumes.

  Para tal, na tela do Dashboard criado ou alterado clique em _salvar_. Isso irá gerar um arquivo _.json_, que deve ser
  colocado no diretório abaixo:

```
grafana/provisioning/dashboards
```

  Caso seja uma alteração em Dashboard existente, substituir o json antigo pelo novo. Caso seja um novo Dashboard, inserir
  o novo arquivo no diretório.


##### ATENÇÃO - Mudança da versão dos componentes de logs

Ao atualizar componentes de logs (ElasticSearch, Kibana, Logstash) é recomendado backup
dos dados além de seguir todas as recomendações da documentação oficial para cada
componente. Consultar as recomendações da documentação oficial antes de executar atualização.
