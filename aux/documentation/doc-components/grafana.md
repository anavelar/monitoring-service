### Conteúdo

1. [Sobre o Grafana](#sobre)

2. [Decisões de projeto e implementação](#impl)
   1. [Container e versão](#versao)
   2. [Configuração e Automação](#conf)
   3. [Problemas de permissão](#permissao)
   4. [Volumes e Armazenamento](#volumes)
 
---

### 1. Grafana <a name="sobre"></a>

O Grafana permite criar visualizações gráficas para os dados coletados. Ele 
se conecta à um banco de dados para obter seus dados em tempo real e os
  exibe com as visualizações gráficas que permite criar.

Uma instância em execução do Grafana permite a criação de diversos dashboards, 
onde cada dashboard é um conjunto de gráficos / visualizações.

Cada visualização é criada:

 - Criando um novo dashboard para contê-la ou acessando o dashboard já criado 
 que irá contê-la
 
 - Criando a visulização / gráfico:

   - Definindo-se quais dados serão exibidos (escreve-se uma consulta SQL 
   sobre o banco de dados conectado que retornará os dados desejados)

   - Escolhendo-se qual forma de exibir aqueles dados: gráfico temporal de 
   barras, indicador único, mapa de calor, tabela (para este caso escolhe-se 
   na interface gráfica o que será exibido em linhas, colunas etc), entre 
   outros.

Criada uma visualização, outras podem ser criadas e colocadas neste mesmo 
dashboard. E novos Dashboards podem ser criados. 

Dashboards podem conter também panels, que são painés que agrupam os gráficos
 de cada dashboard. 

>>>
Mais conceitos sobre o Grafana podem ser vistos na 
[DOCUMENTAÇÃO OFICIAL](https://grafana.com/docs/v6.1/guides/basic_concepts/)
dele também.
>>>

---

### 2. Decisões de projeto e implementação <a name="impl"></a>

#### A. Container e versão <a name="versao"></a>

O container usado é o oficial grafana. A versão escolhida foi a mais recente à 
época da implementação.

#### B. Configuração e Automação <a name="conf"></a>

##### Configuração

A configuração do grafana pode ser feita por mapeamento de arquivos volumes 
com arquivos de configuração ou via environment variables colocadas no arquivo 
docker-compose com a definição do serviço. O próprio Grafana recomenda usar 
environment variables: Sempre ao iniciar o serviço as opções definidas via 
environment variable sobrescrevem os arquivos de configuração. 

Há no grafana um mapeamento um-a-um para cada opção do arquivo de configuração 
com uma environment variable: cada environment variable tem o formato 
GF_<secaoo do arquivo de configuracao>_<variavel a ser configurada> para poder 
fazer a configuração.

No repositório aqui, na pasta de exemplos de arquivos de configuração, há um 
arquivo de configuração do grafana completo com todas as seções e opções de 
configuração comentadas. Pode-se também consultar a 
[DOCUMENTAÇÃO OFICIAL](https://grafana.com/docs/v6.1/installation/configuration/) 
para ler mais sobre cada seção.

__Configuração criada__

Foram definidas as seguintes environment variables para configuração:

- GF_ANALYTICS_REPORTING_ENABLE: 'false' - Desativa o envio de estatísticas,
com a intenção de evitar sobrecarregar a rede já sobrecarregada.

- As outras opções definem o hostname e removem a autenticação obriatória para
facilitar o acesso: é definido acesso nível Admin sem autenticação.

##### Automação

O Grafana permite automatizar algumas personalizações, como fontes de dados 
consultadas para gerar as visualizações (Conexão automática com o Banco de 
Dados) e automatizar a geração dos Dashboards, para que um problema de disco ou
 de volume não cause a perda dos Dashboards criados.

A automatização destas opções é feita através de arquivos colocados na pasta 
`provisioning` apenas. Assim, esta pasta foi mapeada direto para o host aqui 
para facilitar o acesso, pasta `grafana/proviosing/` contendo as pastas 
datasources e dashboards. 

`provisioning/datasources/` permite colocar arquivos que definem conexões com 
bancos de dados. No repositório aqui temos um arquivo de conexão com a 
instância usada do InfluxDB. Caso precise mudar, para fazer novo arquivo é 
possível consultar a 
[DOCUMENTAÇÃO OFICIAL](https://grafana.com/docs/v6.1/administration/provisioning/)
ou ver um modelo padrão com comentários deixado na pasta de exemplos de 
arquivos de configuração aqui.

`provisioning/dashboards` permite colocar arquivos para automatizar a geração 
dos dashboards criados. Cada arquivo com extensão _.json_ corresponde à um 
Dashboard. Além destes, há tambem um arquivo _datasources.yaml_, que é 
diferente do arquivo de conexão com InfluxDB e deve ficar neste lugar para a 
automação. 

Os arquivos _.json_ de cada Dashboard não são feitos na mão: são gerados na 
interface gráfica do Grafana ao clicar em _salvar dashboard_. Já o arquivo 
datasources.yaml desta pasta tem um modelo padrão de exemplo nos exemplos 
de arquivos de configuração aqui e pode também ser consultado na
[DOCUMENTAÇÃO OFICIAL](https://grafana.com/docs/v6.1/administration/provisioning/).
 
__Automações feitas__

- Conexão com InfluxDB: As conexões com Bancos de dados estão em arquivos em 
`grafana/provisioning/datasources/`.

- Automação da geração Dashboards: todos os dashboards gerados até hoje estão 
na pasta `grafana/proviosing/dashboards/`, além do arquivo _datasources.yaml_ 
dela necessário para a automação.

__Como automatizar__

- __Novas fontes de dados (Conexões com Bancos de Dados)__: Colocar na pasta 
`grafana/provisioning/datasources/`, usando de modelo o arquivo exemplo na 
pasta de exemplos de arquivos de configuração aqui ou também consultando a 
documentação oficial (link acima aqui)

- __Novos Dashboards e edições em Dashboards existentes__: 

  - Crie um novo Dashboard ou edite um existente na interface gráfica do 
  Grafana.

  - Ao finalizar, clique em __salvar__. Será gerado um arquivo com extensão 
  _.json_. Copie o conteúdo gerado do arquivo.

  - Cole o conteúdo deste arquivo em um arquivo na pasta 
  `grafana/provisioning/dashboards/`: Se for uma edição, substitua o conteúdo 
  do arquivo correspondente pelo conteúdo copiado; Se for um novo Dashboard, 
  crie um novo arquivo com extensão _.json_ e cole o conteúdo copiado. 

#### C. Problemas de permissão <a name="permissao"></a>

A [IMAGEM DO CONTAINER GRAFANA](https://github.com/grafana/grafana/blob/master/packaging/docker/Dockerfile) 
define o usuário como usuário Grafana. Isso gera diversos problemas de 
permissão mais à frente, incluindo mapeamento de volume necessário para 
automatizar a geração de Dashboards e da conexão com Banco de Dados. Sendo 
assim, na definição do serviço Grafana no arquivo docker-compose foi 
necessário colocar o usuário como root (opção `user: "0"`).

#### D. Volumes e Armazenamento <a name="volumes"></a>

Além do diretório mapeado para automatizar a geração de dashboards e a conexão 
com banco de dados, há um volume na região Docker, grafana-storage, para 
armazenamento dos dados do Grafana que não estão automatizados.
