### ELK: Elasticsearch, Logstash e Kibana

1. [Sobre ELK](#sobre)

2. [Decisões de projeto e implementação](#impl)

   1. [__Coleta__ de logs feita fora do ELK](#coleta)

   2. [Ordem de instalação e configuração](#ordem)

   3. [Containers e Atualizações (`IMPORTANTE`) nos componentes](#containers)

   4. [Modo de funcionamento atual](#modo)

3. [Implementação de cada componente](#cada)

   1. [Logstash](#logstash)

   2. [Elasticsearch](#elasticsearch)

   3. [Kibana](#kibana)

---

#### 1. Sobre ELK <a name="sobre"></a>

Elasticsearch, Logstash e Kibana são três componentes altamente integrados e
 desenvolvidos para se conectarem e trabalharem juntos. A finalidade do uso
 dos três na monitoração aqui é o tratamento dos dados de log coletados.

- __Logstash__ recebe os dados de logs do sistema e os parseia para dizer ao
 Banco de Dados como tratar cada campo.

> [DOCUMENTAÇÃO COMPLETA LOGSTASH](https://www.elastic.co/guide/en/logstash/7.1/index.html)

- __Elasticsearch__ é um componente que é um mecanismo de busca de texto e um
 Banco de Dados para logs coletados.

> [DOCUMENTAÇÃO COMPLETA ELASTICSEARCH](https://www.elastic.co/guide/en/elasticsearch/reference/7.1/index.html)

- __Kibana__ é um componente que fornece interface gráfica e visualizações 
para os dados do Elasticsearch. O Kibana fornece interface gráfica para o 
acesso no Elasticsearch, interface gráfica para consultas e resultados de 
buscas no Elasticsearch, e também Dashboards com os dados do Elasticsearch. 

> [DOCUMENTAÇÃO COMPLETA KIBANA](https://www.elastic.co/guide/en/kibana/7.1/index.html)

---

### 2. Decisões de projeto e implementação <a name="impl"></a>

#### A. Coleta de logs feita fora do ELK <a name="coleta"></a>

À época da implementação aqui, a Elastic oferecia integrado à pilha de 
componentes ELK mais um componente, o __Beats__, que faz a __coleta e envio__ 
de logs (e de outros dados também opcionalmente como métricas de recursos, 
de tráfego de rede, etc).

O _Beats_ é também integrado ao Logstash, Elasticsearch e Kibana, e é um 
agente executando em cada host para fazer as coletas.

Foi tomada a decisão de projeto de não usar os produtos _Beats_ e usar o Log 
Manager do host no lugar. Isso porque a coleta e envio é apenas de logs, e os 
hosts já tem uma opção local executando para coleta e possível envio se 
necessário dos logs. Assim, seria desnecssário acrescentar um agente a mais 
excutando no host.

A coleta de logs na monitoração é feita pelo Log Manager dos hosts, Rsyslog.

#### B. Ordem de instalação e configuração <a name="ordem"></a>

Seguindo recomendações da documentação Elastic oficial, foi primeiro 
configurado Elasticsearch, depois Kibana e por último Logstash. A ordem de 
inicialização dos containers, usando a opção _depends_on_, também é esta.

> [RECOMENDAÇÕES PARA ELK](https://www.elastic.co/guide/en/elastic-stack/7.1/installing-elastic-stack.html)

#### C. Containers e Atualizações nos componentes <a name="containers"></a>

##### Container
---

O container escolhido para usar para os três componentes foram os oficiais 
ELK. Eles ficam em repositório próprio da Elastic.

No momento da instalação havia 4 opções possíveis para os componentes: duas 
eram pagas, uma era sem custo mas não inteiramente opensource (versão basic) 
e outra inteiramente opensource.
[TABELA DE FUNCIONALIDADES EM CADA VERSÃO](https://www.elastic.co/pt/subscriptions).

Foi feita a escolha de usar a versão Basic. Isso porque incluía mais 
funcionalidades sem custo. Outro fator que também motivou a escolha desta 
versão para ser usada foi ser a versão com mais simplificado acesso aos 
containers prontos e sincronizados. Assim, como é a primeira versão de 
implementação da monitoração, para a versão inicial foi escolhido o mais 
simples como início.

>>>
__X-pack__ - Ao ler sobre ELK, haverá várias opções de configuração 
relacionadas ao X-pack. X-pack é um pacote pago que inclui funcionalidades 
mais avançadas de uso e de segurança. Ele não está sendo usado nesta 
implementação.
>>>

##### Atualizações e Versões - `IMPORTANTE`
---

A versão implementada aqui, 7.1.1, era a mais recente estável à epoca da 
implementação do sistema de monitoramento aqui para os três componentes 
Elastic (Elasticsearch, Logstash e Kibana).

`IMPORTANTE 1: Uso da mesma versão nos três componentes`

[A Elastic recomenda que todos os componentes ELK estejam os treŝ na mesma 
versão](https://www.elastic.co/guide/en/elastic-stack/7.1/installing-elastic-stack.html).`
Na maioria dos casos, versões diferentes entre Elasticsearch, Logstash e 
Kibana tornarão incompatível o fluxo de logs entre os três componentes. Há 
casos expecionais em que versões diferentes entre eles são aceitas, [comentados aqui](https://www.elastic.co/guide/en/elastic-stack/7.1/upgrading-elastic-stack.html)

`IMPORTANTE 2: Ao efetuar atualizações de versão nos componentes`

A Elastic recomenda fortemente que efetuar atualizações nos componentes dela 
seja feito seguindo o _Processo em etapas de atualização da pilha ELK_, 
fornecido por ela, junto com outras recomendações de preparo do sistema e 
ambiente pré atualizações que ela recomenda. Tudo isto pode ser encontrado 
começando [
AQUI](https://www.elastic.co/guide/en/elastic-stack/7.1/upgrading-elastic-stack.html).

De qualquer forma, antes desta atualização "backup" das configurações e dos 
dados é muito recomendado.

#### D. Modo de funcionamento atual <a name="modo"></a>

Na primeira versão deste sistema de monitoramento, a pilha ELK (Elasticseach, 
Logstash e Kibana) está funcionando sem clusterização.

`Para ambientes de produção é recomendado na documentação oficial que estejam 
 em "modo produção", que é clusterizado.`

Isso está listado como necessidade a ser incluída na pŕoxima versão da 
monitoração aqui.

Migrar para modo de produção exige uma série de configurações, que se não 
feitas por completo [travam o funcionamento da pilha de componentes](https://www.elastic.co/guide/en/elasticsearch/reference/7.1/system-config.html). 
Estas configurações estão todas listadas nas seções _Configurações_ / 
_Configurações Elasticsearch Importantes_ e  _Configurações de sistema 
importantes_, na 
[DOCUMENTAÇÃO ELASTICSEARCH](https://www.elastic.co/guide/en/elasticsearch/reference/7.1/index.html).

---

### 3. Implementação de cada componente <a name="cada"></a>

#### A. Logstash <a name="logstash"></a>

Os arquivos de configuração do logstash definem seu comportamento em vários 
âmbitos:

- Se haverá entrada de dados no Logstash e como deve ser essa entrada (por 
  qual canal dele, o que irá receber, etc);

- Como tratar os dados entrando, como interpretá-los (inclui o processo de 
  _parse_);

- Saída de dados do Logstash: para onde os dados devem ser enviados, como, 
  etc.

Abaixo estes pontos são detalhados.
 
##### Imagem personalizada para personalizar arquivos de configuração
---

Este container Logstash padrão já vem com arquivos de configuração prontos e 
não possui amplas opções de configuração sobre eles usando environment 
variables. Assim, para pode personalizar a configuração foi necessário 
usar arquivos de configuração inteiros e não environment variables. Como os 
arquivos já vem na imagem, foi decidido:

- Para retirar os arquivos de configuração padrão: **Buildar nova imagem** em 
  que os arquivos de configuração padrão não estão

- Para colocar nova configuração: **Mapear arquivos de configuração direto do 
  host**.

##### Arquivo de configurações principais
---

Os arquivos na pasta `logstash/pipeline/` do container logstash falam sobre 
comportamento do logstash para entrada e saída.

O arquivo de configuração mapeado neste local:

- Informa ao logstash para receber os logs do tipo syslog (que serão enviados 
pelos hosts) via tcp e via udp, e na porta 5000 do container;

- Como interpretar os dados recebidos nos pacotes: como separar os campos 
para então poder armazená-los e indexá-los para busca no Elasticsearch;

- Para enviar todos os dados após parseados para o host elasticsearch. 

##### Portas
---

A porta 5000 do container, como informado aqui no parágrafo anterior, 
recebe os logs rsyslog dos hosts monitorados.

##### Armazenamento Logstash
---

Um volume na região Docker foi construído para manter os dados de 
armazenamento do logstash.




#### B. Elasticsearch <a name="elasticsearch"></a>

O container Elasticsearch está usando a configuração padrão.

##### Volumes
---

O armazenamento dos logs recebidos por ele está sendo feito no volume 
mapeado para a região Docker _elastic-storage_.

>>>
 Este volume será movido em próximas versões para mapeamento bind direto no 
host, como recomendado pela própria Elastic. Está na lista de próximas 
implementações.
>>>

##### Environment variables
---

- *cluster.name*

  Apenas o nome para a instância Elasticsearch do container.

- *discovery.type=single-node*

  Define a instância como não clusterizada, como nó único. 
  Sem essa configuração o container se considera em _modo produção_ e passa a 
  exigir configurações de modo de produção para ficar ativo.




#### C. Kibana <a name="kibana"></a>

O container Kibana segue tudo que já foi dito aqui na seção sobre ELK. 
Atualmente ele está usando a configuração padrão da versão Basic dos 
containers, mas com uma única environment variable o diferenciando do 
padrão:

- CSP_STRICT: 'true'
  
  Opção escolhida para aumentar a segurança contra invasões no acesso.
