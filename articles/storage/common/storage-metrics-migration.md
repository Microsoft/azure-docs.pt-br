---
title: Mover de métricas de Análise de Armazenamento para Azure Monitor métricas | Microsoft Docs
description: Saiba como fazer a transição de métricas de Análise de Armazenamento (métricas clássicas) para métricas no Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: ff4c3c5ae8629879fca6e9e683a9c77d0e2f144b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596104"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>Transição para métricas no Azure Monitor

Em **31 de agosto de 2023** análise de armazenamento métricas, também conhecidas como *métricas clássicas* serão desativadas. Para saber mais, confira o [anúncio oficial](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Se você usar métricas clássicas, não deixe de fazer a transição para as métricas em Azure Monitor antes dessa data. Este artigo ajuda você com a transição. 

## <a name="steps-to-complete-the-transition"></a>Etapas para concluir a transição

Para fazer a transição para as métricas no Azure Monitor, recomendamos a seguinte abordagem.

1. Saiba mais sobre algumas das [principais diferenças](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor) entre métricas clássicas e métricas em Azure monitor. 

2. Compile uma lista de métricas clássicas que você usa atualmente.

3. Identifique [quais métricas no Azure monitor](#metrics-mapping-between-old-metrics-and-new-metrics) fornecem os mesmos dados que as métricas que você usa no momento. 
   
4. Crie [gráficos](/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal) ou [painéis](/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal) para exibir dados de métrica.

   > [!NOTE]
   > As métricas em Azure Monitor são habilitadas por padrão, portanto, não há nada que você precise fazer para começar a capturar as métricas. No entanto, você deve criar gráficos ou painéis para exibir essas métricas. 
 
5. Se você tiver criado regras de alerta baseadas em métricas de armazenamento clássicas, [crie regras de alerta](../../azure-monitor/alerts/alerts-overview.md) baseadas em métricas no Azure monitor. 

6. Depois de ver todas as métricas em Azure Monitor, você pode desativar o registro em log clássico. 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>Métricas clássicas versus métricas no Azure Monitor

Esta seção descreve algumas das principais diferenças entre essas duas plataformas de métricas.

A principal diferença é a forma como as métricas são gerenciadas. As métricas clássicas são gerenciadas pelo armazenamento do Azure, enquanto as métricas no Azure Monitor são gerenciadas pelo Azure Monitor. Com as métricas clássicas, o armazenamento do Azure coleta valores de métrica, agrega-os e os armazena em tabelas que estão localizadas na conta de armazenamento. Com as métricas no Azure Monitor, o armazenamento do Azure envia dados de métrica para o back-end de Azure Monitor. O Azure Monitor fornece uma experiência de monitoramento unificada que inclui dados do portal do Azure, bem como dados que são ingeridos. 

As métricas clássicas são enviadas e armazenadas em uma conta de armazenamento do Azure. Azure Monitor métricas podem ser enviadas para vários locais. Uma conta de armazenamento pode ser um desses locais, mas não é necessária.  

No que diz respeito ao suporte a métricas, as métricas clássicas fornecem métricas de **capacidade** apenas para o armazenamento de BLOBs do Azure. As métricas no Azure Monitor fornecem métricas de capacidade para BLOB, tabela, arquivo, fila e armazenamento Premium. As métricas clássicas fornecem métricas de **transação** em BLOB, tabela, arquivo do Azure e armazenamento de fila. As métricas no Azure Monitor adicionar armazenamento Premium a essa lista.

Se a atividade em sua conta não disparar uma métrica, as métricas clássicas mostrarão um valor igual a zero (0) para essa métrica. As métricas no Azure Monitor irão omitir os dados inteiramente, o que leva a relatórios mais limpos. Por exemplo, com métricas clássicas, se nenhum erro de tempo limite do servidor for relatado, o `ServerTimeoutError` valor na tabela de métricas será definido como 0. Azure Monitor não retorna nenhum dado quando você consulta o valor da métrica `Transactions` com a dimensão `ResponseType` igual a `ServerTimeoutError` . 

Para saber mais sobre as métricas em Azure Monitor, consulte [métricas em Azure monitor](../../azure-monitor/essentials/data-platform-metrics.md).

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>Mapear métricas clássicas para métricas no Azure Monitor

 Use essas tabelas para identificar quais métricas no Azure Monitor fornecem os mesmos dados que as métricas que você usa no momento. 

**Métricas de capacidade**

| Métrica clássica | Métrica no Azure Monitor |
| ------------------- | ----------------- |
| `Capacity`            | `BlobCapacity` com a dimensão `BlobType` igual a `BlockBlob` ou `PageBlob` |
| `ObjectCount`        | `BlobCount` com a dimensão `BlobType` igual a `BlockBlob` ou `PageBlob` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> Há também várias novas métricas de capacidade que não estavam disponíveis como métricas clássicas. Para exibir a lista completa, consulte [métricas](../blobs/monitor-blob-storage-reference.md#metrics).

**Métricas de transação**

| Métrica clássica | Métrica no Azure Monitor |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | Transações com dimensão `ResponseType` igual a `AuthorizationError` e dimensão `Authentication` igual a `Anonymous` |
| `AnonymousClientOtherError` | Transações com dimensão `ResponseType` igual a `ClientOtherError` e dimensão `Authentication` igual a `Anonymous` |
| `AnonymousClientTimeoutError` | Transações com dimensão `ResponseType` igual a `ClientTimeoutError` e dimensão `Authentication` igual a `Anonymous` |
| `AnonymousNetworkError` | Transações com dimensão `ResponseType` igual a `NetworkError` e dimensão `Authentication` igual a `Anonymous` |
| `AnonymousServerOtherError` | Transações com dimensão `ResponseType` igual a `ServerOtherError` e dimensão `Authentication` igual a `Anonymous` |
| `AnonymousServerTimeoutError` | Transações com dimensão `ResponseType` igual a `ServerTimeoutError` e dimensão `Authentication` igual a `Anonymous` |
| `AnonymousSuccess` | Transações com dimensão `ResponseType` igual a `Success` e dimensão `Authentication` igual a `Anonymous` |
| `AnonymousThrottlingError` | Transações com dimensão `ResponseType` igual a `ClientThrottlingError` ou `ServerBusyError` e dimensão `Authentication` igual a `Anonymous` |
| `AuthorizationError` | Transações com a dimensão `ResponseType` igual a `AuthorizationError` |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | Transações com a dimensão `ResponseType` igual a `ClientOtherError` |
| `ClientTimeoutError` | Transações com a dimensão `ResponseType` igual a `ClientTimeoutError` |
| `NetworkError` | Transações com a dimensão `ResponseType` igual a `NetworkError` |
| `PercentAuthorizationError` | Transações com a dimensão `ResponseType` igual a `AuthorizationError` |
| `PercentClientOtherError` | Transações com a dimensão `ResponseType` igual a `ClientOtherError` |
| `PercentNetworkError` | Transações com a dimensão `ResponseType` igual a `NetworkError` |
| `PercentServerOtherError` | Transações com a dimensão `ResponseType` igual a `ServerOtherError` |
| `PercentSuccess` | Transações com a dimensão `ResponseType` igual a `Success` |
| `PercentThrottlingError` | Transações com a dimensão `ResponseType` igual a `ClientThrottlingError` ou `ServerBusyError` |
| `PercentTimeoutError` | Transações com a dimensão `ResponseType` igual `ServerTimeoutError` ou `ResponseType` igual a `ClientTimeoutError` |
| `SASAuthorizationError` | Transações com dimensão `ResponseType` igual a `AuthorizationError` e dimensão `Authentication` igual a `SAS` |
| `SASClientOtherError` | Transações com dimensão `ResponseType` igual a `ClientOtherError` e dimensão `Authentication` igual a `SAS` |
| `SASClientTimeoutError` | Transações com dimensão `ResponseType` igual a `ClientTimeoutError` e dimensão `Authentication` igual a `SAS` |
| `SASNetworkError` | Transações com dimensão `ResponseType` igual a `NetworkError` e dimensão `Authentication` igual a `SAS` |
| `SASServerOtherError` | Transações com dimensão `ResponseType` igual a `ServerOtherError` e dimensão `Authentication` igual a `SAS` |
| `SASServerTimeoutError` | Transações com dimensão `ResponseType` igual a `ServerTimeoutError` e dimensão `Authentication` igual a `SAS` |
| `SASSuccess` | Transações com dimensão `ResponseType` igual a `Success` e dimensão `Authentication` igual a `SAS` |
| `SASThrottlingError` | Transações com dimensão `ResponseType` igual a `ClientThrottlingError` ou `ServerBusyError` e dimensão `Authentication` igual a `SAS` |
| `ServerOtherError` | Transações com a dimensão `ResponseType` igual a `ServerOtherError` |
| `ServerTimeoutError` | Transações com a dimensão `ResponseType` igual a `ServerTimeoutError` |
| `Success` | Transações com a dimensão `ResponseType` igual a `Success` |
| `ThrottlingError` | `Transactions` com a dimensão `ResponseType` igual a `ClientThrottlingError` ou `ServerBusyError`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>Próximas etapas

* [Azure Monitor](../../azure-monitor/overview.md)