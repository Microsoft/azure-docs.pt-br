---
title: Coletar & analisar logs de recursos
description: Registre e analise eventos de log de recursos para o registro de contêiner do Azure, como autenticação, push de imagem e pull de imagem.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 8b05d34e6c50fea3760e30d28f59e55d8c5f211a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348578"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Logs de registro de contêiner do Azure para avaliação de diagnóstico e auditoria

Este artigo explica como coletar dados de log para um registro de contêiner do Azure usando os recursos de [Azure monitor](../azure-monitor/overview.md). Azure Monitor coleta [logs de recursos](../azure-monitor/platform/platform-logs-overview.md) (anteriormente chamados de *logs de diagnóstico*) para eventos controlados pelo usuário no registro. Colete e consuma esses dados para atender às necessidades, como:

* Auditar eventos de autenticação de registro para garantir a segurança e a conformidade 

* Forneça uma trilha de atividade completa em artefatos de registro, como eventos de pull e pull, para que você possa diagnosticar problemas operacionais com o registro 

Coletar dados de log de recursos usando Azure Monitor pode incorrer em custos adicionais. Consulte [Azure monitor preços](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Eventos de repositório

Os seguintes eventos de nível de repositório para imagens e outros artefatos estão registrados no momento:

* **Push**
* **Pull**
* **Formatação**
* **Excluir** (incluindo eventos de exclusão do repositório)
* **Limpar marcação** e **limpar manifesto**

> [!NOTE]
> Os eventos de limpeza serão registrados somente se uma [política de retenção](container-registry-retention-policy.md) de registro estiver configurada.

## <a name="registry-resource-logs"></a>Logs de recursos do registro

Os logs de recursos contêm informações emitidas pelos recursos do Azure que descrevem sua operação interna. Para um registro de contêiner do Azure, os logs contêm eventos de autenticação e de nível de repositório armazenados nas tabelas a seguir. 

* **ContainerRegistryLoginEvents**  -status e eventos de autenticação do registro, incluindo a identidade de entrada e o endereço IP
* **ContainerRegistryRepositoryEvents** – operações como push e pull para imagens e outros artefatos em repositórios de registro
* **AzureMetrics**  -  [Métricas de registro de contêiner](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) , como contagens de push e pull agregadas.

Para operações, os dados de log incluem:
  * Status de êxito ou falha
  * Carimbos de data/hora de início e término

Além dos logs de recursos, o Azure fornece um [log de atividades](../azure-monitor/platform/platform-logs-overview.md), um único registro de nível de assinatura de eventos de gerenciamento do Azure, como a criação ou a exclusão de um registro de contêiner.

## <a name="enable-collection-of-resource-logs"></a>Habilitar a coleta de logs de recursos

A coleção de logs de recursos para um registro de contêiner não está habilitada por padrão. Habilite explicitamente as configurações de diagnóstico para cada registro que você deseja monitorar. Para obter opções para habilitar as configurações de diagnóstico, consulte [criar configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](../azure-monitor/platform/diagnostic-settings.md).

Por exemplo, para exibir logs e métricas para um registro de contêiner quase em tempo real em Azure Monitor, colete os logs de recursos em um espaço de trabalho Log Analytics. Para habilitar essa configuração de diagnóstico usando o portal do Azure:

1. Se você ainda não tiver um espaço de trabalho, crie um espaço de trabalho usando o [portal do Azure](../azure-monitor/learn/quick-create-workspace.md). Para minimizar a latência na coleta de dados, verifique se o espaço de trabalho está na **mesma região** que o registro de contêiner.
1. No portal, selecione o registro e selecione **monitoramento > configurações de diagnóstico > adicionar configuração de diagnóstico**.
1. Insira um nome para a configuração e selecione **Enviar para log Analytics**.
1. Selecione o espaço de trabalho para os logs de diagnóstico do registro.
1. Selecione os dados de log que você deseja coletar e clique em **salvar**.

A imagem a seguir mostra a criação de uma configuração de diagnóstico para um registro usando o Portal.

![Habilitar configurações de diagnóstico](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Colete apenas os dados de que você precisa, balanceando o custo e suas necessidades de monitoramento. Por exemplo, se você só precisa auditar eventos de autenticação, selecione somente o log **ContainerRegistryLoginEvents** . 

## <a name="view-data-in-azure-monitor"></a>Exibir dados no Azure Monitor

Depois de habilitar a coleta de logs de diagnóstico no Log Analytics, pode levar alguns minutos para que os dados apareçam em Azure Monitor. Para exibir os dados no portal, selecione o registro e selecione **monitoramento > logs**. Selecione uma das tabelas que contém dados para o registro. 

Execute consultas para ver os dados. Existem várias consultas de exemplo. Você também pode executar as suas. Por exemplo, a consulta a seguir recupera as 24 horas de dados mais recentes da tabela **ContainerRegistryRepositoryEvents** :

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

A imagem a seguir mostra a saída de exemplo:

![Consultar dados de log](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Para obter um tutorial sobre como usar Log Analytics no portal do Azure, consulte Introdução [ao Azure Monitor log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md)ou experimente o ambiente de [demonstração](https://portal.loganalytics.io/demo)do log Analytics. 

Para obter mais informações sobre consultas de log, consulte [visão geral das consultas de log no Azure monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="query-examples"></a>Exemplos de consulta

### <a name="error-events-from-the-last-hour"></a>Eventos de erro da última hora

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>100 eventos de registro mais recentes

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>Identidade do usuário ou objeto que excluiu o repositório

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>Identidade do usuário ou objeto que excluiu a marca

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>Falhas de operação em nível de repositório

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>Falhas de autenticação do registro

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="additional-log-destinations"></a>Destinos de log adicionais

Além de enviar os logs para Log Analytics, ou como alternativa, um cenário comum é selecionar uma conta de armazenamento do Azure como um destino de log. Para arquivar logs no armazenamento do Azure, crie uma conta de armazenamento antes de habilitar o arquivamento por meio das configurações de diagnóstico.

Você também pode transmitir eventos de log de diagnóstico para um [Hub de eventos do Azure](../event-hubs/event-hubs-about.md). Os Hubs de Eventos podem incluir milhões de eventos por segundo, os quais você pode transformar e armazenar usando qualquer provedor de análise em tempo real. 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como usar [log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md) e criar [consultas de log](../azure-monitor/log-query/get-started-queries.md).
* Consulte [visão geral dos logs da plataforma Azure](../azure-monitor/platform/platform-logs-overview.md) para saber mais sobre os logs de plataforma que estão disponíveis em diferentes camadas do Azure.