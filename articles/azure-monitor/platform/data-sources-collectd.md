---
title: Coletar dados do CollectD no Azure Monitor | Microsoft Docs
description: CollectD é um daemon do Linux de software livre que coleta periodicamente dados de aplicativos e informações de nível de sistema.  Este artigo fornece informações sobre como coletar dados do CollectD no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: 488f273336da05738609333f911fe3a90ba59496
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86111976"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Coletar dados do CollectD em agentes do Linux no Azure Monitor
O [CollectD](https://collectd.org/) é um daemon do Linux de software livre que coleta periodicamente métricas de desempenho de aplicativos e informações de nível de sistema. Exemplos de aplicativos incluem a Máquina Virtual Java (JVM), o MySQL Server e o Nginx. Este artigo fornece informações sobre como coletar dados de desempenho do CollectD no Azure Monitor.

Uma lista completa de plug-ins disponíveis pode ser encontrada na [Tabela de Plug-ins](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Visão geral do CollectD](media/data-sources-collectd/overview.png)

A configuração do CollectD a seguir é incluída no agente do Log Analytics para Linux para rotear os dados do CollectD para o agente do Log Analytics para Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

```xml
LoadPlugin write_http

<Plugin write_http>
   <Node "oms">
      URL "127.0.0.1:26000/oms.collectd"
      Format "JSON"
      StoreRates true
   </Node>
</Plugin>
```

Além disso, se você estiver usando uma versão do CollectD anterior à 5.5, use a configuração a seguir em vez disso.

```xml
LoadPlugin write_http

<Plugin write_http>
   <URL "127.0.0.1:26000/oms.collectd">
      Format "JSON"
      StoreRates true
   </URL>
</Plugin>
```

A configuração do CollectD usa o plug-in padrão`write_http` para enviar dados de métrica de desempenho pela porta 26000 para o agente do Log Analytics para Linux. 

> [!NOTE]
> Essa porta pode ser configurada para uma porta definida de modo personalizado, se necessário.

O agente do Log Analytics para Linux também escuta métricas do CollectD na porta 26000 e, em seguida, converte-as em métricas de esquema do Azure Monitor. A seguir, a configuração do agente do Log Analytics para Linux `collectd.conf`.

```xml
<source>
   type http
   port 26000
   bind 127.0.0.1
</source>

<filter oms.collectd>
   type filter_collectd
</filter>
```

> [!NOTE]
> Coletado por padrão é definido para ler valores em um [intervalo](https://collectd.org/wiki/index.php/Interval)de 10 segundos. Como isso afeta diretamente o volume de dados enviados aos logs de Azure Monitor, talvez seja necessário ajustar esse intervalo dentro da configuração coletada para um bom equilíbrio entre os requisitos de monitoramento e os custos associados e o uso dos logs de Azure Monitor.

## <a name="versions-supported"></a>Versões com suporte
- Atualmente, o Azure Monitor dá suporte ao CollectD versão 4.8 e superior.
- O agente do Log Analytics para Linux v1.1.0-217 ou superior é necessário para coleta de métrica do CollectD.


## <a name="configuration"></a>Configuração
Veja a seguir as etapas básicas para configurar a coleta de dados do CollectD no Azure Monitor.

1. Configure o CollectD para enviar dados para o agente do Log Analytics para Linux usando o plug-in write_http.  
2. Configure o agente do Log Analytics para Linux para escutar os dados do CollectD na porta apropriada.
3. Reinicie CollectD e o agente do Log Analytics para Linux.

### <a name="configure-collectd-to-forward-data"></a>Configurar CollectD para encaminhar dados 

1. Para rotear dados do CollectD para o agente do Log Analytics para Linux, `oms.conf` precisa ser adicionado ao diretório de configuração do CollectD. O destino deste arquivo depende da distribuição Linux de seu computador.

    Se o diretório de configuração CollectD está localizado em /etc/collectd.d/:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf
    ```

    Se o diretório de configuração CollectD está localizado em /etc/collectd/collectd.conf.d/:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf
    ```

    >[!NOTE]
    >Para versões do CollectD anteriores à 5.5, você precisará modificar as marcas em `oms.conf` conforme mostrado acima.
    >

2. Copie collectd.conf para diretório de configuração omsagent do workspace desejado.

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf
    ```

3. Reinicie o CollectD e o agente do Log Analytics para Linux com os comandos a seguir.

    ```console
    sudo service collectd restart
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Métricas do CollectD para conversão de esquema do Azure Monitor
Para manter um modelo familiar entre as métricas de infraestrutura já coletadas pelo agente do Log Analytics para Linux e as novas métricas coletadas pelo CollectD, o mapeamento de esquema a seguir é usado:

| Campo Métrica do CollectD | Campo do Azure Monitor |
|:--|:--|
| `host` | Computador |
| `plugin` | Nenhum |
| `plugin_instance` | Nome da Instância<br>Se **plugin_instance** é *null*, então InstanceName="*_Total*" |
| `type` | ObjectName |
| `type_instance` | CounterName<br>Se **type_instance** é *null*, então CounterName=**blank** |
| `dsnames[]` | CounterName |
| `dstypes` | Nenhum |
| `values[]` | CounterValue |

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [registrar consultas](../log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções. 
* Use [campos personalizados](custom-fields.md) para analisar dados dos registros do syslog em campos individuais.
