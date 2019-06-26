[//]: <> ( README for monitoring speed repo                                    )

Este diretório contém as configurações que os hosts monitorados precisam ter
localmente para serem monitorados.

Cada cliente aqui é um container Docker com as configurações que um cliente
real deve ter. Os clientes reais são configurados pelo puppet, estas configura-
ções devem estar no [repositório puppet](https://git.speed.dcc.ufmg.br/lsl/puppet_speed).

Aqui estão em containers Docker para **finalidade de teste**.

* O arquivo *docker-compose.yaml* contém diversos clientes de teste simultane-
amente. Para executá-lo:

```
$ docker-compose up -d
```  

* O arquivo *docker-compose-single.yaml* tem um único cliente de teste e mapeado
para coletar informações do próprio host e não do container. Para executá-lo:

```
$ docker-compose -f docker-compose-single.yaml up -d
```

---

#### Testes com os checks aqui <--> ambiente de produção puppet

Ao copiar checks dos clients direto do ambiente produção, trocar dentro dos
checks, no comando executável deles, esta parte do caminho
```/opt/sensu/embedded/bin/``` por ```/usr/local/bundle/bin/```.

Os plugins / executáveis dos clientes de teste ficam em lugares diferentes
nos clientes de produção.

---

#### Possível erro na rede Docker

Ao subir os serviços (*docker-compose up -d*), ele procura a rede citada no
arquivo docker-compose pela ID. Isso pode gerar um *network ERROR*. Para que
ele procure a nova rede:

```
$ docker-compose up -d --force-recreate
```
---
