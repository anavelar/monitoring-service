
- [Serviços](#servicos)

- [Imagens personalizadas](#imagens)

- [Redes docker](#redes)

- [Volumes](#volumes)


### Serviços <a name="servicos"></a>
---

Todos os serviços estão containerizados.

- Servidor de monitoração: Todos os componentes estão no mesmo arquivo 
  _docker-compose.yaml_ na raiz do projeto aqui.

- Hosts monitorados: a configuração dos hosts é direto neles, como explicado
  em outra página aqui. Porém há __clientes em container para testes__. 
  Estão nos arquivos _docker-compose.yaml_ da pasta 
  `monitored-hosts/` aqui.

A pilha de serviços pode ser usada tanto em hosts com docker sendo executado 
em modo swarm (Comando `$ docker stack deploy --compose-file docker-compose.yaml monitorStack` na raiz do repositório)
quanto em servidor com docker engine em modo não distribuído (Comando 
`$ docker-compose up -d`). 

### Imagens personalizadas <a name="imagens"></a>
---

Algumas imagens são personalizadas e ainda não estão em um servidor central.
Quando um serviço tiver uma imagem personalizada para ele, o Dockerfile desta 
imagem estará sempre na pasta deste serviço, logo no primeiro nível dela.

> Nas próximas versões está mapeado colocar as imagens em um registry.

> O processo de build destas imagens está sendo feito sempre pelo script 
de automação do GitLab no topo do repositório, arquivo _.gitlab-ci.yaml_.

### Redes docker <a name="redes"></a>
---

Atualmente há duas redes, _metricsnet_ e _logsnet_. Isso porque uma é para 
o fluxo de métricas e outra para o fluxo de logs, como diz o próprio nome.
Assim, em cada rede estão apenas componentes que se comunicam, de modo a 
facilitar o debug.

### Volumes <a name="volumes"></a>
---

Todo o armazenamento dos componentes está sendo feito, por enquanto, na região 
Docker, em volumes separados para cada.

Além disso, há mapeamento direto entre o host e o container mas apenas para 
arquivos de configuração e personalização dos componentes que precisaram 
ter estes arquivos facilmente disponíveis.
