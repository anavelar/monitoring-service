## Instruções

1. [Ativar a monitoração](#ativar)
    1. [No Servidor](#servidor)
    2. [Nos hosts monitorados](#clientes)
    3. [Configuração Inicial](#confini)
2. [Operações comuns](#uso)
    1. [Automatizar Dashboards novos ou modificados](#dashes)
    2. [Trocar o e-mail de recebimento de alertas](#smtp)

---

## 1. Para ativar a monitoração: <a name="ativar"></a>


### A. Servidor <a name="servidor"></a>

Em um servidor (de preferência dedicado para a monitoração) que tenha Docker
instalado (executando em modo _swarm_ ou não): 

1. Clone o projeto / Baixe.

2. Na raiz da pasta _monitoring-service_, faça os comandos abaixo para construir 
algumas imagens customizadas que vão ser usadas:

```
$ docker image build -t sensu:1.4.3 ./sensu

$ docker image build -t logstash-rsyslog:7.1.1 ./logstash

$ docker image build -t monitored-host:1.4.3 ./monitored-hosts 
```

3. Na raiz desta pasta _monitoring-service_ faça

```
$ docker-compose up -d

OU

$ docker stack deploy --compose-file docker-compose.yaml monitorStack
```

O serviço de monitoração já está funcionando.

### B. Monitorar uma entidade: Clientes <a name="clientes"></a>

Para que uma entidade / host seja monitorado pelo sistema de monitoração aqui,
é preciso que o servidor de monitoração possa ser visto na rede pelo host 
monitorado e também que a entidade monitorada tenha as mesmas configurações
do diretório _monitored-hosts_ aqui:

  * Para **logs**: gerenciador de logs do Sistema Operacional do host monitorado
    usando os arquivos de configuração da pasta `rsyslog` se o gerenciador de
    logs for o rsyslog, senão instalar o rsyslog e usar os arquivos.

  * **Para medições de desempenho de recursos e aplicações (métricas)**: 
     Agente sensu [^1] executando (daemon) usando a pasta checks como no
     diretório _monitored-hosts_ e com as configurações de instalação de
     plugins sensu e outros como no Dockerfile do cliente de teste.

`Observação imporante!`: Os clientes do ambiente de produção não são
configurados aqui na pasta _monitored-hosts_, esta pasta contém somente clientes
iguais para teste. O repositório para configuração dos hosts monitorados é
outro, um outro que envia a configuração para os hosts, repositório puppet e
ansible.

### C. Configurações iniciais ao subir o serviço <a name="confini"></a>

   1. **Seleção de logs** - 
     Com a monitoração up, para escolher quais logs visualizar é informado em
     tempo de execução quais logs buscar para exibir. Isso é feito definindo um
     Index no Elasticsearch:

       - Acessar a interface gráfica do Kibana
       `<IP-do-servidor-de-monitoracao>:5601`

       - Configurações >> ElasticSearch >> IndexManagement >> _Create Index_ ,
       colocar o regex no campo nome: _logstash-*_.

       > Obs.: Este processo está sendo automatizado.


   2. __Definir tempo de expriração para os dados - controle do armazenamento__
     - É recomendado escolher um tempo de expiração para o armazenamento dos
     dados coletados (meses, semanas, etc). Caso não seja escolhido, nenhum
     dado nunca será automaticamente removido com o tempo. Porém o espaço em
     disco para o armazenamento será crescente e pode até preencher todo o
     espaço em disco.

       __Para definir um tempo de expiração__:


       * __Lista os containers__: ```$ docker ps```
       A partir dos containers listados, **copia a CONTAINER ID do container
       influxdb** (o que tem a imagem _influxdb:1.7.6_ na coluna ao lado da ID).

       * __Entra no container InfluxDB__ : Onde se substitui <CONTAINER ID> pelo
        número copiado do primeiro passo, retirando os <> e deixando só a ID:
        ```$ docker exec -it <CONTAINER ID> bash```

       * __Acessa o InfluxDB__ de dentro do container: ```$ influx```

       * __Altera o tempo__ de expiração dos dados:
       ```$ CREATE RETENTION POLICY sensurp ON sensu DURATION 3w REPLICATION 1 SHARD DURATION 1d DEFAULT```

         _Observação_: Acima o tempo está sendo definido como 3 semanas. Para
         escolher outro tempo,
           * mudar o número 3 pela quantidade de tempo desejada
           * ao lado do número, usar w para week, d para day, etc - todas as
           opções na documentação online do InfluxDB 1.7., seção InfluxQL.

       * Remove o tempo antigo (opcional):
       ```$ DROP RETENTION POLICY autogen ON sensu```

       * Sai do InfluxDB: ```$ exit```

       * Sai do container InfluxDB:```$ exit```

---

### 2. Operações comuns <a name="uso"></a>

Todos os serviços estão com user, senha, vhost etc padrões da aplicação.
O que não é padrão está documentado aqui e explicitado nos arquivos de
configuração e variáveis do docker-compose principal.

##### A. Criação automática de Dashboards novos criados ou de Dashboards 
alterados <a name="dashes"></a>

Ao alterar Dashboards existentes ou ao criar novos Dashboards no Grafana,
isso pode ser salvo para ser automatizado, de modo que a personalização não
se perca se houver problema com os dados / disco / volumes.

Para tal, 

- **Novo Dashboard:**
  Na tela do Dashboard criado clique em _salvar_. Isso irá gerar um arquivo
  _.json_, que deve ser colocado no diretório `grafana/provisioning/dashboards`.

- **Alterar Dashboard existente**
  Na tela do Dashboard criado clique em _salvar_. Isso irá gerar um arquivo
  _.json_, que deve ser colocado no diretório `grafana/provisioning/dashboards`
  no lugar de seu arquivo _json_ antigo, um arquivo _json_ com o nome do
  dashboard.

##### B. Mudar o e-mail para envio de alertas <a name="smtp"></a>

Para incluir o email para envio de alertas, acessar o arquivo 
`sensu/conf.d/handlers/mailer.json` e preencher os campos
com servidor SMTP a ser usado e dados pedidos.

---

[^1]: Sensu Core.
