---
title: Como codificar e transmitir arquivos de vídeo com o Node.JS
description: Como transmitir arquivos de vídeo com o Node.JS. Execute as etapas deste tutorial para criar uma nova conta dos Serviços de Mídia do Azure, codificar um arquivo e transmiti-lo para o Player de Mídia do Azure.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: serviços de mídia do azure, stream, Node.JS
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: 5bb061af37f6f6d7e6e27cf25f0faa63bca7353c
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109179"
---
# <a name="how-to-encode-and-stream-video-files-with-nodejs"></a>Como codificar e transmitir arquivos de vídeo com o Node.JS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este início rápido mostra como é fácil codificar e iniciar a transmissão de vídeos por streaming em vários navegadores e dispositivos usando os Serviços de Mídia do Azure. Um arquivo de vídeo de entrada pode ser especificado usando URLs HTTPS, URLs SAS ou caminhos para arquivos localizados no Armazenamento de Blobs do Azure.

No final deste guia de início rápido, você saberá:

- Como codificar com o Node.JS
- Como transmitir com o Node.JS
- Como carregar um arquivo de uma URL HTTPS com o Node.JS
- Como usar um player do cliente HLS ou DASH com o Node.JS

O exemplo neste artigo codifica o conteúdo que você disponibiliza por meio de uma URL HTTPS. Observe que, no momento, o AMS v3 não dá suporte à codificação de transferência em partes sobre URLs HTTPS.

![Reproduzir o vídeo](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Instale o [Node.js](https://nodejs.org/en/download/)
- [Crie uma conta de Serviços de Mídia](./create-account-howto.md).<br/>Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia.
- Siga as etapas em [Acessar API de Serviços de Mídia com a CLI do Azure](./access-api-howto.md) e salve as credenciais. Você precisará usá-las para acessar a API.
- Percorra as instruções [Configurar e Conectar-se com o Node.js](./configure-connect-nodejs-howto.md) primeiro para entender como usar o SDK do cliente do Node.js

## <a name="download-and-configure-the-sample"></a>Baixar e configurar o exemplo

Clone um repositório do GitHub que contém o exemplo de streaming de Node.js em seu computador usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

O exemplo está localizado na pasta [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample).

Abra o [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) no seu projeto baixado. Atualize o arquivo *sample.env* na pasta raiz com os valores e as credenciais que você obteve ao [acessar APIs](./access-api-howto.md). Renomeie o arquivo *sample.env* como *.env* (sim, apenas a extensão).

O exemplo executa as ações a seguir:

1. Cria uma **Transformação** com uma [Predefinição de Codificação com Reconhecimento de Conteúdo](./encode-content-aware-concept.md). Primeiro, ele verifica se a Transformação especificada existe.
1. Cria um **Ativo** de saída que é usado pelo **Trabalho** de codificação para conter a saída
1. Opcionalmente, carrega um arquivo local usando o SDK de Armazenamento de Blobs
1. Cria a entrada do **Trabalho** com base em uma URL HTTPS ou arquivo carregado
1. Envia o **Trabalho** de codificação usando a entrada e a saída criadas anteriormente
1. Verifica o status do Trabalho
1. Baixa a saída do trabalho de codificação em uma pasta local
1. Cria um **Localizador de Streaming** para usar no player
1. Cria URLs de streaming para HLS e DASH
1. Reproduz novamente o conteúdo em um aplicativo de player – Player de Mídia do Azure

## <a name="run-the-sample"></a>Execute o exemplo

1. O aplicativo baixa arquivos codificados. Crie uma pasta na qual deseja colocar os arquivos de saída e atualize o valor da variável **outputFolder** no arquivo [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/StreamFilesSample/index.ts#L59). Ele é definido como "Temp" por padrão.
1. Abra **prompt de comando** e navegue até o diretório do exemplo.
1. Altere o diretório na pasta AMSv3Samples.

    ```bash
    cd AMSv3Samples
    ```

1. Instale os pacotes usados no arquivo *packages.json*.

    ```bash
    npm install 
    ```

1. Altere o diretório para a pasta *StreamFilesSample*.

    ```bash
    cd StreamFilesSample
    ```

1. Inicie o Visual Studio Code na pasta *AMSv3Samples*. (É necessário que ele seja iniciado na pasta em que a pasta *.vscode* e os arquivos *tsconfig.json* estão localizados.)

    ```bash
    cd ..
    code .
    ```

Abra a pasta do *StreamFilesSample* e abra o arquivo *index.ts* no editor do Visual Studio Code.
Enquanto estiver no arquivo *index.ts*, pressione F5 para iniciar o depurador.

## <a name="test-with-azure-media-player"></a>Testar com o Player de Mídia do Azure

Use o Player de Mídia do Azure para testar o fluxo. Você também pode usar qualquer player em conformidade com HLS ou DASH, como o Shaka Player, HLS.js, Dash.js ou outros.

Você deve ser capaz de clicar no link gerado no exemplo e iniciar o AMP Player com o manifesto DASH já carregado.

> [!NOTE]
> Se um player estiver hospedado em um site https, atualize a URL para "https".

1. Abra um navegador da Web e navegue até [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Na caixa **URL:** , cole um dos valores de URL de streaming que você obteve ao executar o aplicativo. Você pode colar a URL no formato HLS, Dash ou Smooth e o Player de Mídia do Azure mudará para um protocolo de streaming apropriado para reprodução no seu dispositivo automaticamente.
3. Pressione **Atualizar Player**.

O Player de Mídia do Azure pode ser usado para testes, mas não deve ser usado em um ambiente de produção.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste tutorial, exclua o grupo de recursos.

Execute este comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Mais documentação para desenvolvedores para o Node.js no Azure

- [Azure para desenvolvedores do JavaScript e do Node.js](/azure/developer/javascript/)
- [Código-fonte dos Serviços de Mídia no repositório do GitHub @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentação do Pacote do Azure para desenvolvedores do Node.js](/javascript/api/overview/azure/)

## <a name="see-also"></a>Confira também

- [Códigos de erro de trabalho](/rest/api/media/jobs/get#joberrorcode).
- [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure para desenvolvedores do JavaScript e do Node.js](/azure/developer/javascript/)
- [Código-fonte dos Serviços de Mídia no repositório @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Próximas etapas

> [Conceitos dos Serviços de Mídia](concepts-overview.md)
