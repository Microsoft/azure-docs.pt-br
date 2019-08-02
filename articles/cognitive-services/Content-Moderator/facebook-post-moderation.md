---
title: 'Tutorial: Moderar conteúdo do Facebook – Content Moderator'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você aprenderá a usar o Content Moderator baseado em aprendizado de máquina para ajudar a moderar postagens e comentários do Facebook.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: bd2ed09294ad122b7e8af045f01d3c6f63fcc510
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564939"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Tutorial: Moderar comandos e postagens do Facebook com o Content Moderator do Azure

Neste tutorial, você aprenderá a usar o Content Moderator do Azure para ajudar a moderar postagens e comentários em uma página do Facebook. O Facebook enviará o conteúdo publicado por visitantes ao serviço do Content Moderator. Em seguida, seus fluxos de trabalho do Content Moderator publicam o conteúdo ou criam análises dentro da ferramenta de Análise, dependendo das pontuações e dos limites do conteúdo. Assista ao [Vídeo de demonstração do Build 2017](https://channel9.msdn.com/Events/Build/2017/T6033) para obter um exemplo de funcionamento deste cenário.

Este tutorial mostra como:

> [!div class="checklist"]
> * Criar uma equipe no Content Moderator.
> * Criar Azure Functions que escuta para eventos HTTP do Content Moderator e Facebook.
> * Vincular uma página do Facebook ao Content Moderator usando um aplicativo do Facebook.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este diagrama ilustra cada componente desse cenário:

![Diagrama do Content Moderator recebendo informações do Facebook por meio de "FBListener" e enviando informações por meio de "CMListener"](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> Em 2018, o Facebook implementou uma habilitação mais estrita de aplicativos do Facebook. Você não poderá concluir as etapas deste tutorial caso seu aplicativo não tenha sido examinado e aprovado pela equipe de análise do Facebook.

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura do Content Moderator. Siga as instruções descritas em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o serviço Content Moderator e obter sua chave.
- Uma [conta do Facebook](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Criar uma equipe de análise

Veja o início rápido [Familiarize-se com o Content Moderator](quick-start.md) para obter instruções sobre como se inscrever na [ferramenta de Análise do Content Moderator](https://contentmoderator.cognitive.microsoft.com/) e criar uma equipe de análise. Anote o valor da **ID da Equipe** na página **Credenciais**.

## <a name="configure-image-moderation-workflow"></a>Configurar o fluxo de trabalho de moderação de imagem

Veja o guia [Definir, testar e usar fluxos de trabalho](review-tool-user-guide/workflows.md) para criar um fluxo de trabalho de imagem personalizado. O Content Moderator usará esse fluxo de trabalho para verificar automaticamente imagens no Facebook e enviar algumas para a ferramenta de Análise. Tome nota do **nome** do fluxo de trabalho.

## <a name="configure-text-moderation-workflow"></a>Configurar o fluxo de trabalho de moderação de texto

Mais uma vez, veja o guia [Definir, testar e usar fluxos de trabalho](review-tool-user-guide/workflows.md); desta vez, crie um fluxo de trabalho de texto personalizado. O Content Moderator usará esse fluxo de trabalho para verificar automaticamente o conteúdo do texto. Tome nota do **nome** do fluxo de trabalho.

![Configurar Fluxo de trabalho de Texto](images/text-workflow-configure.PNG)

Teste seu fluxo de trabalho usando o botão **Executar fluxo de trabalho**.

![Testar o Fluxo de trabalho de Texto](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Criar Azure Functions

Entre no [portal do Azure](https://portal.azure.com/) e siga estas etapas:

1. Crie um Aplicativo de funções do Azure, conforme mostrado na página [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
1. Vá para o Aplicativo de funções recém-criado.
1. No aplicativo, acesse a guia **Recursos da plataforma** e selecione **Configuração**. Na seção **Configurações de aplicativo** da página seguinte, selecione **Nova configuração de aplicativo** para adicionar os seguintes pares chave/valor:
    
    | Nome da configuração do aplicativo | value   | 
    | -------------------- |-------------|
    | cm:TeamId   | ID da sua equipe do Content Moderator  | 
    | cm:SubscriptionKey | Sua chave de assinatura Content Moderator - Consulte [Credenciais](review-tool-user-guide/credentials.md) |
    | cm:Região | O nome da região do Content Moderator, sem espaços. |
    | cm:ImageWorkflow | Nome do fluxo de trabalho para executar em Imagens |
    | cm:TextWorkflow | Nome do fluxo de trabalho para executar em Texto |
    | cm:CallbackEndpoint | URL do aplicativo de funções CMListener que você criará mais adiante neste guia |
    | fb:VerificationToken | Um token secreto que você criará, usado para assinar os eventos do feed do Facebook |
    | fb:PageAccessToken | O token de acesso de grafo do Facebook não expira e permite a função Ocultar/Excluir as postagens em seu nome. Você obterá esse token em uma etapa posterior. |

    Clique no botão **Salvar** na parte superior da página.

1. Volte para a guia **Recursos da plataforma**. Use o botão **+** no painel esquerdo para abrir o painel **Nova função**. A função que você está prestes a criar receberá eventos do Facebook.

    ![Painel do Azure Functions com o botão Adicionar Função realçado.](images/new-function.png)

    1. Clique no bloco **Gatilho Http**.
    1. Digite o nome **FBListener**. O campo **Nível de Autorização** deve ser definido como **Função**.
    1. Clique em **Criar**.
    1. Substitua o conteúdo de **run.csx** pelo conteúdo de **FbListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Crie uma nova função de **Gatilho Http** denominada **CMListener**. Esta função recebe eventos do Content Moderator. Substitua o conteúdo de **run.csx** pelo conteúdo do **CMListener/run.csx**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Configure o aplicativo e página do Facebook

1. Crie um aplicativo do Facebook.

    ![Página de desenvolvedor do Facebook](images/facebook-developer-app.png)

    1. Navegue até o [site de desenvolvedor do Facebook](https://developers.facebook.com/)
    1. Clique em **Meus Aplicativos**.
    1. Adicionar um Novo Aplicativo.
    1. Dê um nome a ele
    1. Selecione **Webhooks -> Configurar**
    1. Selecione **Página** no menu suspenso e selecione **Assinar este objeto**
    1. Forneça a **URL FBListener** como a URL de retorno de chamada e o **Verificar Token** configurado em**Configurações do Aplicativo de Funções**
    1. Depois que se inscreveu, vá até o feed e selecione **assinar**.
    1. Clique no botão **Testar** da linha do **feed** para enviar uma mensagem de teste para a Função do Azure FBListener e, em seguida, pressione o botão **Enviar para Meu Servidor**. Você deverá ver a solicitação que está sendo recebida no FBListener.

1. Crie uma página do Facebook.

    > [!IMPORTANT]
    > Em 2018, o Facebook implementou uma habilitação mais estrita de aplicativos do Facebook. Você não poderá executar a seções 2, 3 e 4 caso seu aplicativo não tenha sido examinado e aprovado pela equipe de análise do Facebook.

    1. Navegue até [Facebook](https://www.facebook.com/bookmarks/pages) e crie uma nova **página do Facebook**.
    1. Permita que o aplicativo do Facebook acesse esta página seguindo estas etapas:
        1. Navegue até o [Explorer do API do Graph](https://developers.facebook.com/tools/explorer/).
        1. Selecione **Aplicativo**.
        1. Selecione o **Token de Acesso da Página**, envie uma solicitação **Obter**.
        1. Clique em **ID de Página** na resposta.
        1. Agora acrescente **/subscribed_apps** à URL e Envie uma solicitação **Obter** (resposta vazia).
        1. Envie uma solicitação de **Postagem**. Obtenha a resposta como **sucesso: verdadeiro**.

3. Crie um token de acesso de API do Graph sem expiração.

    1. Navegue até o [Explorer do API do Graph](https://developers.facebook.com/tools/explorer/).
    2. Selecione a opção do **Aplicativo**.
    3. Selecione a opção **Obter Token de Acesso do Usuário**.
    4. Em **Selecionar Permissões**, selecione as opções **manage_pages** e **publish_pages**.
    5. Usaremos o **token de acesso** (Token de Vida Útil Curta) na próxima etapa.

4. Usamos o Portman para as próximas etapas.

    1. Abra o **Postman** (ou obtenha-o [aqui](https://www.getpostman.com/)).
    2. Importe esses dois arquivos:
        1. [Coleção do Postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Ambiente Postman](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Atualize essas variável de ambiente:
    
        | Chave | Valor   | 
        | -------------------- |-------------|
        | appId   | Insira seu Identificador do Aplicativo do Facebook aqui  | 
        | appSecret | Insira seu segredo do aplicativo do Facebook aqui | 
        | short_lived_token | Insira o token de acesso de usuário de vida útil curta gerado na etapa anterior |
    4. Agora execute as 3 APIs listadas na coleção: 
        1. Selecione **Gerar Token de acesso de vida útil longa** e clique em **Enviar**.
        2. Selecione **Obter a ID do usuário** e clique em **Enviar**.
        3. Selecione **Obter Token de acesso da página permanente** e clique em **Enviar**.
    5. Copie o valor **access_token** da resposta e atribua-a à configuração do aplicativo, **fb:PageAccessToken**.

A solução envia todas as imagens e texto publicados na sua página do Facebook para o Content Moderator. Os fluxos de trabalho que você configurou anteriormente serão invocados. O conteúdo que não é aprovado segundo os critérios definidos nos fluxos de trabalho é transmitido para as análises dentro da ferramenta de análise. O restante do conteúdo é publicado automaticamente.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurará um programa para analisar as imagens de produto com a finalidade de marcá-las por tipo de produto e permitir que uma equipe de revisão tome decisões informadas sobre a moderação de conteúdo. Em seguida, saiba mais sobre os detalhes da moderação de imagem.

> [!div class="nextstepaction"]
> [Moderação de imagem](./image-moderation-api.md)
