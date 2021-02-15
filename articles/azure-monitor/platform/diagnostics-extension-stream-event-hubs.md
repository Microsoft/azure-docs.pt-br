---
title: Enviar dados da extensão de diagnóstico do Windows Azure para os hubs de eventos do Azure
description: Configure a extensão de diagnóstico no Azure Monitor para enviar dados para o Hub de eventos do Azure para que você possa encaminhá-lo para locais fora do Azure.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 979535b1f9a237f6975908178fb1e5ed819181b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82233458"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Enviar dados da extensão de diagnóstico do Windows Azure para os hubs de eventos do Azure
A extensão de diagnóstico do Azure é um agente no Azure Monitor que coleta dados de monitoramento do sistema operacional convidado e cargas de trabalho de máquinas virtuais do Azure e outros recursos de computação. Este artigo descreve como enviar dados da extensão de diagnóstico do Windows Azure (WAD) para os [hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/) para que você possa encaminhar para locais fora do Azure.

## <a name="supported-data"></a>Dados com suporte

Os dados coletados do sistema operacional convidado que podem ser enviados aos hubs de eventos incluem o seguinte. Outras fontes de dados coletadas por WAD, incluindo logs do IIS e despejos de memória, não podem ser enviadas para os hubs de eventos.

* Eventos de ETW (Rastreamento de Eventos para Windows)
* Contadores de desempenho
* Logs de eventos do Windows, incluindo logs de aplicativos no log de eventos do Windows
* Logs de infraestrutura do Diagnóstico do Azure

## <a name="prerequisites"></a>Pré-requisitos

* Extensão de diagnóstico do Windows 1,6 ou superior. Consulte [diagnóstico do Azure versões de esquema de configuração de extensão e o histórico](diagnostics-extension-versions.md) para obter um histórico de versão e uma [visão geral de extensão de diagnóstico do Azure](diagnostics-extension-overview.md) para recursos com suporte.
* O namespace de hubs de eventos sempre deve ser provisionado. Consulte Introdução [aos hubs de eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) para obter detalhes.


## <a name="configuration-schema"></a>Esquema de configuração
Consulte [instalar e configurar a extensão de diagnóstico do Windows Azure (wad)](diagnostics-extension-windows-install.md) para diferentes opções para habilitar e configurar a extensão de diagnóstico e [diagnóstico do Azure esquema de configuração](diagnostics-extension-schema-windows.md) para uma referência do esquema de configuração. O restante deste artigo descreverá como usar essa configuração para enviar dados para um hub de eventos. 

O Diagnóstico do Azure sempre envia logs e métricas para uma conta de armazenamento do Azure. Você pode configurar um ou mais *coletores de dados* que enviam dados para locais adicionais. Cada coletor é definido no [elemento SinksConfig](diagnostics-extension-schema-windows.md#sinksconfig-element) da configuração pública com informações confidenciais na configuração privada. Essa configuração para os hubs de eventos usa os valores na tabela a seguir.

| Propriedade | Descrição |
|:---|:---|
| Nome | Nome descritivo do coletor. Usado na configuração para especificar quais fontes de dados enviar para o coletor. |
| Url  | URL do hub de eventos no formato \<event-hubs-namespace\> . ServiceBus.Windows.NET/ \<event-hub-name\> .          |
| SharedAccessKeyName | Nome de uma política de acesso compartilhado para o Hub de eventos que tem pelo menos a autoridade de **envio** . |
| SharedAccessKey     | Chave primária ou secundária da política de acesso compartilhado para o Hub de eventos. |

As configurações públicas e privadas de exemplo são mostradas abaixo. Essa é uma configuração mínima com um único contador de desempenho e log de eventos para ilustrar como configurar e usar o coletor de dados do hub de eventos. Consulte [diagnóstico do Azure esquema de configuração](diagnostics-extension-schema-windows.md) para obter um exemplo mais complexo.

### <a name="public-configuration"></a>Configuração pública

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120,
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Configuração privada

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Opções de configuração
Para enviar dados para um coletor de dados, especifique o atributo **Sinks** no nó da fonte de dados. Onde você coloca o atributo **Sinks** determina o escopo da atribuição. No exemplo a seguir, o atributo **Sinks** é definido para o nó **PerformanceCounters** , que fará com que todos os contadores de desempenho filho sejam enviados para o Hub de eventos.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


No exemplo a seguir, o atributo **Sinks** é aplicado diretamente a três contadores, o que fará com que apenas esses contadores de desempenho sejam enviados para o Hub de eventos. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Validando a configuração
Você pode usar uma variedade de métodos para validar que os dados estão sendo enviados para o Hub de eventos. o método simples é usar a captura de hubs de eventos conforme descrito em [eventos de captura por meio dos hubs de eventos do Azure no armazenamento de BLOBs do Azure ou Azure data Lake Storage](../../event-hubs/event-hubs-capture-overview.md). 


## <a name="troubleshoot-event-hubs-sinks"></a>Solucionar problemas dos coletores dos Hubs de Eventos

- Examine a tabela de armazenamento do Azure **WADDiagnosticInfrastructureLogsTable** que contém logs e erros para diagnóstico do Azure si mesmo. Uma opção é usar uma ferramenta, como o [Gerenciador de Armazenamento do Azure](https://www.storageexplorer.com) , para se conectar a essa conta de armazenamento, exibir essa tabela e adicionar uma consulta por carimbo de data/hora nas últimas 24 horas. Você pode usar a ferramenta para exportar um arquivo .csv e abri-lo em um aplicativo, como o Microsoft Excel. O Excel facilita a pesquisa de cadeias de caracteres de cartão, como **EventHubs**, para ver qual erro é relatado.  

- Verifique se o seu hub de eventos foi provisionado com êxito. Todas as informações de conexão na seção **PrivateConfig** da configuração devem corresponder aos valores de seu recurso, como visto no Portal. Verifique se você tem uma política de SAS definida (*SendRule* no exemplo) no portal e se a permissão de *envio* é concedida.  

## <a name="next-steps"></a>Próximas etapas

* [Visão geral de Hubs de Eventos](../../event-hubs/event-hubs-about.md)
* [Criar um hub de eventos](../../event-hubs/event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



