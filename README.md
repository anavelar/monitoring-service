# DOC IN PROGRESS 

Este repositório contém um sistema para monitorar ambientes em nuvem,
composto por diversos componentes (cada um com uma função) interligados.
O sistema de monitoração monitora hosts e aplicações nestes. Estes hosts podem
ser máquinas físicas ou virtuais no data center.

#### Sumário

1. [Visão Geral da Arquitetura](#arquitetura)
   1. [Servidor](#servidor)
   2. [Clientes: Hosts monitorados](#hosts)

2. [Documentação extensa e projeto](#doccompleta)

3. Implementação e código <a name="secaoimpl"></a>
   1. [Como iniciar a monitoração e operações frequentes](./INSTRUCOES.md)
   2. [Implementação da Arquitetura, componentes, quem é quem no código](./aux/documentation/implementacao.md)

---

#### 1. Arquitetura <a name="arquitetura"></a> 

O funcionamento do sistema de monitoramento pode ser visto aqui:

![monitoramento](./aux/documentation/doc-auxiliar-files/components.png)

As setas representam o fluxo de dados.

O processo de monitoração funciona em modelo cliente-servidor: Os dados são
coletados nos clientes, que são os hosts monitorados, por meio de um agente
coletor local. Cada host envia seus dados coletados através da rede para o
servidor de monitoração, servidor dedicado executando os serviços aqui de 
monitoração.

##### A. Hosts monitorados <a name="hosts"></a>

Em cada host monitorado, são coletados os logs gerados nele e métricas 
dele (que são medições de desempenho de recursos e de aplicações nele).

- Para coletar os logs de um host, instruímos, através de arquivos de
configuração, o gerenciador de logs do Sistema Operacional do host à 
enviar os logs coletados a um servidor remoto (ao invés de salvar em 
arquivos locais).

> Estes arquivos de configuração estão na pasta `monitored-hosts/rsyslog`.

- Para medir o desempenho de aplicações do host e medir o uso de recursos 
dele são executados nele scripts de medição. 

> Esses scripts de medição são primeiro instalados em cada host, e depois
são colocadas instruções no host para usar estes scripts de medição de
tempo em tempo. 

##### B. Servidor de monitoração  <a name="servidor"></a>

O servidor compreende todos os serviços da monitoração no diagrama fora a 
coleta de dados em cada host. A ideia é, primeiramente, poder visualizar o que está ocorrendo em cada host e saber quando estão ocorrendo cenários 
críticos.

Para poder visualizar os dados, os dados são coletados, armazenados (em 
bancos de dados) e depois então acessados para visualização. A parte de 
detecção de cenários críticos é feita por alertas.

É importante ver que temos três cores para os componentes da monitoração.
Os componentes coloridos de verde são os componentes por onde passa o fluxo
de dados dos logs. Os componentes coloridos de rosa são por onde passa o 
fluxo de dados das métricas, medições de desempenho. O componente amarelo 
exibe nem um nem outro: exibe uma visão geral do sistema, visão geral de 
quais hosts estão sendo monitorados, é uma visão dos hosts. **Pode-se 
perguntar porque fluxos diferentes para os dados**. Isto se dá devido aos 
tipos de dados: logs são dados com formato variável e com muito texto. 
Métricas são dados com formato bem padronizado e comum, e dados totalmente 
 numéricos. Assim, estes dados tem demandas diferentes de tratamento, o 
que motiva a separação dos fluxos.

Além dos componentes de visualização dos dados e dos bancos de dados, 
vemos mais dois componentes no servidor de monitoração, alertas e parsing 
dos dados. O componente de alertas serve para enviar alertas quando 
ocorrem cenários críticos de medição. O componente parsing serve para 
fazer a entrada de dados dos logs nos bancos de dados: informa ao Banco de
 Dados quais campos interpretar de que forma, visto que não tem tamanho 
definido e os dados são quase todos texto puro. 

#### 2. Documentação extensa  <a name="doccompleta"></a>

A arquitetura de monitoração usada aqui se chama Seshat e foi proposta 
[NESTE DOCUMENTO](./aux/documentation/project-guides/arquitetura-seshat.pdf)
que explica de forma completa motivações, decisões, ferramentas. Há uma 
implementação dela que monitora além de ambientes em nuvem, monitora 
o comportamento de aplicações e energético ao processar _Big Data_ 
[NESTE REPOSITÓRIO](https://github.com/viniiciusconceicao/monitoring-service).

A arquitetura Seshat proposta foi usada no repositório atual para monitorar um 
ambiente em nuvem em geral: monitorar a própria nuvem e seu comportamento, 
monitoramento da infraestrutura. O uso de Seshat para construir uma monitoração 
com esta finalidade (a adaptação para o data center aqui) é documentado de forma
 extensa
[NESTE DOCUMENTO](./aux/documentation/project-guides/seshat-adaptada.pdf). 

>>>
Para apenas saber sobre implementação, funcionamento, organização do código e 
operações acesse a [SEÇÃO IMPLEMENTAÇÃO](#secaoimpl).
>>>
