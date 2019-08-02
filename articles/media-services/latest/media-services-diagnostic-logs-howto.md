---
title: Monitorar logs de diagnóstico dos serviços de mídia por meio do Azure Monitor | Microsoft Docs
description: Este artigo mostra como rotear e exibir os logs de diagnóstico por meio do Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 233b043ffdc295fe94ed2e3ba837d4229848df22
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795851"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Monitorar logs de diagnóstico dos serviços de mídia

[O Azure Monitor](../../azure-monitor/overview.md) habilita você a monitorar métricas e logs de diagnóstico que ajudam você a entende como estão o desempenho de seus aplicativos. Para obter uma descrição detalhada desse recurso e para ver por que você iria querer usar logs de diagnóstico e métricas de serviços de mídia do Azure, consulte [logs de diagnóstico e métricas de serviços de mídia do Monitor](media-services-metrics-diagnostic-logs.md).

Este artigo mostra como rotear os dados para a conta de armazenamento e, em seguida, exibir os dados. 

## <a name="prerequisites"></a>Pré-requisitos

- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).
- Revisão [logs de diagnóstico e métricas de serviços de mídia do Monitor](media-services-metrics-diagnostic-logs.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Rotear os dados para a conta de armazenamento usando o portal

1. Faça logon no Portal do Azure em https://portal.azure.com.
1. Navegue até sua conta de serviços de mídia em e clique em **configurações de diagnóstico** sob **Monitor**. Veja aqui uma lista de todos os recursos em sua assinatura que produzem dados de monitoramento por meio do Azure Monitor. 

    ![Seção Configurações de diagnóstico](media/media-services-diagnostic-logs/logs01.png)

1. Clique em **Adicionar configuração de diagnóstico**.

   Uma configuração de diagnóstico de recurso é uma definição de *quais* dados de monitoramento devem ser encaminhados de um recurso específico e para *onde* os dados de monitoramento devem ir.

1. Na seção exibida, dê à configuração um **nome** e marque a caixa de **Arquivar em uma conta de armazenamento**.

    Selecione a conta de armazenamento ao qual você deseja enviar logs e pressione **Okey**.
1. Marque todas as caixas em **Log** e **Métrica**. Dependendo do tipo de recurso, você poderá ter somente uma dessas opções. Essas caixas de seleção controlam quais categorias de dados de log e métrica disponíveis para o tipo de recurso são enviadas para o destino selecionado, nesse caso, uma conta de armazenamento.

   ![Seção Configurações de diagnóstico](media/media-services-diagnostic-logs/logs02.png)
1. Defina o controle deslizante **Retenção (dias)** como 30. Esse controle deslizante define um número de dias para reter os dados de monitoramento na conta de armazenamento. O Azure Monitor exclui automaticamente os dados mais antigos do que o número de dias especificado. Uma retenção de zero dias armazena os dados por tempo indeterminado.
1. Clique em **Salvar**.

Os dados de monitoramento de seu recurso agora estão fluindo para a conta de armazenamento.

## <a name="route-data-to-the-storage-account-using-the-cli"></a>Rotear os dados para a conta de armazenamento usando a CLI

Para habilitar o armazenamento dos logs de diagnóstico em uma conta de armazenamento, você executaria o seguinte `az monitor diagnostic-settings` comando da CLI: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Por exemplo:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>Exibir dados na conta de armazenamento usando o portal

Se você seguiu as etapas anteriores, os dados começaram a fluir para sua conta de armazenamento.

Talvez seja necessário aguardar até cinco minutos antes que o evento seja exibido na conta de armazenamento.

1. No portal, navegue para a seção **Contas de Armazenamento** localizando-a na barra de navegação à esquerda.
1. Identifique a conta de armazenamento criada na seção anterior e clique nela.
1. Clique em **Blobs**, em seguida, no contêiner rotulado **insights-logs-keydeliveryrequests**. Isso é o contêiner que tem os logs em si. Os dados de monitoramento são divididos em contêineres por ID de recurso, em seguida, pela data e hora.
1. Navegue para o arquivo PT1H.json clicando nos contêineres, em ID de recurso, data e hora. Clique no arquivo PT1H.json e clique em **Baixar**.

 Agora você pode exibir o evento do JSON armazenado na conta de armazenamento.

### <a name="examples-of-pt1hjson"></a>Exemplos de PT1H.json

#### <a name="clear-key-delivery-log"></a>Log de entrega de chave não criptografada

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Log de distribuição de chaves criptografados Widevine

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="see-also"></a>Consulte também

* [Métricas do Azure Monitor](../../azure-monitor/platform/data-platform.md)
* [Logs de diagnóstico do Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Como coletar e consumir dados de log dos recursos do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)

## <a name="next-steps"></a>Próximas etapas

[Monitorar métricas](media-services-metrics-howto.md)
