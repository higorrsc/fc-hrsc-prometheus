# Projeto de Monitoramento com Prometheus e Grafana

Este repositório contém uma aplicação Go de exemplo, juntamente com uma stack de monitoramento completa utilizando Prometheus, Grafana, cAdvisor e Redis, todos orquestrados com Docker Compose.

## Visão Geral

O objetivo deste projeto é demonstrar como instrumentar uma aplicação para expor métricas customizadas e como coletar, visualizar e monitorar essas métricas, bem como as métricas da infraestrutura.

A stack é composta pelos seguintes serviços:

- **app**: Uma aplicação web em Go que expõe métricas customizadas no endpoint `/metrics`.
- **prometheus**: Coleta e armazena as métricas da `app` e do `cadvisor`.
- **grafana**: Permite a criação de dashboards para visualizar as métricas coletadas pelo Prometheus.
- **cadvisor**: Coleta métricas sobre os contêineres Docker em execução.
- **redis**: Um banco de dados em memória, incluído como um exemplo de serviço a ser monitorado.

## Como Executar

### Pré-requisitos

- Docker
- Docker Compose

### Passos

1. Clone este repositório:

   ```bash
   git clone higorrsc/fc-hrsc-prometheus
   cd fc-hrsc-prometheus
   ```

2. Inicie todos os serviços com o Docker Compose:

   ```bash
   docker-compose up -d
   ```

3. Após a execução, os serviços estarão disponíveis nos seguintes endereços:

   - **Aplicação Go**: [http://localhost:8181](http://localhost:8181)
   - **Métricas da Aplicação**: [http://localhost:8181/metrics](http://localhost:8181/metrics)
   - **Prometheus**: [http://localhost:9090](http://localhost:9090)
   - **Grafana**: [http://localhost:3000](http://localhost:3000) (login padrão: `admin`/`admin`)
   - **cAdvisor**: [http://localhost:8080](http://localhost:8080)

## Métricas da Aplicação (`app`)

A aplicação Go expõe as seguintes métricas customizadas, que podem ser visualizadas no Prometheus:

- `goapp_online_users`: Um `Gauge` que simula o número de usuários online em tempo real.
- `goapp_http_requests_total`: Um `Counter` que conta o número total de requisições HTTP recebidas pela aplicação.
- `goapp_http_request_duration`: Um `Histogram` que mede a duração das requisições HTTP para os diferentes handlers (`/` e `/contact`).

## Configuração do Prometheus

O arquivo `config/prometheus.yml` define os alvos que o Prometheus deve "raspar" (scrape):

- `prometheus`: O próprio serviço do Prometheus.
- `cadvisor`: O serviço do cAdvisor para métricas de contêineres.
- `goapp`: A nossa aplicação Go.

```yaml
scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
  - job_name: cadvisor
    static_configs:
      - targets: ["cadvisor:8080"]
  - job_name: goapp
    static_configs:
      - targets: ["app:8181"]
```

## Como Parar a Aplicação

Para parar e remover todos os contêineres, redes e volumes, execute:

```bash
docker-compose down
```
