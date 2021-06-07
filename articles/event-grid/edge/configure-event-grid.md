---
title: Configuração-IoT Edge de grade de eventos do Azure | Microsoft Docs
description: Configuração na grade de eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 632227579fd021a0d2ce1d0b1bb0b8a8288c5f47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171662"
---
# <a name="event-grid-configuration"></a>Configuração da grade de eventos

A grade de eventos fornece muitas configurações que podem ser modificadas por ambiente. A seção a seguir é uma referência a todas as opções disponíveis e seus padrões.

## <a name="tls-configuration"></a>Configuração TLS

Para saber mais sobre a autenticação de cliente em geral, consulte [segurança e autenticação](security-authentication.md). Exemplos de uso podem ser encontrados neste [artigo](configure-api-protocol.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Política TLS do módulo de grade de eventos. O valor padrão é somente HTTPS.
|`inbound__serverAuth__serverCert__source`| Origem do certificado de servidor usado pelo módulo de grade de eventos para sua configuração de TLS. O valor padrão é IoT Edge.

## <a name="incoming-client-authentication"></a>Autenticação de cliente de entrada

Para saber mais sobre a autenticação de cliente em geral, consulte [segurança e autenticação](security-authentication.md). Exemplos podem ser encontrados neste [artigo](configure-client-auth.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Para ativar/desativar a autenticação de cliente baseada em certificado. O valor padrão é true.
|`inbound__clientAuth__clientCert__source`| Fonte para validar certificados de cliente. O valor padrão é IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Política para permitir um certificado de cliente autoassinado. O valor padrão é true.
|`inbound__clientAuth__sasKeys__enabled`| Para ativar/desativar a autenticação de cliente baseada na chave SAS. O valor padrão é off.
|`inbound__clientAuth__sasKeys__key1`| Um dos valores para validar as solicitações de entrada.
|`inbound__clientAuth__sasKeys__key2`| Segundo valor opcional para validar solicitações de entrada.

## <a name="outgoing-client-authentication"></a>Autenticação de cliente de saída
Para saber mais sobre a autenticação de cliente em geral, consulte [segurança e autenticação](security-authentication.md). Exemplos podem ser encontrados neste [artigo](configure-identity-auth.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Para ativar/desativar a anexação de um certificado de identidade para solicitações de saída. O valor padrão é true.
|`outbound__clientAuth__clientCert__source`| Fonte para recuperar o certificado de saída do módulo de grade de eventos. O valor padrão é IoT Edge.

## <a name="webhook-event-handlers"></a>Manipuladores de eventos de webhook

Para saber mais sobre a autenticação de cliente em geral, consulte [segurança e autenticação](security-authentication.md). Exemplos podem ser encontrados neste [artigo](configure-webhook-subscriber-auth.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Política para controlar se somente assinantes HTTPS serão permitidos. O valor padrão é true (somente HTTPS).
|`outbound__webhook__skipServerCertValidation`| Sinalizador para controlar se o certificado do Assinante deve ser validado. O valor padrão é true.
|`outbound__webhook__allowUnknownCA`| Política para controlar se um certificado autoassinado pode ser apresentado por um assinante. O valor padrão é true. 

## <a name="delivery-and-retry"></a>Entregar e tentar novamente

Para saber mais sobre esse recurso em geral, confira [entrega e repetição](delivery-retry.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Número máximo de tentativas para entregar um evento. O valor padrão é 30.
| `broker__defaultEventTimeToLiveInSeconds` | Tempo de vida (TTL) em segundos após o qual um evento será Descartado se não for entregue. O valor padrão é  **7200** segundos

## <a name="output-batching"></a>Envio em lote de saída

Para saber mais sobre esse recurso em geral, confira [envio e envio em lote de saída](delivery-output-batching.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Valor máximo permitido para o `ApproxBatchSizeInBytes` botão. O valor padrão é `1_058_576`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Valor máximo permitido para o `MaxEventsPerBatch` botão. O valor padrão é `50`.
| `broker__defaultMaxBatchSizeInBytes` | Tamanho máximo da solicitação de entrega quando apenas `MaxEventsPerBatch` é especificado. O valor padrão é `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Número máximo de eventos a serem adicionados a um lote somente quando `MaxBatchSizeInBytes` for especificado. O valor padrão é `10`.

## <a name="metrics"></a>Métricas

Para saber mais sobre como usar as métricas com a grade de eventos em IoT Edge, consulte [monitorar tópicos e assinaturas](monitor-topics-subscriptions.md)

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
| `metrics__reporterType` | Tipo de reporter para o ponto de extremidade de métricas. O padrão é `none` e desabilita as métricas. Configuração para `prometheus` habilita as métricas no formato Prometheus exposição.