#### Sensu-client para testes
Na monitoração os clients não são em container e vão para cada máquina pelo *Puppet*. Porém estes clientes estão disponíveis aqui para testes.

 - *docker-compose.yaml* - Contém uma stack com clientes similares aos do ambiente  de produção.
 
 - *docker-compose-single.yaml* - Para subir um client único, que mede o host nos checks e não o container.
 
 - *docker-compose-single-companion.yaml* - Caso decida subir outro client além do single.
 
---

##### ATENÇÃO

Ao copiar checks dos clients direto do ambiente produção, trocar dentro dos checks, no comando executável deles, esta parte do caminho:

```sh /opt/sensu/embedded/bin/ ```

substituir por:

```sh /usr/local/bundle/bin/ ```

Os plugins / executáveis dos clientes de teste ficam em lugares diferentes dos clientes de p
rodução.
