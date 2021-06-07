---
title: Transmitir arquivos de vídeo com a CLI dos Serviços de Mídia do Azure
description: Siga as etapas deste tutorial para usar a CLI do Azure para criar uma conta dos Serviços de Mídia do Azure, codificar um arquivo e transmiti-lo para o Player de Mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: serviços de mídia do azure, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c78205d7e2b41628de9e8b92c9fa5506e82158cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954489"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---azure-cli"></a>Tutorial: Codificar um arquivo remoto baseado em URL e transmitir o vídeo – CLI do Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este tutorial mostra como é fácil codificar e iniciar a transmissão de vídeos por streaming em vários navegadores e dispositivos usando os Serviços de Mídia do Azure e a CLI do Azure. Um conteúdo de entrada pode ser especificado usando HTTPS, URLs SAS ou caminhos para arquivos localizados no armazenamento de Blobs do Azure.

O exemplo neste tópico codifica o conteúdo disponibilizado por meio de uma URL HTTPS. No momento, os Serviços de Mídia v3 não dão suporte à codificação de transferência em partes sobre URLs HTTPS.

Ao final deste tutorial, você poderá transmitir um vídeo por streaming.  

![Reproduzir o vídeo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia

Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisará criar uma conta nos Serviços de Mídia. A conta dos Serviços de Mídia precisa estar associada a uma ou mais contas de armazenamento.

A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. É recomendável que você use contas de armazenamento que estão no mesmo local como a conta de serviços de mídia para limitar os custos de latência e os dados de saída.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

```azurecli-interactive
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

Neste exemplo, criamos uma conta de uso geral v2, LRS Padrão.

Caso deseje fazer experimentos com contas de armazenamento, use `--sku Standard_LRS`. No entanto, ao escolher um SKU para produção, você deverá considerar `--sku Standard_RAGRS`, que fornece replicação geográfica para a continuidade dos negócios. Para obter mais informações, confira [Contas de armazenamento](/cli/azure/storage/account).

```azurecli-interactive
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Criar uma conta de Serviços de Mídia do Azure

```azurecli-interactive
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

Você receberá uma resposta como esta:

```
{
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "location": "West US 2",
  "mediaServiceId": "8b569c2e-d648-4fcb-9035-c7fcc3aa7ddf",
  "name": "amsaccount",
  "resourceGroup": "amsResourceGroupTest",
  "storageAccounts": [
    {
      "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Storage/storageAccounts/amsstorageaccount",
      "resourceGroup": "amsResourceGroupTest",
      "type": "Primary"
    }
  ],
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```

## <a name="start-the-streaming-endpoint"></a>Iniciar o ponto de extremidade de streaming

O comando da CLI do Azure a seguir inicia o **Ponto de Extremidade de Streaming** padrão.

```azurecli-interactive
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Você receberá uma resposta como esta:

```
{
  "accessControl": null,
  "availabilitySetName": null,
  "cdnEnabled": true,
  "cdnProfile": "AzureMediaStreamingPlatformCdnProfile-StandardVerizon",
  "cdnProvider": "StandardVerizon",
  "created": "2019-02-06T21:58:03.604954+00:00",
  "crossSiteAccessPolicies": null,
  "customHostNames": [],
  "description": "",
  "freeTrialEndTime": "2019-02-21T22:05:31.277936+00:00",
  "hostName": "amsaccount-usw22.streaming.media.azure.net",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingendpoints/default",
  "lastModified": "2019-02-06T21:58:03.604954+00:00",
  "location": "West US 2",
  "maxCacheAge": null,
  "name": "default",
  "provisioningState": "Succeeded",
  "resourceGroup": "amsResourceGroup",
  "resourceState": "Running",
  "scaleUnits": 0,
  "tags": {},
  "type": "Microsoft.Media/mediaservices/streamingEndpoints"
}
```

Se o ponto de extremidade de streaming já estiver em execução, você receberá esta mensagem:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Crie uma transformação para a codificação de taxa de bits adaptável

Crie uma **Transformação** para configurar tarefas comuns para codificar ou analisar vídeos. Neste exemplo, queremos fazer uma codificação de taxa de bits adaptável. Em seguida, você enviará um trabalho na transformação criada. O trabalho é a solicitação aos Serviços de Mídia para que eles apliquem a transformação a determinado conteúdo de áudio ou vídeo de entrada.

```azurecli-interactive
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Você receberá uma resposta como esta:

```
{
  "created": "2019-02-15T00:11:18.506019+00:00",
  "description": "a simple Transform for Adaptive Bitrate Encoding",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform",
  "lastModified": "2019-02-15T00:11:18.506019+00:00",
  "name": "testEncodingTransform",
  "outputs": [
    {
      "onError": "StopProcessingJob",
      "preset": {
        "odatatype": "#Microsoft.Media.BuiltInStandardEncoderPreset",
        "presetName": "AdaptiveStreaming"
      },
      "relativePriority": "Normal"
    }
  ],
  "resourceGroup": "amsResourceGroup",
  "type": "Microsoft.Media/mediaservices/transforms"
}
```

## <a name="create-an-output-asset"></a>Criar um ativo de saída

Cria um **Ativo** de saída que é usado como a saída do trabalho de codificação.

```azurecli-interactive
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Você receberá uma resposta como esta:

```
{
  "alternateId": null,
  "assetId": "96427438-bbce-4a74-ba91-e38179b72f36",
  "container": null,
  "created": "2019-02-14T23:58:19.127000+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/assets/testOutputAssetName",
  "lastModified": "2019-02-14T23:58:19.127000+00:00",
  "name": "testOutputAssetName",
  "resourceGroup": "amsResourceGroup",
  "storageAccountName": "amsstorageaccount",
  "storageEncryptionFormat": "None",
  "type": "Microsoft.Media/mediaservices/assets"
}
```

## <a name="start-a-job-by-using-https-input"></a>Iniciar um trabalho por meio de HTTPS de entrada

Ao enviar trabalhos para processar seus vídeos, você precisará informar os Serviços de Mídia onde encontrar o vídeo de entrada. Uma das opções é especificar uma URL HTTP como um trabalho de entrada, conforme mostrado neste exemplo.

Ao executar `az ams job start`, você pode definir um rótulo na saída do trabalho. Em seguida, você poderá usar o rótulo para identificar para que é o ativo de saída.

- Se você atribuir um valor ao rótulo, defina '--output-assets' como "assetname=label".
- Se você não atribuir um valor ao rótulo, defina '--output-assets' como "assetname=".

  Observe que "=" é adicionado ao `output-assets`.

```azurecli-interactive
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup
```

Você receberá uma resposta como esta:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

### <a name="check-status"></a>Verificar o status

Em 5 minutos, verifique o status do trabalho. Ele deverá indicar "Concluído". Se ele estiver concluído, verifique novamente em alguns minutos. Depois que ele indicar "Concluído", vá para a próxima etapa e crie um **Localizador de Streaming**.

```azurecli-interactive
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Criar o localizador de streaming e obtenha um caminho

Depois que a codificação for concluída, a próxima etapa será disponibilizar o vídeo no ativo de saída aos clientes para reprodução. Para fazê-lo, primeiro crie um Localizador de Streaming. Depois, compile as URLs de streaming que os clientes podem usar.

### <a name="create-a-streaming-locator"></a>Criar um localizador de streaming

```azurecli-interactive
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Você receberá uma resposta como esta:

```
{
  "alternativeMediaId": null,
  "assetName": "output-3b6d7b1dffe9419fa104b952f7f6ab76",
  "contentKeys": [],
  "created": "2019-02-15T04:35:46.270750+00:00",
  "defaultContentKeyPolicyName": null,
  "endTime": "9999-12-31T23:59:59.999999+00:00",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingLocators/testStreamingLocator",
  "name": "testStreamingLocator",
  "resourceGroup": "amsResourceGroup",
  "startTime": null,
  "streamingLocatorId": "e01b2be1-5ea4-42ca-ae5d-7fe704a5962f",
  "streamingPolicyName": "Predefined_ClearStreamingOnly",
  "type": "Microsoft.Media/mediaservices/streamingLocators"
}
```

### <a name="get-streaming-locator-paths"></a>Obtenha caminhos do localizador de streaming

```azurecli-interactive
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Você receberá uma resposta como esta:

```
{
  "downloadPaths": [],
  "streamingPaths": [
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)"
      ],
      "streamingProtocol": "Hls"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=mpd-time-csf)"
      ],
      "streamingProtocol": "Dash"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest"
      ],
      "streamingProtocol": "SmoothStreaming"
    }
  ]
}
```

Copie o caminho da HTTP Live Streaming (HLS). Nesse caso, use `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`.

## <a name="build-the-url"></a>Criar a URL

### <a name="get-the-streaming-endpoint-host-name"></a>Obtém o nome de host do ponto de extremidade de streaming

```azurecli-interactive
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

Copie o valor `hostName`. Nesse caso, use `amsaccount-usw22.streaming.media.azure.net`.

### <a name="assemble-the-url"></a>Montar a URL

"https://" + &lt;valor de hostName&gt; + &lt;valor do caminho do HLS&gt;

Aqui está um exemplo:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Testar a reprodução com o Player de Mídia do Azure

> [!NOTE]
> Se um player estiver hospedado em um site HTTPS, atualize a URL para "https".

1. Agora, abra um navegador da Web e vá para [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Na caixa da **URL:** , cole a URL criada na seção anterior. Você pode colar a URL no formato Smooth, Dash ou HLS. O Player de mídia do Azure usará automaticamente um protocolo de streaming apropriado para reprodução em seu dispositivo.
3. Selecione **Atualizar Player**.

>[!NOTE]
>O Player de Mídia do Azure pode ser usado para testes, mas não deve ser usado em um ambiente de produção.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que criou neste tutorial, exclua o grupo de recursos.

Execute este comando da CLI do Azure:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

[Visão geral dos Serviços de Mídia](media-services-overview.md)
