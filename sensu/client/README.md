## Sensu-client para testes

Na monitoração os clients não são em container e vão para cada máquina pelo *Puppet*. Porém estes clientes estão disponíveis aqui para testes.

 - *docker-compose.yaml* - Contém uma stack com clientes similares aos do ambiente  de produção.

 - *docker-compose-single.yaml* - Para subir um client único, que mede o host nos checks e não o container.

---

#### DEBUG COMPOSE CLIENTES

Ao usar o comando *docker-compose up*, ele procura a rede citada no arquivo docker-compose pela ID. Isso faz ter um *network ERROR*. Para que ele procure a nova rede up, pelo mesmo, faça

```
$ docker-compose up -d --force-recreate
```
---

#### DOCKER IMAGE

A imagem para os clientes está no Dockerfile aqui. Toda vez que alterar o Dockerfile, fazer depois, dentro desta pasta:

```
$ docker image build -t avelarana/sensu-plugins:1.4.3 .
```

e depois

```
$ docker image push avelarana/sensu-plugins:1.4.3

```

---

#### CHECKS

Ao copiar checks dos clients direto do ambiente produção, trocar dentro dos checks, no comando executável deles, esta parte do caminho:

```sh /opt/sensu/embedded/bin/ ```

substituir por:

```sh /usr/local/bundle/bin/ ```

Os plugins / executáveis dos clientes de teste ficam em lugares diferentes dos clientes de produção.

---
