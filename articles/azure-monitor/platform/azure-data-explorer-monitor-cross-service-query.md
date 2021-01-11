---
title: Consulta entre serviços entre Azure Monitor e Data Explorer do Azure (versão prévia)
description: Consulte dados do Azure Data Explorer por meio das ferramentas de Log Analytics do Azure vice-versa para ingressar e analisar todos os dados em um único lugar.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 5aadd9d96f538f92e1b9e0100b2c1055ee0b0633
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065262"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer-preview"></a>Azure Monitor de consulta entre serviços e Data Explorer do Azure (versão prévia)
Crie consultas entre serviços entre o [Azure data Explorer](https://docs.microsoft.com/azure/data-explorer/), [Application insights](/azure/azure-monitor/app/app-insights-overview)e [log Analytics](/azure/azure-monitor/platform/data-platform-logs).
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Azure Monitor e Data Explorer consulta entre serviços do Azure
Essa experiência permite que você [crie consultas entre serviços entre o azure data Explorer e o Azure monitor](https://docs.microsoft.com/azure/data-explorer/query-monitor-data) e [crie consultas entre serviços entre Azure monitor e data Explorer do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy).

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="Fluxo de proxy do Azure data Explorer.":::

Por exemplo, (consultando o Azure Data Explorer do Log Analytics):
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
Onde a consulta externa está consultando uma tabela no espaço de trabalho e, em seguida, unindo-se a outra tabela em um cluster Data Explorer do Azure (nesse caso, ClusterName = Help, DatabaseName = samples) usando uma nova função "ADX ()", como como você pode fazer o mesmo para consultar outro espaço de trabalho de dentro do texto da consulta.

> [!NOTE]
> * A capacidade de consultar Azure Monitor dados do Data Explorer do Azure, diretamente das ferramentas de cliente do Azure Data Explorer, ou indiretamente, executando uma consulta em um cluster de Data Explorer do Azure, está no modo de visualização.
> * Entre em contato com a equipe de [consulta entre serviços](mailto:adxproxy@microsoft.com) com perguntas.

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>Consulta exportada Log Analytics dados da conta de armazenamento de BLOBs do Azure

A exportação de dados de Azure Monitor para uma conta de armazenamento do Azure permite a retenção de baixo custo e a capacidade de realocar os logs para regiões diferentes.

Use o Data Explorer do Azure para consultar dados que foram exportados de seus espaços de trabalho do Log Analytics. Uma vez configuradas, as tabelas com suporte enviadas de seus espaços de trabalho para uma conta de armazenamento do Azure estarão disponíveis como uma fonte de dados para o Azure Data Explorer. [Consultar dados exportados de Azure monitor usando o data Explorer do Azure (versão prévia)](https://docs.microsoft.com/azure/azure-monitor/platform/azure-data-explorer-query-storage).

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-query.png" alt-text="O Azure Data Explorer consulta do fluxo de armazenamento.":::

>[!tip] 
> * Para exportar todos os dados do seu espaço de trabalho do Log Analytics para uma conta de armazenamento do Azure ou Hub de eventos, use o recurso de exportação de dados do espaço de trabalho Log Analytics dos logs do Azure Monitor. [Confira log Analytics exportação de dados de espaço de trabalho em Azure monitor (versão prévia)](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre:
* [crie consultas entre serviços entre o Azure data Explorer e o Azure monitor](https://docs.microsoft.com/azure/data-explorer/query-monitor-data). Consultar dados de Azure Monitor do Azure Data Explorer
* [crie consultas entre serviços entre Azure monitor e data Explorer do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy). Consultar dados do Azure Data Explorer do Azure Monitor
* [Log Analytics exportação de dados de espaço de trabalho no Azure monitor (versão prévia)](https://docs.microsoft.com/azure/data-explorer/query-monitor-data). Vincular e consultar a conta de armazenamento de BLOBs do Azure com Log Analytics dados exportados.
