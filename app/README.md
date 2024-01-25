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