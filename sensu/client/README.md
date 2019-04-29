#### Sensu-client para testes
Na monitoração os clients não são em container e vão para cada máquina pelo
*Puppet*. Porém este client é para testes e costuma ter as mesmas configurações
dos clients no puppet.

##### ATENÇÃO

Ao copiar checks dos clients direto, trocar no comando executável esta parte do
caminho do script

```sh /opt/sensu/embedded/bin/ ```

substituir por

```sh /usr/local/bundle/bin/ ```

para o client de testes aqui, os executáveis do sensu ficam em lugares
diferentes nos dois.
