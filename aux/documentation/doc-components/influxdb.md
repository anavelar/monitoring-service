### Conteúdo

1. [Sobre o InfluxDB](#sobre)
   1. [Arquitetura](#arquitetura)
   2. [Acesso](#acesso)

2. [Decisões de projeto e implementação](#impl)
   1. [Container e versão](#versao)
   2. [Configuraçao Usada](#conf)
   3. [Volumes](#volumes)

---

### 1. InfluxDB <a name="sobre"></a>

O InfluxDB é um Banco de Dados time series, otimizado para séries temporais e 
dados numéricos. Ele irá armazenar os dados das métricas coletadas.

Ele é NoSQL.

##### A. Arquitetura <a name="arquitetura"></a>

O InfluxDB pode conter distintos vários Bancos de Dados. Cada banco de dados 
contém _measurements_, que se comportam como tabelas SQL. Cada _measurement_ 
contém _tag keys_ e _fields keys_, as colunas da tabela sendo uma indexável e 
a outra não. Além disso os dados possuem um retention policy: política de 
retenção que determina a duração deles.

>>>
[CONCEITOS E DOCUMENTAÇÃO](https://docs.influxdata.com/influxdb/v1.7/concepts/key_concepts/)
>>>

##### B. Acesso à Instância e execução e visualizar os dados <a name="acesso"></a>

O InfluxDB nesta versão não possui interface gráfica web, que foi substituída 
por ferramenta de visualização à parte, que junto com outras ferramentas 
cria uma pilha de monitoração da coleta à visualização do próprio InfluxDB. 
Como são usados outros componentes para coleta e visualização aqui já, foi 
escolhido não usar esta opção gráfica.

Para acessar o InfluxDB, entra-se no container InfluxDB 
(`$ docker exec -it <hash-container-influbdb-obtida-docker ls> bash`) e 
executa-se o comando `influx`. Estamos então dentro do InfluxDB.

Assim, só escolher o banco de dados sobre o qual os comandos serão aplicados

```USE DATABASE sensu```

e aplicar as consultas disponíveis para poder ver os dados. As opções de 
consultas InfluxDB-SQL, chamadas _InfluxQL_ que podem ser feitas estão na 
seção InfluxSQL da
 [DOCUMENTAÇÃO OFICIAL](https://docs.influxdata.com/influxdb/v1.7/query_language/schema_exploration/).

---

### 2. Decisões de projeto e implementação <a name="impl"></a>

##### A. Container e versão <a name="versao"></a>

O container usado é o oficial InfluxDB. 

A versão é a mais recente da época da implementação.

##### B. Configuração <a name="conf"></a>

A configuração é feita através de environment variables do serviço (no arquivo 
docker-compose.yaml que o define) e pelo mapeamento dos arquivos de 
configuração através de volumes. A documentação oficial define 
[opções de configuração](https://docs.influxdata.com/influxdb/v1.7/administration/config/) 
 e além disso há na pasta de exemplos de arquivos de configuração aqui exemplo 
do arquivo padrão completo.

>>>
IMPORTANTE:

Pela configuração automatizada não é possível definir o tempo padrão de duração 
dos dados, o que causou que toda vez que o InfluxDB reinicia é preciso definir 
na mão esse tempo, como no passo-a-passo do README principal, em INSTRUÇÕES.
>>>

__Environment Variables Usadas__

- INFLUXDB_ADMIN_ENABLED: ativa usuário admin.

- INFLUXDB_ADMIN_USER e INFLUXDB_ADMIN_PASSWORD: credenciais do administrador.

- INFLUXDB_DB: sensu - Já cria um Banco de Dados chamado _sensu_ na instância 
ao criá-lo. Isto é necessário para a conexão InfluxDB-Sensu: a extensão do 
sensu que envia os dados para o InfluxDB precisa informar qual banco de dados 
usará, e este banco de dados já precisa estar criado para receber os dados. 
Como, ao subir os serviços, não há garantia de que o InfluxDB estará 
funcionando 100% antes do Sensu então o Banco de Dados já é criado para receber 
os dados.
 
__Arquivos de configuração mapeados__

Foi mapeado o volume com um arquivo de configuração mínimo necessário, o 
volume `/influxdb/influxdb.conf`.

##### C. Volumes <a name="volumes"></a>

Além do volume com arquivo de configuração mapeado, `influxdb/influxdb.conf`, 
o volume para armazenar os dados do influxdb foi mapeado como influxdb-storage,
na região docker. Como dito acima, é necessário realizar o procedimento no 
arquivo README ao reiniciar o serviço quando deletar o volume também, senão 
os dados terão tempo de armazenamento padrão que é infinito, e a quantidade 
necessária de disco para armazenamento será infinita.

>>>
Observação: nas Issues, tarefas a fazer, está mover o armazenamento do InfluxDB
 para mapeamento direto no host fora da região Docker, mapeamento bind, como foi
feito para o arquivo de configuração. Este mapeamento afetará diretamente o 
desempenho do InfluxDB, otimizado quando fora da região Docker de armazenamento.
Foi colocado apenas para versão inicial, mudar.
>>>

