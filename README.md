# prometheus-grafana
Observabilidade: coletando métricas de uma aplicação com Prometheus

### Na raiz do projeto

* Pra ver o conteúdo do mysql via cmd

````bash
cat mysql/database.sql
````

* Pra ver o conteúdo do docker compose via cmd

`````bash
vim docker-compose.yaml
`````

* Pra sair do cmd :x

* Stop e Start no service do mysql instalado localmente para não dar conflito com container

````bash
brew services stop mysql
````

````bash
brew services start mysql
````

* Subir os serviços

````bash
docker-compose up
````

### Na raiz da aplicação

* Para buildar a aplicação

````bash
mvn clean package
````

* Script para aplicação subir

* Dar permissão ao script

`````bash
chmod +x start.sh
`````
* Executando o script

````bash
./start.sh
````

### Na raiz do projeto

* Para subir os containers

````bash
docker-compose up -d

 ✔ Network prometheus-grafana_api       Created                                                                                                                                                                             0.0s 
 ✔ Network prometheus-grafana_monit     Created                                                                                                                                                                             0.1s 
 ✔ Network prometheus-grafana_cache     Created                                                                                                                                                                             0.0s 
 ✔ Network prometheus-grafana_database  Created                                                                                                                                                                             0.0s 
 ✔ Network prometheus-grafana_proxy     Created                                                                                                                                                                             0.1s 
 ✔ Container redis-forum-api            Started                                                                                                                                                                             3.7s 
 ✔ Container mysql-forum-api            Started                                                                                                                                                                             3.9s 
 ✔ Container app-forum-api              Started                                                                                                                                                                             3.5s 
 ✔ Container proxy-forum-api            Started                                                                                                                                                                             4.7s 
 ✔ Container prometheus-forum-api       Started 
````

* É possível acessar os endpoints da aplicação e do healthcheck para ver as métricas do prometheus

* Para ver o Prometheus por interface gráfica, acessar http://localhost:9090

![Captura de Tela 2024-01-25 às 12.11.28 PM.png](imgs%2FCaptura%20de%20Tela%202024-01-25%20%C3%A0s%2012.11.28%20PM.png)

* Para ver as métricas do Prometheus, acessar http://localhost:9090/metrics

<p>O diretório prometheus_data está no path do usuário do Linux/Mac, e esse não é o mesmo usuário de execução do Prometheus, que logicamente não é o root.
Sendo assim, ele não tinha permissão para acessar esse compartilhamento. Para esse diretório específico ("prometheus_data"), liberamos um chmod 777.</p>

`````bash
~ prometheus-grafana % cd prometheus 
~ prometheus % ls -lh prometheus_data
total 40
drwxr-xr-x  3 dyanearaujo  staff    96B Jan 25 12:12 chunks_head
-rw-r--r--  1 dyanearaujo  staff     0B Jan 25 12:02 lock
-rw-r--r--  1 dyanearaujo  staff    20K Jan 25 12:09 queries.active
drwxr-xr-x  3 dyanearaujo  staff    96B Jan 25 12:02 wal

~ prometheus % ls -lhd prometheus_data
drwxrwxr-x@ 6 dyanearaujo  staff   192B Jan 25 12:02 prometheus_data
~ prometheus % chmod 777 prometheus_data
`````

* Client para consumir os endpoints da aplicação e através do script gera requisições de sucessos e erros automaticamente
* Dentro de client:

````bash
~ prometheus-grafana % cd client
~ client % ls
Dockerfile	client.sh
````

* Volte para a raiz do projeto e retire os comentários da parte do client api do docker-compose.yaml, para salvar esc e depois :wq

````bash
~ client % cd ..
~ prometheus-grafana % vim docker-compose.yaml

  client-forum-api:
    build:
      context: ./client/
      dockerfile: Dockerfile
    image: client-forum-api
    container_name: client-forum-api
    restart: unless-stopped
    networks:
      - proxy
    depends_on:
      - proxy-forum-api
````

* Para subir o client e os demais containers

````bash
docker-compose up -d
````

<p>O Prometheus utiliza quatro tipos de dados para métricas: Instant vector (vetor instantâneo), Range vector (vetor de uma série temporal), Float 
(vetor escalar) e String (vetor não-utilizado).</p>

* Instant Vector:
<p>Representa um vetor instantâneo.
Retorna uma série temporal com índices representados por labels.
Exemplo: logback_events_total(application="app-forum-api", instance="app-forum-api:8080", job="app-forum-api", level="debug").</p>

* Range Vector:
<p>Representa um vetor de uma série temporal.
Obtido ao adicionar um intervalo de tempo à consulta, como logback_events_total[1m].
Cada série temporal possui valores para cada scrape time do Prometheus.</p>

* Float (Dado Escalar):
<p>Representa um valor float simples.
Exemplo: hikaricp_connections_idle{application="app-forum-api", pool-"HikariPool-1"}.<p>

* String:
<p>Tipo não amplamente utilizado no contexto do Prometheus.
Raramente encontrado em casos de uso.
A análise de séries temporais, instant vectors e range vectors permite compreender e visualizar dados no Prometheus. A documentação do Prometheus 
fornece informações adicionais sobre esses tipos de dados e seu uso.</p>








