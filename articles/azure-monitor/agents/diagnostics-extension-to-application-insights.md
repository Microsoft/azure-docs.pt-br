---
title: Enviar dados de Diagnóstico do Azure para Application Insights
description: Atualize a configuração pública do Diagnóstico do Azure para enviar dados ao Application Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: b9e9d6b1b5939804b24fd523bf8b7444ed41178f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708587"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Enviar dados de diagnóstico do Serviço de Nuvem, da máquina virtual ou do Service Fabric ao Application Insights
Serviços de nuvem, máquinas virtuais, conjuntos de dimensionamento de máquinas virtuais e o Service Fabric usam a extensão do Diagnóstico do Azure para coletar dados.  O Diagnóstico do Azure envia dados às tabelas do Armazenamento do Azure.  No entanto, também é possível redirecionar todos os dados, ou um subconjunto deles, para outros locais usando a extensão do Diagnóstico do Azure 1.5 ou posterior.

Este artigo descreve como enviar dados da extensão do Diagnóstico do Azure para o Application Insights.

## <a name="diagnostics-configuration-explained"></a>Explicação da configuração do Diagnóstico
A extensão do Diagnóstico do Azure 1.5 introduziu coletores, que são locais adicionais para os quais você pode enviar dados de diagnóstico.

Exemplo de configuração de um coletor para o Application Insights:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- O atributo **Sink** *name* é um valor de cadeia de caracteres que identifica exclusivamente o coletor.

- O elemento **ApplicationInsights** especifica a chave de instrumentação do recurso do Application Insights para onde os dados do Diagnóstico do Azure são enviados.
    - Se você não tiver um recurso existente do Application Insights, confira [Criar um novo recurso do Application Insights](../app/create-new-resource.md) para saber mais sobre como criar um recurso e obter a chave de instrumentação.
    - Se você estiver desenvolvendo um Serviço de Nuvem com o Azure SDK 2.8 e posterior, essa chave de instrumentação é preenchida automaticamente. O valor tem base na configuração do serviço **APPINSIGHTS_INSTRUMENTATIONKEY** ao empacotar o projeto de Serviço de Nuvem. Consulte [Usar o Application Insights com o Serviços de Nuvem](../app/cloudservices.md).

- O elemento **Channels** contém um ou mais elementos **Channel**.
    - O atributo *name* refere-se exclusivamente a esse canal.
    - O atributo *loglevel* permite que você especifique o nível de log permitido pelo canal. Os níveis de log disponíveis, organizados do que contém mais para o que contém menos informações, são:
        - Detalhado
        - Informações
        - Aviso
        - Erro
        - Crítico

Um canal funciona como um filtro e permite que você selecione níveis de log específicos para enviar ao coletor de destino. Por exemplo, você poderia coletar logs detalhados e enviá-los ao armazenamento, mas enviar apenas os Erros ao coletor.

O gráfico a seguir mostra essa relação.

![Configuração pública do Diagnóstico](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

O gráfico a seguir resume os valores de configuração e como eles funcionam. Você pode incluir vários coletores na configuração em diferentes níveis na hierarquia. O coletor no nível superior atua como uma configuração global, e o especificado no elemento individual atua como uma substituição daquela configuração global.

![Configuração dos coletores de diagnóstico com o Application Insights](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Exemplo de configuração completa do coletor
Veja um exemplo completo do arquivo de configuração pública que
1. envia todos os erros ao Application Insights (especificado no nó **DiagnosticMonitorConfiguration**)
2. também envia logs de nível Detalhado para os Logs do Aplicativo (especificado no nó **Logs**).

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
Na configuração anterior, as linhas a seguir apresentam estes significados:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Enviar todos os dados que estão sendo coletados pelo Diagnóstico do Azure

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Enviar somente logs de erro para o coletor do Application Insights

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Enviar logs de aplicativo Detalhados para o Application Insights

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Limitações

- **Os canais só registro o tipo e não contadores de desempenho.** Se você especificar um canal com um elemento contador de desempenho, ele será ignorado.
- **O nível de log para um canal não pode exceder o nível de log do que está sendo coletado pelo Diagnóstico do Azure.** Por exemplo: não é possível coletar erros do Log de Aplicativo no elemento Logs e tentar enviar logs Detalhados ao coletor do Application Insights. O atributo *scheduledTransferLogLevelFilter* sempre deve coletar uma quantidade igual ou maior de logs que a quantidade de logs que você está tentando enviar para um coletor.
- **Não é possível enviar dados de blob coletados pela extensão do Diagnóstico do Azure ao Application Insights.** Por exemplo, qualquer coisa especificada no nó *diretórios* . No caso de Despejos de Memória, o despejo de memória real é enviado ao armazenamento de blobs, e somente uma notificação da geração do despejo é enviada ao Application Insights.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [exibir as informações de diagnóstico do Azure](../app/cloudservices.md) no Application Insights.
* Use o [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) para habilitar a extensão do Diagnóstico do Azure para seu aplicativo.
* Use o [Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) para habilitar a extensão do Diagnóstico do Azure para seu aplicativo

