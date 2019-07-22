#### Conteúdo

1. [Sobre o Uchiwa](#sobre)

2. [Decisões de projeto e implementação](#impl)

   1. [Versão e repositórios dos containers](#versao)

   2. [Configuração](#conf)

---

### 1. Uchiwa <a name="sobre"></a>

Uchiwa é um dashboard acessado pelo navegador web (só acessar o IP, porque está mapeado aqui 
para a porta 80) que permite acompanhar os hosts sendo monitorados.

Ele lista os hosts conectados, hosts que perderam o contato, o status da coleta do agente
Sensu em cada host e o estado atual do Sensu (se há conexão ok com transporte, banco de dados, 
etc). Assim, o Uchiwa é uma ferramenta que permite acompanhar o estado do Sensu e permite 
acompanhar os hosts conectados também (mostra tudo o que está sendo coletado dos hosts).

O Uchiwa consulta o Sensu via Sensu API.

> [DOCUMENTAÇÃO COMPLETA](https://docs.sensu.io/uchiwa/1.0/).

---

### 2. Decisões de projeto e implementação <a name="impl"></a>

#### A. Versão e repositórios dos containers <a name="containers"></a>

Foi escolhido o container sensu sstarcher pela facilidade de configuração via environment 
variables e pela fácil integração com o container sensu, também sstarcher.

#### B. Configuração <a name="conf"></a>

##### Porta

A porta foi mapeada para 80 para facilitar visão dos hosts e acesso, e para não colidir com a 
porta 3000 do Grafana.

#### environment variables

As environemnt variables listadas, colocadas por scripts do container em arquivos de 
configuração Sensu, são obrigatórias para o container funcionar.

- Nome do host em que está a API para se comunicar com o sensu API: `SENSU_HOSTNAME: api`
(O container da api sensu tem hostname api).

- Nome do servidor Sensu (Data Center, DC Name) a dar: `SENSU_DC_NAME: speed` 
