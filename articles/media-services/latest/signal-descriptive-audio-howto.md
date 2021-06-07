---
title: Sinalizar faixas de áudio descritivas com os Serviços de Mídia v3
description: Siga as etapas deste tutorial para carregar um arquivo, codificar o vídeo, adicionar faixas de áudio descritivo e transmitir o conteúdo com os Serviços de Mídia v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 00a3fa397bf88520fa4923b6fbe7495c0aa0b8a2
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277395"
---
# <a name="signal-descriptive-audio-tracks"></a>Sinalizar faixas de áudio descritivo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Você pode adicionar uma faixa de narração ao seu vídeo para ajudar os clientes com deficiência visual a acompanhá-lo. Para sinalizar as faixas de áudio descritivo nos Serviços de Mídia v3, você as anota no arquivo de manifesto.

Este artigo mostra como codificar um vídeo, carregar um arquivo MP4 apenas com o áudio descritivo (codec AAC) no ativo de saída e editar o arquivo .ism para incluir o áudio descritivo.

## <a name="prerequisites"></a>Pré-requisitos

- [Crie uma conta de Serviços de Mídia](./account-create-how-to.md).
- Siga as etapas em [Acessar API de Serviços de Mídia com a CLI do Azure](./access-api-howto.md) e salve as credenciais. Você precisará usá-las para acessar a API.
- Confira [Empacotamento dinâmico](encode-dynamic-packaging-concept.md).
- Leia o tutorial [Carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md).

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Criar um ativo de entrada e carregar um arquivo local para ele 

A função **CreateInputAsset** cria um novo [Ativo](/rest/api/media/assets) de entrada e carrega o arquivo de vídeo local especificado para ele. Este **Ativo** é usado como entrada para o trabalho de codificação. Em Serviços de Mídia v3, a entrada para um **Trabalho** pode ser um **Ativo** ou o conteúdo que você disponibiliza a sua conta do Serviços de Mídia por meio de URLs de HTTPS. 

Se quiser saber como codificar usando uma URL HTTPS, confira [este](job-input-from-http-how-to.md) artigo.  

Em Serviços de Mídia v3, você usará as APIs de Armazenamento do Microsoft Azure para carregar os arquivos. O snippet .NET a seguir mostra como.

A função a seguir realiza essas ações:

* Cria um **Ativo** 
* Obtém uma [URL SAS](../../storage/common/storage-sas-overview.md) gravável para o [contêiner no armazenamento](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) do ativo
* Carrega o arquivo para o contêiner no armazenamento usando a URL de SAS

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Se você precisar passar o nome do ativo de entrada criado para outros métodos, use a propriedade `Name` no objeto de ativo retornado por `CreateInputAssetAsync`, por exemplo, inputAsset.Name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Criar um ativo de saída para armazenar o resultado do trabalho de codificação

O [Ativo](/rest/api/media/assets) de saída armazena o resultado do trabalho de codificação. A função a seguir mostra como criar um ativo de saída.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Se você precisar passar o nome do ativo de saída criado para outros métodos, use a propriedade `Name` no objeto de ativo retornado por `CreateIOutputAssetAsync`, por exemplo, outputAsset.Name. 

No caso deste artigo, passe o valor `outputAsset.Name` para as funções `SubmitJobAsync` e `UploadAudioIntoOutputAsset`.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Criar uma transformação e um trabalho que codifica o arquivo carregado

Ao codificar ou processar o conteúdo nos Serviços de Mídia do Microsoft Azure, é um padrão comum para configurar as configurações de codificação como uma receita. Em seguida, você pode enviar um **Trabalho** para aplicar essa fórmula a um vídeo. Ao enviar novos trabalhos para cada novo vídeo, você está aplicando essa fórmula em todos os vídeos na biblioteca. Uma receita nos Serviços de Mídia do Microsoft Azure é chamada de **Transformação**. Para obter mais informações, consulte [Transformações e Trabalhos](./transform-jobs-concept.md). O exemplo descrito neste tutorial define uma receita que codifica o vídeo para transmitir para uma variedade de dispositivos de iOS e Android. 

O exemplo a seguir cria uma transformação (se não houver uma).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

A função a seguir envia um trabalho.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguarde a conclusão do trabalho

Este trabalho levará algum tempo para concluir e quando você desejar ser notificado. É recomendável usar a Grade de Eventos para aguardar a conclusão do trabalho.

O trabalho normalmente passa pelos seguintes estados: **Agendado**, **Em fila**, **Processamento**, **Concluído** (o estado final). Se o trabalho encontrou um erro, você receberá o estado do **Erro**. Se o trabalho está no processo de ser cancelado, você obterá **Cancelando** e **Cancelado** quando estiver pronto.

Veja mais informações em [Lidando com os eventos da Grade de Eventos](monitoring/reacting-to-media-services-events.md).

## <a name="upload-the-audio-only-mp4-file"></a>Carregar o arquivo MP4 de áudio

Carregue o arquivo MP4 adicional (codec AAC) que contém apenas o áudio descritivo no ativo de saída.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

Aqui está um exemplo de uma chamada para a função `UpoadAudioIntoOutputAsset`:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Editar o arquivo .ism

Quando o trabalho de codificação é concluído, o ativo de saída contém os arquivos gerados por ele. 

1. No portal do Azure, navegue até a conta de armazenamento associada à sua conta dos Serviços de Mídia. 
1. Localize o contêiner com o nome do ativo de saída. 
1. No contêiner, localize o arquivo .ism e clique em **Editar blob** (na janela à direita). 
1. No arquivo .ism, adicione as informações sobre o arquivo MP4 de áudio carregado (codec AAC) que contém o áudio descritivo e pressione **Salvar** quando terminar.

    Para sinalizar as faixas de áudio descritivo, você precisa adicionar os parâmetros "accessibility" e "role" ao arquivo .ism. É sua responsabilidade definir esses parâmetros corretamente para sinalizar uma faixa de áudio como descrição de áudio. Por exemplo, adicione `<param name="accessibility" value="description" />` e `<param name="role" value="alternate" />` ao arquivo.ism para uma faixa de áudio específica, como mostra o exemplo a seguir.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>Obter um Localizador de Streaming

Depois que a codificação for concluída, a próxima etapa é tornar no vídeo no ativo de saída disponível para os clientes para reprodução. Você pode fazer isso em duas etapas: primeiro, crie um [Localizador de Streaming](/rest/api/media/streaminglocators) e, em seguida, crie as URLs de streaming que os clientes podem usar. 

O processo de criação de um **Localizador de Streaming** é chamado de publicação. Por padrão, o **Localizador de Streaming** é válido imediatamente após você fazer as chamadas à API e dura até ser excluído, a menos que você configure os horários de início e término opcionais. 

Ao criar um [StreamingLocator](/rest/api/media/streaminglocators), você precisará especificar os detalhes desejados **StreamingPolicyName**. Neste exemplo, você fará o streaming limpo (ou conteúdo não criptografado), de modo que a política de streaming clara predefinida (**PredefinedStreamingPolicy.ClearStreamingOnly**) seja utilizada.

> [!IMPORTANT]
> Ao usar uma [Política de Streaming](/rest/api/media/streamingpolicies) personalizada, você deve criar um conjunto limitado de políticas para a conta de Serviço de Mídia e reutilizá-los para os Localizadores de Streaming sempre que as mesmas opções e protocolos de criptografia forem necessários. A conta de Serviço de Mídia tem uma cota para o número de entradas de Política de Streaming. Você não deve criar uma nova Política de Streaming para cada Localizador de Streaming.

O código a seguir pressupõe que você está chamando a função com um locatorName exclusivo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Ao passo que o exemplo deste tópico discute streaming, você pode usar a mesma chamada para criar um Localizador de Streaming para fornecimento de vídeo por meio de download progressivo.

### <a name="get-streaming-urls"></a>Obter URLs de streaming

Agora que o [Localizador de Streaming](/rest/api/media/streaminglocators) foi criado, você pode obter as URLs de streaming, conforme mostrado em **GetStreamingURLs**. Para criar uma URL, você precisa concatenar o nome do host do [Ponto de Extremidade de Streaming](/rest/api/media/streamingendpoints) com o caminho do **Localizador de Streaming**. Neste exemplo, o *padrão* do **Ponto de Extremidade de Streaming** é usado. Ao criar uma conta de Serviço de Mídia, este *padrão* **Ponto de Extremidade de Streaming** estará em um estado parado, portanto você precisa chamar **Iniciar**.

> [!NOTE]
> Neste método, é necessário o locatorName que foi usado durante a criação do **Localizador de Streaming** para o Ativo gerado.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Testar com o Player de Mídia do Azure

Para testar o streaming, este artigo usa o Player de Mídia do Azure. 

> [!NOTE]
> Se um player estiver hospedado em um site https, atualize a URL para "https".

1. Abra um navegador da Web e navegue até [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Na caixa **URL:** , cole um dos valores de URL de streaming que você obteve do aplicativo. 
 
     Você poderá colar a URL no formato HLS, Dash ou Smooth e o Player de Mídia do Azure alternará para um protocolo de streaming apropriado para reprodução em seu dispositivo automaticamente.
3. Pressione **Atualizar Player**.

O Player de Mídia do Azure pode ser usado para testes, mas não deve ser usado em um ambiente de produção. 

## <a name="next-steps"></a>Próximas etapas

[Analisar vídeos](analyze-videos-tutorial.md)
