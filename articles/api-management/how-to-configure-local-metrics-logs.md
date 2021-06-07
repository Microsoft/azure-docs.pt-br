---
title: Configurar métricas locais e logs para o gateway auto-hospedado do gerenciamento de API do Azure | Microsoft Docs
description: Saiba como configurar métricas locais e logs para o gateway auto-hospedado do gerenciamento de API do Azure em um kubernetes webhdfs
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/01/2021
ms.author: apimpm
ms.openlocfilehash: 2b66663c9ee8033bcb12bfac57964ea0eafecdac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594178"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Configurar métricas locais e logs para o gateway auto-hospedado do gerenciamento de API do Azure

Este artigo fornece detalhes sobre a configuração de métricas locais e logs para o [Gateway auto-hospedado](./self-hosted-gateway-overview.md) implantado em um cluster kubernetes. Para configurar logs e métricas de nuvem, consulte [Este artigo](how-to-configure-cloud-metrics-logs.md). 

## <a name="metrics"></a>Métricas
O gateway auto-hospedado dá suporte a [estatísticas](https://github.com/statsd/statsd), que se tornou um protocolo unificado para coleta e agregação de métricas. Esta seção percorre as etapas de implantação de estatísticas em kubernetes, configuração do gateway para emissão de métricas por meio de estatísticas e uso de [Prometheus](https://prometheus.io/) para monitorar as métricas. 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>Implantar estatísticas e Prometheus no cluster

Abaixo está um exemplo de configuração de YAML para implantar estatísticas e Prometheus no cluster kubernetes em que um gateway auto-hospedado é implantado. Ele também cria um [serviço](https://kubernetes.io/docs/concepts/services-networking/service/) para cada um. O gateway auto-hospedado publicará métricas no serviço de estatísticas. Acessaremos o painel do Prometheus por meio de seu serviço.   

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: mcr.microsoft.com/aks/hcp/prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: mcr.microsoft.com/oss/prometheus/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

Salve as configurações em um arquivo chamado `metrics.yaml` e use o comando abaixo para implantar tudo no cluster:

```console
kubectl apply -f metrics.yaml
```

Após a conclusão da implantação, execute o comando abaixo para verificar se os pods estão em execução. Observe que o nome do pod será diferente. 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

Execute o comando abaixo para verificar se os serviços estão em execução. Anote o `CLUSTER-IP` e `PORT` o do serviço de estatísticas, precisamos dele mais tarde. Você pode visitar o painel do Prometheus usando seu `EXTERNAL-IP` e `PORT` .

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>Configurar o gateway auto-hospedado para emitir métricas

Agora que ambas as estatísticas e Prometheus foram implantadas, podemos atualizar as configurações do gateway auto-hospedado para iniciar a emissão de métricas por meio de estatísticas. O recurso pode ser habilitado ou desabilitado usando a `telemetry.metrics.local` chave no ConfigMap da implantação de gateway de hospedagem interna com opções adicionais. Abaixo está uma análise das opções disponíveis:

| Campo  | Padrão | Descrição |
| ------------- | ------------- | ------------- |
| telemetria. métricas. local  | `none` | Habilita o registro em log por meio de estatísticas. O valor pode ser `none` , `statsd` . |
| telemetria. métricas. local. stats. EndPoint  | N/D | Especifica o ponto de extremidade com estatísticas. |
| telemetria. métricas. local. stats. amostragem  | N/D | Especifica a taxa de amostragem de métricas. O valor pode estar entre 0 e 1. por exemplo, `0.5`|
| telemetria. Metrics. local. stated. Tag-Format  | N/D | [Formato de marcação](https://github.com/prometheus/statsd_exporter#tagging-extensions)de exportador com estatísticas. O valor pode ser `none` , `librato` , `dogStatsD` , `influxDB` . |

Aqui está uma configuração de exemplo:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

Atualize o arquivo YAML da implantação de gateway de hospedagem interna com as configurações acima e aplique as alterações usando o comando abaixo: 

```console
kubectl apply -f <file-name>.yaml
 ```

Para obter as alterações de configuração mais recentes, reinicie a implantação de gateway usando o comando abaixo:

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>Exibir as métricas

Agora que tudo está implantado e configurado, o gateway auto-hospedado deve relatar métricas por meio de estatísticas. O Prometheus coletará as métricas de estatísticas. Vá para o painel do Prometheus usando o `EXTERNAL-IP` e `PORT` do serviço Prometheus. 

Faça algumas chamadas à API por meio do gateway auto-hospedado, se tudo estiver configurado corretamente, você poderá exibir as métricas abaixo:

| Métrica  | Descrição |
| ------------- | ------------- |
| Requests  | Número de solicitações de API no período |
| DurationInMS | Número de milissegundos do momento em que o gateway recebeu a solicitação até o momento em que a resposta foi enviada por completo |
| BackendDurationInMS | Número de milissegundos gastos na E/S do back-end no total (conectando, enviando e recebendo bytes)  |
| ClientDurationInMS | Número de milissegundos gastos na E/S geral do cliente (conectando, enviando e recebendo bytes)  |

## <a name="logs"></a>Logs

O gateway auto-hospedado gera logs para `stdout` e `stderr` por padrão. Você pode exibir facilmente os logs usando o seguinte comando:

```console
kubectl logs <pod-name>
```

Se o seu gateway auto-hospedado for implantado no serviço kubernetes do Azure, você poderá habilitar [Azure monitor para contêineres](../azure-monitor/containers/container-insights-overview.md) coletar `stdout` e `stderr` de suas cargas de trabalho e exibir os logs em log Analytics. 

O gateway auto-hospedado também dá suporte a vários protocolos `localsyslog` , incluindo, `rfc5424` e `journal` . A tabela abaixo resume todas as opções com suporte. 

| Campo  | Padrão | Descrição |
| ------------- | ------------- | ------------- |
| telemetria. logs. STD  | `text` | Habilita o registro em log para fluxos padrão. O valor pode ser `none` , `text` , `json` |
| telemetria. logs. local  | `none` | Habilita o log local. O valor pode ser `none` , `auto` ,, `localsyslog` `rfc5424` , `journal`  |
| telemetria. logs. local. localsyslog. EndPoint  | N/D | Especifica o ponto de extremidade localsyslog.  |
| telemetria. logs. local. localsyslog. Facility  | N/D | Especifica o [código de instalação](https://en.wikipedia.org/wiki/Syslog#Facility)localsyslog. por exemplo, `7` 
| telemetria. logs. local. rfc5424. EndPoint  | N/D | Especifica o ponto de extremidade rfc5424.  |
| telemetria. logs. local. rfc5424. Facility  | N/D | Especifica o código de instalação por [rfc5424](https://tools.ietf.org/html/rfc5424). por exemplo, `7`  |
| telemetria. logs. local. Journal. EndPoint  | N/D | Especifica o ponto de extremidade do diário.  |

Aqui está um exemplo de configuração de log local:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```
 
## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o gateway auto-hospedado, confira [visão geral do gateway de gerenciamento de API do Azure-hospedado](self-hosted-gateway-overview.md)
* Saiba mais sobre como [configurar e persistir logs na nuvem](how-to-configure-local-metrics-logs.md)
