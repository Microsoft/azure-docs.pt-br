---
title: Monitorar logs de diagnóstico por meio de Azure Monitor
description: Este artigo demonstra como rotear e exibir logs de diagnóstico por meio de Azure Monitor.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3093d20fbe0a1ded68e34e816b7fc865a237ad92
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576313"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Monitorar logs de diagnóstico dos serviços de mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure monitor](../../azure-monitor/overview.md) permite que você monitore as métricas e os logs de diagnóstico que ajudam a entender como seus aplicativos estão sendo executados. Para obter uma descrição detalhada desse recurso e ver por que você desejaria usar os logs de diagnóstico e as métricas dos serviços de mídia do Azure, consulte [monitorar as métricas dos serviços de mídia e os logs de diagnóstico](media-services-metrics-diagnostic-logs.md).

Este artigo mostra como rotear dados para a conta de armazenamento e exibir os dados.

## <a name="prerequisites"></a>Pré-requisitos

- [Crie uma conta de Serviços de Mídia](./create-account-howto.md).
- Revise  [monitorar métricas de serviços de mídia e logs de diagnóstico](media-services-metrics-diagnostic-logs.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Rotear dados para a conta de armazenamento usando o portal

1. Faça logon no Portal do Azure em https://portal.azure.com.
1. Navegue até sua conta dos serviços de mídia no e clique em **configurações de diagnóstico** em **Monitor**. Veja aqui uma lista de todos os recursos em sua assinatura que produzem dados de monitoramento por meio do Azure Monitor.

    ![Captura de tela que realça as configurações de diagnóstico na seção monitoramento.](media/media-services-diagnostic-logs/logs01.png)

1. Clique em **Adicionar configuração de diagnóstico**.

   Uma configuração de diagnóstico de recurso é uma definição de *quais* dados de monitoramento devem ser encaminhados de um recurso específico e para *onde* os dados de monitoramento devem ir.

1. Na seção exibida, dê à configuração um **nome** e marque a caixa de **Arquivar em uma conta de armazenamento**.

    Selecione a conta de armazenamento para a qual você deseja enviar os logs e pressione **OK**.
1. Marque todas as caixas em **Log** e **Métrica**. Dependendo do tipo de recurso, você poderá ter somente uma dessas opções. Essas caixas de seleção controlam quais categorias de dados de log e métrica disponíveis para o tipo de recurso são enviadas para o destino selecionado, nesse caso, uma conta de armazenamento.

   ![Seção Configurações de diagnóstico](media/media-services-diagnostic-logs/logs02.png)
1. Defina o controle deslizante **Retenção (dias)** como 30. Esse controle deslizante define um número de dias para reter os dados de monitoramento na conta de armazenamento. O Azure Monitor exclui automaticamente os dados mais antigos do que o número de dias especificado. Uma retenção de zero dias armazena os dados por tempo indeterminado.
1. Clique em **Save** (Salvar).

Os dados de monitoramento de seu recurso agora estão fluindo para a conta de armazenamento.

## <a name="route-data-to-the-storage-account-using-the-azure-cli"></a>Rotear dados para a conta de armazenamento usando o CLI do Azure

Para habilitar o armazenamento de logs de diagnóstico em uma conta de armazenamento, execute o seguinte `az monitor diagnostic-settings` comando de CLI do Azure:

```azurecli-interactive
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

```azurecli-interactive
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
1. Clique em **BLOBs** e, em seguida, no contêiner rotulado como **insights-logs-keydeliveryrequests**. Esse é o contêiner que tem seus logs. Os dados de monitoramento são divididos em contêineres por ID de recurso e, em seguida, por data e hora.
1. Navegue para o arquivo PT1H.json clicando nos contêineres, em ID de recurso, data e hora. Clique no arquivo PT1H.json e clique em **Baixar**.

 Agora você pode exibir o evento do JSON armazenado na conta de armazenamento.

### <a name="examples-of-pt1hjson"></a>Exemplos de PT1H.jsem

#### <a name="clear-key-delivery-log"></a>Limpar o log de distribuição de chaves

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

#### <a name="widevine-encrypted-key-delivery-log"></a>Log de distribuição de chaves criptografadas Widevine

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

## <a name="additional-notes"></a>Observações adicionais

* O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="see-also"></a>Veja também

* [Métricas do Azure Monitor](../../azure-monitor/data-platform.md)
* [Azure Monitor logs de diagnóstico](../../azure-monitor/essentials/platform-logs-overview.md)
* [Como coletar e consumir dados de log de seus recursos do Azure](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="next-steps"></a>Próximas etapas

[Métricas de monitoramento](media-services-metrics-howto.md)
