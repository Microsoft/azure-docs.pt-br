---
title: Referência – Log de recursos do Gerenciamento de API do Azure
description: Referência de esquema para o log de recursos GatewayLogs do Gerenciamento de API do Azure
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 064ac21e01239f7be3ddca9e48089ce880c6af58
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379729"
---
# <a name="reference-api-management-resource-log-schema"></a>Referência: esquema do log de recursos do Gerenciamento de API

Neste artigo, você verá a referência de esquema para o log de recursos GatewayLogs do Gerenciamento de API do Azure. As entradas de log também incluem campos no [esquema comum de nível superior](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema).

Para habilitar a coleta do log de recursos no Gerenciamento de API, confira [Monitorar APIs publicadas](api-management-howto-use-azure-monitor.md#resource-logs).

## <a name="gatewaylogs-schema"></a>Esquema GatewayLogs

Confira a seguir as propriedades registradas para cada solicitação de API.

| Propriedade  | Tipo | Descrição |
| ------------- | ------------- | ------------- |
| method | string | Método HTTP da solicitação de entrada |
| url | string | URL da solicitação de entrada |
| responseCode | inteiro | Código de status de resposta HTTP enviada a um cliente |
| responseSize | inteiro | Número de bytes enviados a um cliente durante o processamento da solicitação | 
| cache | string | Status de envolvimento do cache do Gerenciamento de API no processamento da solicitação (ocorrências, ignorado, nenhum) | 
| apiId | string | Identificador da entidade de API para a solicitação atual | 
| operationId | string | Identificador da entidade de operação para a solicitação atual | 
| clientProtocol | string | Versão do protocolo HTTP da solicitação de entrada |
| clientTime | inteiro | Número de milissegundos gastos na E/S geral do cliente (conectando, enviando e recebendo bytes) | 
| apiRevision | string | Revisão da API para a solicitação atual | 
| clientTlsVersion| string | Versão TLS usada pelo cliente que envia a solicitação |
| lastError | objeto | Para uma solicitação malsucedida, detalhes sobre o último erro de processamento de solicitação | 
| backendMethod | string | Método HTTP da solicitação enviada a um back-end |
| backendUrl | string | URL da solicitação enviada a um back-end |
| backendResponseCode | inteiro | Código de resposta HTTP recebida de um back-end |
| backedProtocol | string | Versão do protocolo HTTP da solicitação enviada a um back-end |
| backendTime | inteiro | Número de milissegundos gastos na E/S do back-end no total (conectando, enviando e recebendo bytes) | 


## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como monitorar APIs no Gerenciamento de API, confira [Monitorar APIs publicadas](api-management-howto-use-azure-monitor.md)
* Saiba mais sobre o [Esquema comum e específico de serviço para os Logs de Recursos do Azure](../azure-monitor/platform/resource-logs-schema.md)

