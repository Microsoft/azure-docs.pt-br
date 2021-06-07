---
title: Monitorando a referência de dados dos serviços de mídia
description: Material de referência importante necessário ao monitorar os serviços de mídia
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 03/17/2021
ms.openlocfilehash: 66fce608515d16c5418ddd18e00319a3cbf088f7
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609081"
---
# <a name="monitoring-media-services-data-reference"></a>Monitorando a referência de dados dos serviços de mídia

Este artigo aborda os dados que são úteis para monitorar os serviços de mídia. Para obter mais informações sobre todas as métricas de plataforma com suporte no Azure Monitor, examine as [métricas com suporte com Azure monitor](../../../azure-monitor/essentials/metrics-supported.md).

## <a name="media-services-metrics"></a>Métricas dos serviços de mídia

As métrica são coletadas em intervalos regulares independentemente da mudança no valor. Elas são úteis para alertar, pois podem ser utilizadas com frequência, e um alerta pode ser acionado rapidamente com uma lógica relativamente simples.

Os serviços de mídia oferecem suporte a métricas de monitoramento para os seguintes recursos:

* Conta
* Ponto de Extremidade de Streaming

### <a name="account"></a>Conta

Você pode monitorar as seguintes métricas de conta.

|Nome da métrica|Nome de exibição|Descrição|
|---|---|---|
|AssetCount|Contagem de ativos|Ativos em sua conta.|
|AssetQuota|Cota de ativos|Cota de ativos em sua conta.|
|AssetQuotaUsedPercentage|Percentual de cota de ativos usado|A porcentagem da cota de ativos já usada.|
|ContentKeyPolicyCount|Contagem de Política de Chave de Conteúdo|Políticas de chave de conteúdo em sua conta.|
|ContentKeyPolicyQuota|Cota de Política de Chave de Conteúdo|Cota de políticas de chave de conteúdo em sua conta.|
|ContentKeyPolicyQuotaUsedPercentage|Percentual de Cota Usada da Política de Chave de Conteúdo|A porcentagem da cota de política de chave de conteúdo já usada.|
|StreamingPolicyCount|Contagem de Política de Streaming|Políticas de streaming em sua conta.|
|StreamingPolicyQuota|Cota de Política de Streaming|Cota de políticas de streaming em sua conta.|
|StreamingPolicyQuotaUsedPercentage|Percentual de cota usada da Política de Streaming|A porcentagem da cota da política de streaming já usada.|

Você também deve examinar as [cotas de conta e os limites](../limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Ponto de Extremidade de Streaming

As métricas de [pontos de extremidade de streaming](/rest/api/media/streamingendpoints) dos serviços de mídia a seguir têm suporte:

|Nome da métrica|Nome de exibição|Descrição|
|---|---|---|
|Requests|Requests|Fornece o número total de solicitações HTTP atendidas pelo ponto de extremidade de streaming.|
|Saída|Saída|Total de bytes de saída por minuto por ponto de extremidade de streaming.|
|SuccessE2ELatency|Latência de ponta a ponta bem-sucedida|Duração de tempo de quando o ponto de extremidade de streaming recebeu a solicitação para quando o último byte da resposta foi enviado.|
|Uso da CPU| | Uso da CPU para pontos de extremidade de streaming Premium. Esses dados não estão disponíveis para pontos de extremidade de streaming padrão. |
|Largura de banda de saída | | Largura de banda de saída em bits por segundo.|

## <a name="metric-dimensions"></a>Dimensões de métrica

Para obter mais informações sobre o que são dimensões de métrica, confira [Métricas multidimensionais](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

<!--**PLACEHOLDER** for dimensions table.-->

## <a name="resource-logs"></a>Logs de recursos

## <a name="media-services-diagnostic-logs"></a>Logs de diagnóstico dos serviços de mídia

Os logs de diagnóstico fornecem dados avançados e frequentes sobre a operação de um recurso do Azure. Para obter mais informações, consulte [como coletar e consumir dados de log de seus recursos do Azure](../../../azure-monitor/essentials/platform-logs-overview.md).

Os serviços de mídia oferecem suporte aos seguintes logs de diagnóstico:

* Entrega de chave

### <a name="key-delivery"></a>Entrega de chave

|Nome|Descrição|
|---|---|
|Solicitação do serviço de distribuição de chaves|Logs que mostram as informações de solicitação de serviço de entrega de chave. Para obter mais informações, consulte [esquemas](monitor-media-services-data-reference.md).|

## <a name="schemas"></a>Esquemas

Para obter uma descrição detalhada do esquema de logs de diagnóstico de nível superior, consulte [serviços, esquemas e categorias com suporte para logs de diagnóstico do Azure](../../../azure-monitor/essentials/resource-logs-schema.md).

## <a name="key-delivery-log-schema-properties"></a>Propriedades do esquema do log de distribuição de chaves

Essas propriedades são específicas para o esquema de log de distribuição de chaves.

|Nome|Descrição|
|---|---|
|keyId|A ID da chave solicitada.|
|keyType|Pode ser um dos seguintes valores: "Clear" (sem criptografia), "FairPlay", "PlayReady" ou "Widevine".|
|policyName|O nome Azure Resource Manager da política.|
|tokenType|O tipo do token.|
|statusMessage|A mensagem de status.|

### <a name="example"></a>Exemplo

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

>[!NOTE]
> O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
