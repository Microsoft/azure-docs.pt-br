---
title: Gerenciar pontos de extremidade de streaming
description: Este artigo demonstra como gerenciar pontos de extremidade de streaming com os serviços de mídia do Azure v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 8cdfc9e1243de831364bc6a8a11fe25453cc4931
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213854"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Gerenciar pontos de extremidade de streaming com os serviços de mídia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Quando sua conta de serviços de mídia for criada, um [ponto de extremidade de streaming](streaming-endpoint-concept.md) **padrão** será adicionado à sua conta no estado **parado** . Para começar a transmitir seu conteúdo e aproveitar o [empacotamento dinâmico](dynamic-packaging-overview.md) e a [criptografia dinâmica](content-protection-overview.md), o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar no estado **executando** .

Este artigo mostra como executar o comando [Iniciar](/rest/api/media/streamingendpoints/start) em seu ponto de extremidade de streaming usando tecnologias diferentes. 
 
> [!NOTE]
> Você será cobrado apenas quando seu ponto de extremidade de streaming estiver em estado de execução.
    
## <a name="prerequisites"></a>Pré-requisitos

Examine: 

* [Conceitos dos Serviços de Mídia](concepts-overview.md)
* [Conceito de ponto de extremidade de streaming](streaming-endpoint-concept.md)
* [Empacotamento dinâmico](dynamic-packaging-overview.md)

## <a name="use-rest"></a>Usar o REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Para obter mais informações, consulte: 

* A documentação de referência do [Start a StreamingEndpoint](/rest/api/media/streamingendpoints/start) .
* Iniciar um ponto de extremidade de streaming é uma operação assíncrona. 

    Para obter informações sobre como monitorar operações de execução longa, consulte [operações de execução longa](media-services-apis-overview.md).
* Esta [coleção do postmaster](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) contém exemplos de várias operações REST, incluindo como iniciar um ponto de extremidade de streaming.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure 
 
1. Entre no [portal do Azure](https://portal.azure.com/).
1. Vá para sua conta dos serviços de mídia do Azure.
1. No painel esquerdo, selecione  **pontos de extremidade de streaming**.
1. Selecione o ponto de extremidade de streaming que você deseja iniciar e, em seguida, selecione **Iniciar**.

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Para obter mais informações, consulte [AZ AMS streaming-Endpoint Start](/cli/azure/ams/streaming-endpoint#az-ams-streaming-endpoint-start).

## <a name="use-sdks"></a>Usar SDKs

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Consulte o [exemplo de código Java](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128)completo.

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Consulte o exemplo completo de [código .net](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Próximas etapas

* [Especificação de OpenAPI do serviço de mídia v3 (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Operações de ponto de extremidade de streaming](/rest/api/media/streamingendpoints)
