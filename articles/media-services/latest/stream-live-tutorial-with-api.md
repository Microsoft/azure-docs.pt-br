---
title: Transmitir ao vivo com Serviços de Mídia do Azure v3 | Microsoft Docs
description: Este tutorial orienta você pelas etapas de transmissão ao vivo com o Serviços de Mídia v3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 5028fd4179f19634b41bb46a5f6df40f36cc8e29
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275578"
---
# <a name="tutorial-stream-live-with-media-services"></a>Tutorial: Transmissão ao vivo com os Serviços de Mídia

> [!NOTE]
> Embora o tutorial use os exemplos do [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet), as etapas gerais são as mesmas para [API REST](https://docs.microsoft.com/rest/api/media/liveevents), [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest) ou outros [SDKs](media-services-apis-overview.md#sdks) suportados.

Nos Serviços de Mídia do Azure, [Eventos ao Vivo](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pelo processamento do conteúdo de transmissão ao vivo. Um Evento ao Vivo fornece um ponto de extremidade de entrada (URL de entrada) que você fornece a um codificador dinâmico. O Evento ao Vivo recebe fluxos de entrada ao vivo do codificador dinâmico e o disponibiliza para streaming por meio de um ou mais [Pontos de Extremidade de Streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints). Eventos ao Vivo também fornecem um ponto de extremidade de visualização (URL de visualização) usado para visualizar e validar o fluxo antes de processamento e entrega adicionais. Este tutorial mostra como usar o .NET Core para criar um tipo de **passagem** de um evento ao vivo. 

Este tutorial mostra como:    

> [!div class="checklist"]
> * Baixe o aplicativo de exemplo descrito no tópico
> * Examinar o código que executa a transmissão ao vivo
> * Assista ao evento com o [Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) em https://ampdemo.azureedge.net
> * Limpar recursos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir o tutorial.

- Instale o Visual Studio Code ou o Visual Studio.
- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).<br/>Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia.
- Siga as etapas em [Acessar API de Serviços de Mídia com a CLI do Azure](access-api-cli-how-to.md) e salve as credenciais. Você precisará usá-las para acessar a API.
- Uma câmera ou um dispositivo (como laptop) usado para transmitir um evento.
- Um codificador ao vivo local que converte sinais da câmera em fluxos enviados para o serviço de transmissão ao vivo dos Serviços de Mídia. O fluxo deve estar no formato **RTMP** ou **Smooth Streaming**.

> [!TIP]
> Certifique-se de revisar [Transmissão ao vivo com Serviços de Mídia v3](live-streaming-overview.md) antes de continuar. 

## <a name="download-and-configure-the-sample"></a>Baixar e configurar o exemplo

Clone um repositório do GitHub que contém o exemplo de streaming de .NET em sua máquina usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

A amostra de transmissão ao vivo está localizada na pasta [Dinâmica](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp).

Abra [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) no projeto que você baixou. Substitua os valores pelas credenciais que você obteve de [acesso às APIs](access-api-cli-how-to.md).

> [!IMPORTANT]
> Esta amostra usa o sufixo exclusivo para cada recurso. Se você cancelar a depuração ou encerrar o aplicativo sem executá-lo, finalizará vários Eventos ao Vivo em sua conta. <br/>Interrompa a execução de Eventos ao Vivo. Caso contrário, você será **cobrado**!

## <a name="examine-the-code-that-performs-live-streaming"></a>Examinar o código que executa a transmissão ao vivo

Esta seção examina as funções definidas no arquivo [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) do projeto *MediaV3LiveApp*.

A amostra cria um sufixo exclusivo para cada recurso, de modo que não ocorra conflitos de nome se executar a amostra várias vezes sem limpeza.

> [!IMPORTANT]
> Esta amostra usa o sufixo exclusivo para cada recurso. Se você cancelar a depuração ou encerrar o aplicativo sem executá-lo, finalizará vários Eventos ao Vivo em sua conta. <br/>
> Interrompa a execução de Eventos ao Vivo. Caso contrário, você será **cobrado**!
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, é necessário criar um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. No código que você clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no arquivo de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Criar um evento ao vivo

Esta seção mostra como criar um tipo de **passagem** do Evento ao Vivo (LiveEventEncodingType definido como Nenhum). Para obter mais informações sobre os tipos de Eventos ao vivo disponíveis, consulte [Tipos de evento ao vivo](live-events-outputs-concept.md#live-event-types). 
 
Algumas coisas que talvez você queira especificar ao criar o evento ao vivo são:

* Local dos Serviços de Mídia 
* O protocolo de streaming para Evento ao Vivo (atualmente, há suporte para os protocolos RTMP e Smooth Streaming).<br/>Não é possível alterar a opção de protocolo enquanto o Evento ao vivo ou suas Saídas ao vivo associadas estiverem em execução. Se você precisar de protocolos diferentes, crie um Evento ao vivo separado para cada protocolo de streaming.  
* Restrições de IP sobre a ingestão e versão prévia. É possível definir os endereços IP que têm permissão para ingerir um vídeo neste Evento ao vivo. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo, '10.0.0.1'), um intervalo IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22) ou um intervalo IP usando um endereço IP e uma máscara de sub-rede com notação decimal com ponto (por exemplo, '10.0.0.1(255.255.252.0)').<br/>Se nenhum endereço IP for especificado e não houver definição de regra, nenhum endereço IP será permitido. Para permitir qualquer endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP devem estar em um dos formatos a seguir: endereço IPv4 com 4 números e intervalo de endereços CIDR.
* Ao criar o evento, é possível especificar para iniciá-lo automaticamente. <br/>Quando a inicialização automática é definida como verdadeira, o evento em tempo real será iniciado após a criação. Isso significa que a cobrança começa assim que o Evento ao vivo começa a ser transmitido. É necessário chamar explicitamente Parar no recurso Evento ao Vivo para interromper a cobrança adicional. Para saber mais, confira [Estados e cobrança do Evento ao vivo](live-event-states-billing.md).
* Para que uma URL de ingestão seja preditiva, defina o modo de "personalização". Para obter informações detalhadas, consulte [URLs de ingestão de eventos ao vivo](live-events-outputs-concept.md#live-event-ingest-urls).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>Obter URLs de ingestão

Depois que o Evento ao Vivo é criado, você pode obter URLs de ingestão que você fornecerá ao codificador dinâmico. O codificador usa essas URLs para gerar entrada de um fluxo ao vivo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Obter a URL de versão prévia

Use o previewEndpoint para visualizar e verificar se a entrada do codificador está realmente sendo recebida.

> [!IMPORTANT]
> Certifique-se de que o vídeo está fluindo para a URL de versão prévia antes de continuar!

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Criar e gerenciar Eventos ao Vivo e Saídas ao Vivo

Uma vez que o fluxo está fluindo para o Evento ao Vivo, é possível começar o evento de transmissão criando um Ativo, uma Saída ao Vivo e um Localizador de Streaming. Isso vai arquivar o fluxo e torná-lo disponível para usuários por meio do ponto de extremidade de Streaming. 

#### <a name="create-an-asset"></a>Criar um ativo

Criar um Ativo para a Saída ao Vivo usar.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Criar uma Saída ao Vivo

As Saídas ao Vivo iniciam na criação e terminam quando são excluídas. Quando você exclui a Saída ao Vivo, não está excluindo o Ativo subjacente e o conteúdo no ativo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Criar um Localizador de Streaming

> [!NOTE]
> Quando sua conta dos Serviços de Mídia é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para iniciar o streaming do conteúdo e aproveitar o [empacotamento dinâmico](dynamic-packaging-overview.md) e a criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar no estado de **Execução**. 

Quando você publica o ativo de Saída ao Vivo usando um Localizador de Streaming, o Evento ao Vivo (até a duração da janela DVR) continuará visível até a expiração ou a exclusão do Localizador de Streaming, o que ocorrer primeiro.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Limpar recursos na conta dos Serviços de Mídia

Se você tiver terminado o fluxo de eventos e deseja limpar os recursos provisionados anteriormente, siga o procedimento a seguir.

* Pare de enviar o fluxo por push por meio do codificador.
* Interrompa o Evento ao Vivo. Depois que o Evento ao Vivo estiver parado, ele não incorrerá em nenhum encargo. Quando for necessário iniciá-lo novamente ele terá a mesma URL de ingestão, portanto, você não precisará reconfigurar seu codificador.
* Você pode parar seu ponto de extremidade de Streaming, a menos que você deseje continuar a fornecer o arquivo morto do evento ao vivo como um fluxo sob demanda. Se o Evento ao Vivo estiver no estado interrompido, ele não incorrerá em nenhum encargo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

O código a seguir mostra como limpar sua conta de todos os Eventos ao Vivo:

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>Assistir ao evento

Para assistir ao evento, copie a URL de streaming obtida quando você executou o código descrito em Criar um Localizador de Streaming e use um player de sua escolha. É possível usar o [Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) para testar o stream em https://ampdemo.azureedge.net. 

O Evento ao Vivo é convertido automaticamente em conteúdo sob demanda quando é interrompido. Mesmo depois de você parar e excluir o evento, os usuários poderão transmitir seu conteúdo arquivado como vídeo por demanda enquanto você não excluir o ativo. Não será possível excluir um ativo se este for usado por um evento; o evento deve ser excluído primeiro. 

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste tutorial, exclua o grupo de recursos criados anteriormente.

Execute este comando da CLI:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Deixar o Evento ao Vivo em execução gera custos de cobrança. Esteja ciente de que, se o projeto/programa falhar ou for fechado por qualquer motivo, isso poderá deixar o Evento ao Vivo em execução em um estado de cobrança.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

[Transmissão de arquivos](stream-files-tutorial-with-api.md)
 
