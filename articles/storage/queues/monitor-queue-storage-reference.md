---
title: Referência de dados de monitoramento do armazenamento de filas do Azure
description: Referência de log e métricas para monitorar dados do armazenamento de filas do Azure.
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 10/02/2020
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: ba8a82ed1113bfb3e71560ca9a6c713602df21f2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590640"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Referência de dados de monitoramento do armazenamento de filas do Azure

Consulte [Monitoramento do armazenamento do Azure](monitor-queue-storage.md) para obter detalhes sobre como coletar e analisar dados de monitoramento para o armazenamento do Azure.

## <a name="metrics"></a>Métricas

As tabelas a seguir listam as métricas da plataforma coletadas para o armazenamento do Azure.

### <a name="capacity-metrics"></a>Métricas de capacidade

Os valores de métricas de capacidade são atualizados diariamente (até 24 horas). O intervalo de agregação define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de agregação compatível com todas as métricas de capacidade é uma hora (PT1H).

O Armazenamento do Azure fornece as seguintes métricas de capacidade no Azure Monitor.

#### <a name="account-level-capacity-metrics"></a>Métricas de capacidade de nível de conta

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>Métricas de armazenamento de filas

Esta tabela mostra as [métricas de armazenamento de filas](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices).

| Métrica | Descrição |
| ------------------- | ----------------- |
| **QueueCapacity** | A quantidade de armazenamento de fila usada pela conta de armazenamento. <br><br> Unidade `Bytes` <br> Tipo de agregação: `Average` <br> Exemplo de valor: `1024` |
| **QueueCount** | O número de filas em uma conta de armazenamento. <br><br> Unidade `Count` <br> Tipo de agregação: `Average` <br> Exemplo de valor: `1024` |
| **QueueMessageCount** | O número aproximado de mensagens de fila na conta de armazenamento. <br><br> Unidade `Count` <br> Tipo de agregação: `Average` <br> Exemplo de valor: `1024` |

### <a name="transaction-metrics"></a>Métricas de transação

As métricas de transação são emitidas, do Armazenamento do Azure para o Azure Monitor, em cada solicitação a uma conta de armazenamento. Caso não haja nenhuma atividade em sua conta de armazenamento, não haverá nenhum dado nas métricas de transações no período. Todas as métricas de transação estão disponíveis no nível de serviço de armazenamento de conta e fila. O intervalo de agregação define o intervalo de tempo para o qual os valores das métricas são apresentados. Os intervalos de agregação para todas as métricas de transação são PT1H e PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensões das métricas

O Armazenamento do Azure oferece suporte às seguintes dimensões para métricas no Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Logs de recurso (versão prévia)

> [!NOTE]
> Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Essa versão preliminar habilita logs para blobs (incluindo o Azure Data Lake Storage Gen2), arquivos, filas, tabelas, contas de armazenamento Premium nas contas de armazenamento GPv1 e GPv2. Não há suporte para contas de armazenamento clássicas.

A tabela a seguir lista as propriedades dos logs de recursos do armazenamento do Azure quando eles são coletados nos logs de Azure Monitor ou no armazenamento do Azure. As propriedades descrevem a operação, o serviço e o tipo de autorização que foi usado para executar a operação.

### <a name="fields-that-describe-the-operation"></a>Campos que descrevem a operação

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Campos que descrevem como a operação foi autenticada

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Campos que descrevem o serviço

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Veja também

- Consulte [monitoramento do armazenamento de filas do Azure](monitor-queue-storage.md) para obter uma descrição de monitoramento do armazenamento de filas do Azure.
- Confira [Como monitorar os recursos do Azure com o Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) para obter detalhes sobre o monitoramento de recursos do Azure.
