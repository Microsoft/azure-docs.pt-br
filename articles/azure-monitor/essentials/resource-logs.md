---
title: Logs de recursos do Azure
description: Saiba como transmitir logs de recursos do Azure para um espaço de trabalho Log Analytics no Azure Monitor.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: bwren
ms.openlocfilehash: 2435e4ed16889d9d4701b6047c0a1f602ee7ae91
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558688"
---
# <a name="azure-resource-logs"></a>Logs de recursos do Azure
Os logs de recursos do Azure são [logs de plataforma](../essentials/platform-logs-overview.md) que fornecem informações sobre as operações que foram executadas em um recurso do Azure. O conteúdo de logs de recursos varia de acordo com o tipo de recurso e serviço do Azure. Os logs de recursos não são coletados por padrão. Você deve criar uma configuração de diagnóstico para cada recurso do Azure para enviar seus logs de recursos para um Log Analytics espaço de trabalho para usar com [logs de Azure monitor](../logs/data-platform-logs.md), hubs de eventos do Azure para encaminhar fora do Azure ou para o armazenamento do Azure para arquivamento.

Consulte [criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes](../essentials/diagnostic-settings.md) para obter detalhes sobre como criar uma configuração de diagnóstico e [implantar Azure monitor em escala usando Azure Policy](../deploy-scale.md) para obter detalhes sobre como usar Azure Policy para criar automaticamente uma configuração de diagnóstico para cada recurso do Azure que você criar.

## <a name="send-to-log-analytics-workspace"></a>Enviar para o workspace do Log Analytics
 Envie logs de recursos para um espaço de trabalho Log Analytics para habilitar os recursos de [logs de Azure monitor](../logs/data-platform-logs.md) que incluem o seguinte:

- Correlacione os dados de log de recursos com outros dados de monitoramento coletados pelo Azure Monitor.
- Consolide entradas de log de vários recursos, assinaturas e locatários do Azure em um único local para análise.
- Use consultas de log para executar análises complexas e obter informações aprofundadas sobre dados de log.
- Use alertas de log com lógica de alerta complexa.

[Crie uma configuração de diagnóstico](../essentials/diagnostic-settings.md) para enviar logs de recursos para um espaço de trabalho log Analytics. Esses dados são armazenados em tabelas, conforme descrito em [estrutura de logs de Azure monitor](../logs/data-platform-logs.md). As tabelas usadas pelos logs de recursos dependem do tipo de coleção que o recurso está usando:

- Diagnóstico do Azure-todos os dados gravados estão na tabela [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) .
- Dados específicos do recurso são gravados em uma tabela individual para cada categoria do recurso.

### <a name="azure-diagnostics-mode"></a>Modo de diagnóstico do Azure 
Nesse modo, todos os dados de qualquer configuração de diagnóstico serão coletados na tabela [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) . Esse é o método herdado usado hoje pela maioria dos serviços do Azure. Como vários tipos de recursos enviam dados para a mesma tabela, seu esquema é o superconjunto dos esquemas de todos os tipos de dados diferentes que estão sendo coletados. Consulte [referência de AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) para obter detalhes sobre a estrutura desta tabela e como ela funciona com esse número potencialmente grande de colunas.

Considere o exemplo a seguir em que as configurações de diagnóstico estão sendo coletadas no mesmo espaço de trabalho para os seguintes tipos de dados:

- Logs de auditoria do serviço 1 (tendo um esquema que consiste nas colunas A, B e C)  
- Logs de erros do serviço 1 (tendo um esquema que consiste em colunas D, E e F)  
- Logs de auditoria do serviço 2 (tendo um esquema que consiste em colunas G, H e I)  

A tabela AzureDiagnostics terá a seguinte aparência:  

| ResourceProvider    | Categoria     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft. Service1 | AuditLogs    | X1 | Y1 | z1 |    |    |    |    |    |    |
| Microsoft. Service1 | ErrorLogs    |    |    |    | q1 | W1 | E1 |    |    |    |
| Microsoft. Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | L1 |
| Microsoft. Service1 | ErrorLogs    |    |    |    | q2 | W2 | E2 |    |    |    |
| Microsoft. Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | MB |
| Microsoft. Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Específico do recurso
Nesse modo, as tabelas individuais no espaço de trabalho selecionado são criadas para cada categoria selecionada na configuração de diagnóstico. Esse método é recomendado, pois torna muito mais fácil trabalhar com os dados em consultas de log, fornece melhor capacidade de descoberta de esquemas e sua estrutura, melhora o desempenho em tempos de consulta e latência de ingestão e a capacidade de conceder direitos de RBAC do Azure em uma tabela específica. Todos os serviços do Azure eventualmente serão migrados para o modo de Resource-Specific. 

O exemplo acima resultaria em três tabelas sendo criadas:
 
- Tabela *Service1AuditLogs* da seguinte maneira:

    | Provedor de recursos | Categoria | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | X1 | Y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- Tabela *Service1ErrorLogs* da seguinte maneira:  

    | Provedor de recursos | Categoria | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  q1 | W1 | E1 |
    | Service1 | ErrorLogs |  q2 | W2 | E2 |
    | ... |

- Tabela *Service2AuditLogs* da seguinte maneira:  

    | Provedor de recursos | Categoria | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | k1 | L1|
    | Service2 | AuditLogs | j3 | k3 | MB|
    | ... |



### <a name="select-the-collection-mode"></a>Selecione o modo de coleta
A maioria dos recursos do Azure gravará dados no espaço de trabalho no modo de **diagnóstico do Azure** ou **específico do recurso,** sem lhe dar uma opção. Consulte a [documentação de cada serviço](./resource-logs-schema.md) para obter detalhes sobre qual modo ele usa. Todos os serviços do Azure eventualmente usarão o modo de Resource-Specific. Como parte dessa transição, alguns recursos permitirão que você selecione um modo na configuração de diagnóstico. Especifique o modo específico do recurso para as novas configurações de diagnóstico, pois isso torna os dados mais fáceis de gerenciar e pode ajudá-lo a evitar migrações complexas em uma data posterior.
  
   ![Seletor de modo de configurações de diagnóstico](media/resource-logs/diagnostic-settings-mode-selector.png)

> [!NOTE]
> Para obter um exemplo de como definir o modo de coleta usando um modelo do Resource Manager, consulte [exemplos de modelo do Resource Manager para configurações de diagnóstico em Azure monitor](./resource-manager-diagnostic-settings.md#diagnostic-setting-for-recovery-services-vault).


Você pode modificar uma configuração de diagnóstico existente para o modo específico do recurso. Nesse caso, os dados que já foram coletados permanecerão na tabela _AzureDiagnostics_ até que sejam removidos de acordo com sua configuração de retenção para o espaço de trabalho. Novos dados serão coletados na tabela dedicada. Use o operador [Union](/azure/kusto/query/unionoperator) para consultar dados em ambas as tabelas.

Continue a assistir ao blog de [atualizações do Azure](https://azure.microsoft.com/updates/) para obter anúncios sobre os serviços do Azure que dão suporte ao modo de Resource-Specific.


## <a name="send-to-azure-event-hubs"></a>Enviar para hubs de eventos do Azure
Envie logs de recursos para um hub de eventos para enviá-los para fora do Azure, por exemplo, para um SIEM de terceiros ou outras soluções do log Analytics. Os logs de recursos dos hubs de eventos são consumidos no formato JSON com um `records` elemento que contém os registros em cada carga. O esquema depende do tipo de recurso, conforme descrito em [comum e em esquema específico de serviço para logs de recursos do Azure](resource-logs-schema.md).

Veja a seguir exemplos de dados de saída dos hubs de eventos para um log de recursos:

```json
{
    "records": [
        {
            "time": "2019-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2019-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

## <a name="send-to-azure-storage"></a>Enviar ao Armazenamento do Azure
Envie logs de recursos para o armazenamento do Azure para remantê-lo para arquivamento. Depois de criar a configuração de diagnóstico, um contêiner de armazenamento é criado na conta de armazenamento assim que um evento ocorre em uma das categorias de log habilitadas. Os BLOBs dentro do contêiner usam a seguinte convenção de nomenclatura:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, o blob de um grupo de segurança de rede pode ter um nome semelhante ao seguinte:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro de uma hora especificada na URL do blob (por exemplo, h=12). Durante a hora presente, os eventos são acrescentados ao arquivo PT1H.json conforme eles ocorrem. O valor de minuto (m = 00) é sempre 00, pois os eventos do log de recursos são divididos em BLOBs individuais por hora.

Dentro do PT1H.jsno arquivo, cada evento é armazenado com o formato a seguir. Isso usará um esquema comum de nível superior, mas será exclusivo para cada serviço do Azure, conforme descrito em [esquema de logs de recursos](./resource-logs-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Os logs de plataforma são gravados no armazenamento de BLOB usando [linhas JSON](http://jsonlines.org/), em que cada evento é uma linha e o caractere de linha nova indica um novo evento. Esse formato foi implementado em novembro de 2018. Antes dessa data, os logs foram gravados no armazenamento de blob como uma matriz JSON de registros, conforme descrito em [preparar para a alteração de formato para Azure monitor os logs de plataforma arquivados em uma conta de armazenamento](resource-logs-blob-format.md).


## <a name="next-steps"></a>Próximas etapas

* [Leia mais sobre os logs de recursos](../essentials/platform-logs-overview.md).
* [Crie configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes](../essentials/diagnostic-settings.md).
