---
title: 'Tutorial: Gerar metadados de imagens do Azure'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você aprenderá a integrar o serviço de Pesquisa Visual Computacional do Azure a um aplicativo Web para gerar metadados para imagens.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: b51234ff121071de27ec8c91425f4a769c8d833e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008779"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Tutorial: Use a Pesquisa Visual Computacional para gerar metadados de imagem no Armazenamento do Azure

Neste tutorial, você aprenderá a integrar o serviço de Pesquisa Visual Computacional do Azure a um aplicativo Web para gerar metadados para imagens carregadas. Isso é útil para cenários de [DAM (gerenciamento de ativos digitais)](../overview.md#computer-vision-for-digital-asset-management), assim como quando uma empresa deseja gerar rapidamente legendas descritivas ou palavras-chave pesquisáveis para todas as suas imagens.

Um guia de aplicativo completo pode ser encontrado no [Laboratório do Armazenamento do Azure e dos Serviços Cognitivos](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) no GitHub; este tutorial aborda essencialmente o Exercício 5 do laboratório. Pode ser interessante criar o aplicativo completo seguindo cada etapa, mas se você quiser apenas saber como integrar a Pesquisa Visual Computacional a um aplicativo Web existente, leia aqui.

Este tutorial mostra como:

> [!div class="checklist"]
> * Criar um recurso da Pesquisa Visual Computacional no Azure
> * Executar a análise de imagem em imagens do Armazenamento do Azure
> * Anexar metadados a imagens do Armazenamento do Azure
> * Verificar metadados de imagem usando o Gerenciador de Armazenamento do Azure

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) ou posterior, com as cargas de trabalho "ASP.NET e desenvolvimento para a Web" e "Desenvolvimento do Azure" instaladas.
- Uma conta do Armazenamento do Azure com um contêiner de blobs configurado para armazenamento de imagens (siga o [Exercício 1 do Laboratório do Armazenamento do Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) caso precise de ajuda com esta etapa).
- A ferramenta Gerenciador do Armazenamento do Azure (siga o [Exercício 2 do Laboratório do Armazenamento do Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) caso precise de ajuda com esta etapa).
- Um aplicativo Web ASP.NET com acesso ao Armazenamento do Azure (siga o [Exercício 3 do Laboratório do Armazenamento do Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) para criar um aplicativo desse tipo rapidamente).

## <a name="create-a-computer-vision-resource"></a>Criar um recurso da Pesquisa Visual Computacional

Você precisará criar um recurso da Pesquisa Visual Computacional para sua conta do Azure; esse recurso gerencia seu acesso ao serviço de Pesquisa Visual Computacional do Azure. 

1. Siga as instruções em [Criar um recurso de Serviços Cognitivos do Azure](../../cognitive-services-apis-create-account.md) para criar um recurso de Pesquisa Visual Computacional.

1. Depois vá ao menu do grupo de recursos e clique na assinatura da API da Pesquisa Visual Computacional recém-criada. Copie a URL em **Ponto de extremidade** para algum lugar em que poderá recuperá-la com facilidade em pouco tempo. Em seguida, clique em **Mostrar chaves de acesso**.

    ![Página do portal do Azure a URL de ponto de extremidade e o link de chaves de acesso delineados](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. Na próxima janela, copie o valor de **KEY 1** para a área de transferência.

    ![Caixa de diálogo Gerenciar chaves, com o botão de cópia delineado](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Adicionar credenciais da Pesquisa Visual Computacional

Em seguida, você adicionará as credenciais necessárias ao seu aplicativo de modo que ele possa acessar os recursos da Pesquisa Visual Computacional.

Abra o aplicativo Web ASP.NET no Visual Studio e navegue para o arquivo **Web.config** na raiz do projeto. Adicione as instruções a seguir à seção `<appSettings>` do arquivo, substituindo `VISION_KEY` pela chave copiada na etapa anterior e `VISION_ENDPOINT` pela URL salva na etapa anterior.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Em seguida, no Gerenciador de Soluções, clique com o botão direito do mouse no projeto e use o comando **Gerenciar Pacotes NuGet** para instalar o pacote **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**. Esse pacote contém os tipos necessários para chamar a API da Pesquisa Visual Computacional.

## <a name="add-metadata-generation-code"></a>Adicionar o código de geração de metadados

Em seguida, você adicionará o código que, na verdade, aproveita o serviço de Pesquisa Visual Computacional para criar metadados para imagens. Essas etapas se aplicarão ao aplicativo ASP.NET no laboratório, mas você poderá adaptá-las ao seu próprio aplicativo. O importante é que, neste momento, você tem um aplicativo Web ASP.NET que pode carregar imagens em um contêiner do Armazenamento do Azure, ler as imagens dele e mostrá-las na exibição. Caso não tenha certeza sobre essa etapa, é melhor seguir o [Exercício 3 do Laboratório do Armazenamento do Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3). 

1. Abra o arquivo *HomeController.cs* da pasta **Controllers** do projeto e adicione as seguintes instruções `using` na parte superior do arquivo:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Em seguida, acesse o método **Upload**; esse método converte e carrega imagens no armazenamento de blobs. Adicione o código a seguir imediatamente após o bloco que começa com `// Generate a thumbnail` (ou ao final do processo de criação do blob de imagem). Esse código usa o blob que contém a imagem (`photo`) e usa a Pesquisa Visual Computacional para gerar uma descrição para essa imagem. A API da Pesquisa Visual Computacional também gera uma lista de palavras-chave que se aplicam à imagem. A descrição e as palavras-chave geradas são armazenadas nos metadados do blob para que possam ser recuperadas posteriormente.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. Em seguida, vá para o método **Index** no mesmo arquivo. Esse método enumera os blobs de imagem armazenados no contêiner de blobs de destino (como instâncias **IListBlobItem**) e passa-os para a exibição do aplicativo. Substitua o bloco `foreach` nesse método pelo código a seguir. Esse código chama **CloudBlockBlob.FetchAttributes** para obter os metadados anexados de cada blob. Ele extrai a descrição gerada por computador (`caption`) dos metadados e adiciona-o ao objeto **BlobInfo**, que é passado para a exibição.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>Testar o aplicativo

Salve as alterações no Visual Studio e pressione **Ctrl+F5** para iniciar o aplicativo no navegador. Use o aplicativo para carregar algumas imagens, da pasta "photos" nos recursos do laboratório ou de sua própria pasta. Quando você focalizar o cursor em uma das imagens na exibição, uma janela de dica de ferramenta deverá aparecer e exibir a legenda gerada por computador para a imagem.

![A legenda gerada por computador](../Images/thumbnail-with-tooltip.png)

Para exibir todos os metadados anexados, use o Gerenciador de Armazenamento do Azure para exibir o contêiner de armazenamento que você está usando para imagens. Clique com o botão direito do mouse em um dos blobs do contêiner e selecione **Propriedades**. Na caixa de diálogo, você verá uma lista de pares chave-valor. A descrição da imagem gerada por computador é armazenada no item "Caption", e as palavras-chave de pesquisa são armazenadas em "Tag0", "Tag1" e assim por diante. Quando terminar, clique em **Cancelar** para fechar a caixa de diálogo.

![Janela de caixa de diálogo Propriedades de imagem, com marcas de metadados listadas](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Caso deseje continuar trabalhando em seu aplicativo Web, confira a seção [Próximas etapas](#next-steps). Se você não pretende continuar usando esse aplicativo, exclua todos os recursos específicos do aplicativo. Para excluir recursos, exclua o grupo de recursos que contém sua assinatura do Armazenamento do Azure e o recurso da Pesquisa Visual Computacional. Isso removerá a conta de armazenamento, os blobs carregados nela e o recurso do Serviço de Aplicativo necessário para se conectar ao aplicativo Web ASP.NET. 

Para excluir o grupo de recursos, abra a guia **Grupos de recursos** no portal, navegue até o grupo de recursos usado para este projeto e clique em **Excluir grupo de recursos** na parte superior da exibição. Você deverá digitar o nome do grupo de recursos para confirmar que deseja excluí-lo, porque depois de excluído, um grupo de recursos não poderá ser recuperado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou o serviço de Pesquisa Visual Computacional do Azure em um aplicativo Web existente para gerar automaticamente legendas e palavras-chave para imagens de blob, conforme elas são carregadas. Em seguida, consulte o Laboratório do Armazenamento do Azure, Exercício 6, para saber como adicionar a funcionalidade de pesquisa ao aplicativo Web. Isso aproveita as palavras-chave de pesquisa geradas pelo serviço de Pesquisa Visual Computacional.

> [!div class="nextstepaction"]
> [Adicionar uma pesquisa ao aplicativo](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
