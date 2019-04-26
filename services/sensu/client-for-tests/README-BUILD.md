##### Porque fazer build sobre a imagem sstarcher/sensu:1.4.3 e não usá-la direto:
Para poder modificar o template do arquivo client.json (arquivo de configuração do cliente), de modo a permitir dois handlers para notificações de keepalive (queda e recuperação de uma máquina).

##### Diferenças do build para a imagem sstarcher/sensu:1.4.3

* **Arquivo client.json: os handlers do keepalive.** Ver em build/templates/client.json.tmpl
* Parâmetros (environment variables) no docker-compose do client
* Tempo para notificar warning e critical de queda: Poderia ter usado environment variables no compose mas modifiquei direto no template o tempo (build/templates/client.json.tmpl).
