---
title: Transmitir arquivos de vídeo com os Serviços de Mídia do Azure – .NET
description: Siga as etapas deste tutorial para usar o .NET para criar uma conta dos Serviços de Mídia do Azure, codificar um arquivo e transmiti-lo para o Player de Mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: serviços de mídia do azure, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: e7c35e079a6f4bf6c40992debace60e10631a8ba
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962032"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>Tutorial: Codificar um arquivo remoto baseado em URL e transmitir o vídeo – .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este tutorial mostra como é fácil codificar e iniciar a transmissão de vídeos por streaming em vários navegadores e dispositivos usando os Serviços de Mídia do Azure. Um conteúdo de entrada pode ser especificado usando URLs HTTPS, URLs SAS ou caminhos para arquivos localizados no Armazenamento de Blobs do Azure.
O exemplo neste tópico codifica o conteúdo disponibilizado por meio de uma URL HTTPS. Observe que, no momento, o AMS v3 não dá suporte à codificação de transferência em partes sobre URLs HTTPS.

Ao final do tutorial, você poderá transmitir um vídeo por streaming.  

![Reproduzir o vídeo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se o Visual Studio não estiver instalado, você poderá obter o [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Crie uma conta de Serviços de Mídia](./account-create-how-to.md).<br/>Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia.
- Siga as etapas em [Acessar API de Serviços de Mídia com a CLI do Azure](./access-api-howto.md) e salve as credenciais. Você precisará usá-las para acessar a API.

## <a name="download-and-configure-the-sample"></a>Baixar e configurar o exemplo

Clone um repositório do GitHub que contém o exemplo de streaming de .NET em sua máquina usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

O exemplo está localizado na pasta [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

Abra [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) no projeto que você baixou. Substitua os valores pelas credenciais que você obteve de [acesso às APIs](./access-api-howto.md).

O exemplo executa as ações a seguir:

1. Cria uma **Transformação** (primeiro, verifica se a transformação especificada existe). 
2. Cria um **Ativo** de saída que é usado como a saída do **Trabalho** de codificação.
3. Cria a entrada do **Trabalho** com base em uma URL HTTPS.
4. Envia o **Trabalho** de codificação usando a entrada e a saída criadas anteriormente.
5. Verifica o status do trabalho.
6. Cria um **Localizador de Streaming**.
7. Compila as URLs de streaming.

Para obter explicações sobre cada função no exemplo, examine o código e os comentários neste [arquivo de origem](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

Quando você executa o aplicativo, as URLs que podem ser usadas para reproduzir o vídeo usando protocolos diferentes são exibidas. 

1. Pressione Ctrl + F5 para executar o aplicativo *EncodeAndStreamFiles*.
2. Escolha o protocolo **HLS** da Apple (termina com *manifest(format=m3u8-aapl)* ) e copie a URL de streaming do console.

![Captura de tela da saída do aplicativo EncodeAndStreamFiles no Visual Studio mostrando três URLs de streaming para uso no Player de Mídia do Azure.](./media/stream-files-tutorial-with-api/output.png)

No exemplo de [código-fonte](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs), você pode ver como a URL é criada. Para criá-la, você precisa concatenar o nome do host do ponto de extremidade de streaming e o caminho do localizador de streaming.  

## <a name="test-with-azure-media-player"></a>Testar com o Player de Mídia do Azure

Para testar o streaming, este artigo usa o Player de Mídia do Azure. 

> [!NOTE]
> Se um player estiver hospedado em um site https, atualize a URL para "https".

1. Abra um navegador da Web e navegue até [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Na caixa **URL:** , cole um dos valores de URL de streaming que você obteve ao executar o aplicativo. 
 
     Você poderá colar a URL no formato HLS, Dash ou Smooth e o Player de Mídia do Azure alternará para um protocolo de streaming apropriado para reprodução em seu dispositivo automaticamente.
3. Pressione **Atualizar Player**.

O Player de Mídia do Azure pode ser usado para testes, mas não deve ser usado em um ambiente de produção. 

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste tutorial, exclua o grupo de recursos.

Execute este comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Examinar o código

Para obter explicações sobre cada função no exemplo, examine o código e os comentários neste [arquivo de origem](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

O tutorial [carregar, codificar e transmitir arquivos por streaming](stream-files-tutorial-with-api.md) fornece um exemplo mais avançado de streaming com explicações detalhadas. 

### <a name="job-error-codes"></a>Códigos de erro do trabalho

Confira [Códigos de erro](/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Multithreading

Os SDKs dos Serviços de Mídia do Azure v3 não são thread-safe. Ao trabalhar com aplicativos multithread, você deve gerar um novo objeto AzureMediaServicesClient por thread.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md)
