---
title: Esquemas de logs de diagnóstico dos serviços de mídia do Azure – Azure
description: Este artigo demonstra os esquemas de logs de diagnóstico dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 8015f610dfd9404810a1fa19c951078ebb45ac4f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588486"
---
# <a name="diagnostic-logs-schemas"></a>Esquemas de logs de diagnóstico

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure monitor](../../azure-monitor/overview.md) permite que você monitore as métricas e os logs de diagnóstico que ajudam a entender como seus aplicativos estão sendo executados. Você pode monitorar os logs de diagnóstico dos serviços de mídia e criar alertas e notificações para as métricas e os logs coletados. Você pode enviar logs para o [armazenamento do Azure](https://azure.microsoft.com/services/storage/), transmiti-los para os [hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/)e exportá-los para [log Analytics](https://azure.microsoft.com/services/log-analytics/)ou usar serviços de terceiros.

Para obter informações detalhadas, consulte [Azure monitor métricas](../../azure-monitor/data-platform.md) e [Azure monitor logs de diagnóstico](../../azure-monitor/essentials/platform-logs-overview.md).

Este artigo descreve os esquemas dos logs de diagnóstico dos serviços de mídia.

## <a name="top-level-diagnostic-logs-schema"></a>Esquema de logs de diagnóstico de nível superior

Para obter uma descrição detalhada do esquema de logs de diagnóstico de nível superior, consulte [serviços, esquemas e categorias com suporte para logs de diagnóstico do Azure](../../azure-monitor/essentials/resource-logs-schema.md).

## <a name="key-delivery-log-schema"></a>Esquema do log de distribuição de chaves

### <a name="properties"></a>Propriedades

Essas propriedades são específicas para o esquema de log de distribuição de chaves.

|Name|Descrição|
|---|---|
|keyId|A ID da chave solicitada.|
|keyType|Pode ser um dos seguintes valores: "Clear" (sem criptografia), "FairPlay", "PlayReady" ou "Widevine".|
|policyName|O nome Azure Resource Manager da política.|
|tokenType|O tipo do token.|
|statusMessage|A mensagem de status.|

### <a name="examples"></a>Exemplos

Propriedades do esquema de solicitações de entrega de chave.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="additional-notes"></a>Observações adicionais

* O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps"></a>Próximas etapas

[Monitorar as métricas dos serviços de mídia e os logs de diagnóstico](media-services-metrics-diagnostic-logs.md)
