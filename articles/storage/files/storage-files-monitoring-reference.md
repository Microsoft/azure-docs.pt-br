---
title: Referência de dados de monitoramento de arquivos do Azure | Microsoft Docs
description: Referência de log e métricas para monitorar dados de arquivos do Azure.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 4575709a4fa7067b02228036fb2e2b4a60844e21
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592318"
---
# <a name="azure-files-monitoring-data-reference"></a>Referência de dados de monitoramento de arquivos do Azure

Consulte [monitorando arquivos do Azure](storage-files-monitoring.md) para obter detalhes sobre como coletar e analisar dados de monitoramento para arquivos do Azure.

## <a name="metrics"></a>Métricas

As tabelas a seguir listam as métricas de plataforma coletadas para os arquivos do Azure. 

### <a name="capacity-metrics"></a>Métricas de capacidade

Os valores de métricas de capacidade são atualizados diariamente (até 24 horas). O intervalo de agregação define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de agregação compatível com todas as métricas de capacidade é uma hora (PT1H).

Os arquivos do Azure fornecem as seguintes métricas de capacidade no Azure Monitor.

#### <a name="account-level"></a>Nível de conta

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="azure-files"></a>Arquivos do Azure

Esta tabela mostra as [métricas de arquivos do Azure](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices).

| Métrica | Descrição |
| ------------------- | ----------------- |
| FileCapacity | A quantidade de armazenamento de Arquivos usada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileCount   | O número de arquivos na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileShareCapacityQuota | O limite superior da quantidade de armazenamento que pode ser usada pelo serviço de arquivos do Azure em bytes. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024|
| FileShareCount | O número de compartilhamentos de arquivos na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileShareProvisionedIOPS | O número de IOPS provisionados em um compartilhamento de arquivos. Essa métrica é aplicável somente ao armazenamento de arquivos premium. <br/><br/> Unidade: bytes <br/> Tipo de agregação: Média |
| FileShareSnapshotCount | O número de instantâneos presentes no compartilhamento no serviço de arquivos do Azure da conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Média | 
|FileShareSnapshotSize|A quantidade de armazenamento usada pelos instantâneos no serviço de arquivos do Azure da conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média|

### <a name="transaction-metrics"></a>Métricas de transação

As métricas de transação são emitidas, do Armazenamento do Azure para o Azure Monitor, em cada solicitação a uma conta de armazenamento. Caso não haja nenhuma atividade em sua conta de armazenamento, não haverá nenhum dado nas métricas de transações no período. Todas as métricas de transação estão disponíveis no nível de serviço conta e arquivos do Azure. O intervalo de agregação define o intervalo de tempo para o qual os valores das métricas são apresentados. Os intervalos de agregação para todas as métricas de transação são PT1H e PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensões das métricas

Os arquivos do Azure dão suporte às seguintes dimensões para métricas no Azure Monitor.

> [!NOTE] 
> A dimensão de compartilhamento de arquivos não está disponível para compartilhamentos de arquivos padrão (somente compartilhamentos de arquivos Premium). Ao usar compartilhamentos de arquivos padrão, as métricas fornecidas são para todos os compartilhamentos de arquivos na conta de armazenamento. Para obter métricas por compartilhamento para compartilhamentos de arquivos padrão, crie um compartilhamento de arquivos por conta de armazenamento.

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

## <a name="see-also"></a>Confira também

- Consulte [monitorando arquivos do Azure](storage-files-monitoring-reference.md) para obter uma descrição do monitoramento do armazenamento do Azure.
- Confira [Como monitorar os recursos do Azure com o Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md) para obter detalhes sobre o monitoramento de recursos do Azure.