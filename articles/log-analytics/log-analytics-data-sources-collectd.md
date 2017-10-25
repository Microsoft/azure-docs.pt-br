---
title: Coletar dados do CollectD no OMS Log Analytics | Microsoft Docs
description: "CollectD é um daemon do Linux de software livre que coleta periodicamente dados de aplicativos e informações de nível de sistema.  Este artigo fornece informações sobre a coleta de dados do CollectD no Log Analytics."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: magoedte
ms.openlocfilehash: a63b15ca5126b45451f0694c9ee75d7b67b1ceaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="collect-data-from-collectd-on-linux-agents-in-log-analytics"></a>Coletar dados do CollectD em agentes do Linux no Log Analytics
O [CollectD](https://collectd.org/) é um daemon do Linux de software livre que coleta periodicamente métricas de desempenho de aplicativos e informações de nível de sistema. Exemplos de aplicativos incluem a Máquina Virtual Java (JVM), o MySQL Server e o Nginx. Este artigo fornece informações sobre a coleta de dados de desempenho do CollectD no Log Analytics.

Uma lista completa de plug-ins disponíveis pode ser encontrada na [Tabela de Plug-ins](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Visão geral do CollectD](media/log-analytics-data-sources-collectd/overview.png)

A configuração do CollectD a seguir é incluída no Agente do OMS para Linux para rotear os dados do CollectD para esse Agente.

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Além disso, se você estiver usando uma versão do CollectD anterior à 5.5, use a configuração a seguir em vez disso.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

A configuração do CollectD usa o plug-in padrão`write_http` para enviar dados de métrica de desempenho pela porta 26000 para o Agente do OMS para Linux. 

> [!NOTE]
> Essa porta pode ser configurada para uma porta definida de modo personalizado, se necessário.

O Agente do OMS para Linux também escuta métricas do CollectD na porta 26000 e, em seguida, converte-as em métricas de esquema do OMS. A seguir está a configuração do Agente do OMS para Linux `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>Versões com suporte
- O Log Analytics dá suporte atualmente às versões 4.8 e superiores do CollectD.
- O Agente do OMS para Linux v1.1.0-217 ou superior é necessário para coleta de métrica do CollectD.


## <a name="configuration"></a>Configuração
A seguir estão as etapas básicas para configurar a coleta de dados do CollectD no Log Analytics.

1. Configure o CollectD para enviar dados para o Agente do OMS para Linux usando o plug-in write_http.  
2. Configure o Agente do OMS para Linux para escutar os dados do CollectD na porta apropriada.
3. Reinicie o CollectD e o Agente do OMS para Linux.

### <a name="configure-collectd-to-forward-data"></a>Configurar CollectD para encaminhar dados 

1. Para rotear dados do CollectD para o Agente do OMS para Linux, `oms.conf` precisa ser adicionado ao diretório de configuração do CollectD. O destino deste arquivo depende da distribuição Linux de seu computador.

    Se o diretório de configuração CollectD está localizado em /etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Se o diretório de configuração CollectD está localizado em /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Para versões do CollectD anteriores à 5.5, você precisará modificar as marcas em `oms.conf` conforme mostrado acima.
    >

2. Copie collectd.conf para diretório de configuração omsagent do espaço de trabalho desejado.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Reinicie o CollectD e o Agente do OMS para Linux com os comandos a seguir.

    sudo service collectd restart  sudo /opt/microsoft/omsagent/bin/service_control restart

## <a name="collectd-metrics-to-log-analytics-schema-conversion"></a>Métricas de CollectD para conversão de esquema do Log Analytics
Para manter um modelo familiar entre as métricas de infraestrutura já coletadas pelo Agente do OMS para Linux e as novas métricas coletadas pelo CollectD, o mapeamento de esquema a seguir é usado:

| Campo Métrica do CollectD | Campo Log Analytics |
|:--|:--|
| host | Computador |
| plug-in | Nenhum |
| plugin_instance | Nome da Instância<br>Se **plugin_instance** é *null*, então InstanceName="*_Total*" |
| type | ObjectName |
| type_instance | CounterName<br>Se **type_instance** é *null*, então CounterName=**blank** |
| dsnames[] | CounterName |
| dstypes | Nenhum |
| values[] | CounterValue |

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções. 
* Use [campos personalizados](log-analytics-custom-fields.md) para analisar dados dos registros do syslog em campos individuais.

