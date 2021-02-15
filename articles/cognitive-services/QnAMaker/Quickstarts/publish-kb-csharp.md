---
title: 'Início rápido: publicar base de dados de conhecimento, REST, C#-QnA Maker'
description: Este início rápido baseado na REST em C# publica a base de dados de conhecimento e cria um ponto de extremidade que pode ser chamado no aplicativo ou no chatbot.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: abaccfb1a1f36e1f7f47d3f5acefb08e2091cb39
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351173"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Início Rápido: publicar uma base de dados de conhecimento no QnA Maker usando C#

Esse início rápido baseado em REST orienta você durante a publicação programática de sua KB (base de dados de conhecimento). A publicação envia por push a versão mais recente da base de dados de conhecimento para um índice dedicado da Azure Cognitive Search e cria um ponto de extremidade que pode ser chamado no aplicativo ou no chatbot.

Este início rápido chama as APIs de QnA Maker:
* [Publicar](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – esta API não requer nenhuma informação no corpo da solicitação.

## <a name="prerequisites"></a>Pré-requisitos

* [**Edição mais recente do Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **Início Rápido** para o recurso no portal do Azure.
* A ID da KB (base de dados de conhecimento) do QnA Maker encontrada na URL no parâmetro de cadeia de caracteres de consulta `kbid`, conforme mostrada abaixo.

    ![ID da base de dados de conhecimento do QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se você não tiver uma base de dados de conhecimento, pode criar uma de amostra para usar para este início rápido: [Criar uma nova base de dados de conhecimento](create-new-kb-csharp.md).

> [!NOTE]
> Os arquivos de solução completos estão disponíveis no repositório do GitHub [ **Azure-Samples/cognitiva-Services-qnamaker-Csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Criar projeto da base de dados de conhecimento

1. Abra o Visual Studio 2019 Community Edition.
1. Crie um novo projeto de **Aplicativo do Console (.NET Core)** e nomeie o projeto `QnaMakerQuickstart`. Aceite os padrões para as configurações restantes.

## <a name="add-required-dependencies"></a>Adicionar as dependências necessárias

No topo do Program.cs, substitua a única instrução using pelas linhas abaixo para adicionar as dependências necessárias ao projeto:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="dependencies":::

## <a name="add-required-constants"></a>Adicionar constantes necessárias

Na classe **Programa**, adicione as constantes necessárias para acessar o QnA Maker.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="constants":::

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>Adicionar o método Principal para publicar a base de dados de conhecimento

Após as constantes necessárias, adicione o código a seguir, o qual faz uma solicitação HTTPS à API de QnA Maker para publicar uma base de dados de conhecimento e recebe a resposta:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="post":::

A chamada à API retorna um status 204 de publicação bem-sucedida sem nenhum conteúdo no corpo da resposta.

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Compile e execute o programa. Ela enviará automaticamente a solicitação para a API de QnA Maker para publicar a base de dados de conhecimento e, em seguida, a resposta será impressa na janela do console.

Após sua base de dados de conhecimento ser publicada, você poderá consultá-la do ponto de extremidade com um aplicativo cliente ou chat bot.

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Após a publicação da base de dados de conhecimento, você precisa da [URL do ponto de extremidade para gerar uma resposta](./get-answer-from-knowledge-base-csharp.md).

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)