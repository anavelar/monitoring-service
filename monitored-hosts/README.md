### Clientes para Testes (Containerizados) - WIP

Esta pasta contém hosts monitorados de teste para serem usados em ambiente de 
desenvolvimento no teste de novas implementações no servidor.

O [arquivo que fala sobre implementação da monitoração, servidor e hosts 
monitorados,](../aux/documentation/implementacao.md) explica tudo que um host 
monitorado deve ter para coletar os dados de monitoração. Aqui temos um 
exemplo de implementação.

```
IMPORTANTE

O ideal é que estes clientes de teste sejam sempre atualizados para se manterem 
iguais à hosts em produção em configuração. Isso faz com que os testes sejam 
mais reais e evita crashes após merge e após colocar o código em produção.
```

1. [Funcionamento](#on)
   1. [Como ativar](#ativar)
   2. [Arquivos de coleta de métricas: produção x dev](#checks)
   3. [Possíveis crashes](#crash)
2. [Implementação](#implementacao) 

---

### 1. Funcionamento <a name="on"></a>

#### A. "Ativando" os hosts monitorados para testes <a name="ativar"></a>
---

Os hosts monitorados estão em containers e tem seu serviço definido nos arquivos
docker-compose.yaml aqui.

- O arquivo *docker-compose.yaml* contém diversos clientes de teste simultane-
amente. Para executá-lo:

```
$ docker-compose up -d
```  

- O arquivo *docker-compose-single.yaml* tem um único cliente de teste e mapeado
para coletar informações do host executando o container, e não do container. 
Para executá-lo:

```
$ docker-compose -f docker-compose-single.yaml up -d
```

#### B. Arquivos de coleta de métricas: produção x testes <a name="checks"></a>
---

* **ATENÇÃO!** Ao inserir novo check:
  * Instalar o plugin dele na imagem do container cliente
  * Colocar no check o handler metrics, como os do exemplo (É este que faz enviar para o InfluxDB)

* **Para testes com os checks aqui <--> ambiente de produção puppet**:
Ao copiar checks dos clients direto do ambiente produção, trocar dentro dos
checks, no comando executável deles, esta parte do caminho
```/opt/sensu/embedded/bin/``` por ```/usr/local/bundle/bin/```.
Os plugins / executáveis dos clientes de teste ficam em lugares diferentes
nos clientes de produção.

#### C. PLUS: Possível erro na rede Docker <a name="crash"></a>
---

Ao subir os serviços (*docker-compose up -d*), ele procura a rede citada no
arquivo docker-compose pela ID. Isso pode gerar um *network ERROR*. Para que
ele procure a nova rede:

```
$ docker-compose up -d --force-recreate
```
---

### 2. Implementacao <a name="implementacao"></a>

O container usado é um container com SO Debian e instalado o Sensu Core 1.4.3. e
o rsyslog. 

As environment variables dele preenchem arquivos de configuração Sensu client. 
Quais variáveis estão preenchendo o que pode ser visto na pasta _templates_ e 
nos scripts do [REPOSITÓRIO SSTARCHER](https://github.com/sstarcher/docker-sensu),
repositório que contém a imagem que foi usada como base para o container aqui.

Resumindo: O container aqui é um container com Sensu Core 1.4.3 cliente, vindo 
do repositório sstarcher, e ele é buildado para fazer instalação do rsyslog e 
instalações de pacotes e plugins sensu necessários para fazer as coletas. Como 
todas as outras imagens em que é feito build, seu Dockerfile se encontra aqui 
na pasta dele.

Os arquivos mapeados entre host e container são para configurações de logs do 
rsyslog e para colocar instruções de coleta do Sensu (_Checks_).

Este containers precisam estar nas duas redes docker: a rede externa (definida 
no arquivo compose do servidor) _metricsnet_ é necessária para envio das 
métricas coletas, e a rede _logsnet_ é necessária para envio de logs ao 
logstash do servidor.
