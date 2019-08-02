---
title: Ativar várias conversas
titleSuffix: Azure Cognitive Services
description: Usar prompts e contexto para gerenciar o vários ativa, conhecido como várias por sua vez, para o bot de uma pergunta para outro. Vários por sua vez é a capacidade de ter uma conversa back bidirecional em que o contexto da pergunta anterior influencia a próxima pergunta e resposta.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 10249375922b47a40f71a60938cdd12ffe0f9b54
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508146"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Prompts de acompanhamento de uso para criar várias sequências de uma conversa

Usar prompts de acompanhamento e o contexto para gerenciar o vários ativa, conhecido como _ativar várias_, para o bot de uma pergunta para outro.

Para ver como funciona com vários por sua vez, exiba o vídeo de demonstração a seguir:

[![Ativar a conversa do QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>O que é uma conversa com vários por sua vez?

Algumas perguntas não podem ser respondidas em uma única rodada. Quando você projeta suas conversas de (bot de bate-papo) do aplicativo cliente, um usuário pode fazer uma pergunta que precisa ser filtrada ou refinados para determinar a resposta correta. Você possibilitam esse fluxo pelas perguntas, apresentando o usuário com *acompanhamento solicita*.

Quando um usuário faz uma pergunta, o QnA Maker retorna a resposta _e_ quaisquer prompts de acompanhamento. Essa resposta permite que você apresente as perguntas de acompanhamento como opções. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Conversa de turno vários de exemplo com o bot de bate-papo

Com vários por sua vez, um chat bot gerencia uma conversa com um usuário para determinar a resposta final, conforme mostrado na imagem a seguir:

![Uma caixa de diálogo de ativar vários prompts que orientam a um usuário por meio de uma conversa](../media/conversational-context/conversation-in-bot.png)

Na imagem anterior, um usuário iniciou uma conversa, inserindo **minha conta**. A base de Conhecimento tem três pares de perguntas e respostas vinculados. Para refinar a resposta, o usuário seleciona uma das três opções na base de Conhecimento. A pergunta (n º 1), tem três avisos de acompanhamento, que são apresentados no bot bate-papo como três opções (2). 

Quando o usuário seleciona uma opção (3), a lista próxima de refinamento opções (4) é apresentada. Essa sequência continua (5) até que o usuário determina a resposta correta, final (6).

> [!NOTE]
> Na imagem anterior, o **Ativar Ativar várias** caixa de seleção foi marcada para garantir que os prompts são exibidos. 

### <a name="use-multi-turn-in-a-bot"></a>Usar vários por sua vez em um bot

Para gerenciar a conversa contextual, altere seu aplicativo cliente pelo [adicionando código ao seu bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting). Adicionando o código permite que os usuários vejam os avisos.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Criar uma conversa com vários por sua vez da estrutura de um documento

Quando você cria uma base de dados de Conhecimento, o **popular seu KB** seção exibe uma **habilitar ativar a extração de URLs, os arquivos. PDF ou. docx** caixa de seleção. 

![Caixa de seleção para habilitar a extração de folheio de vários](../media/conversational-context/enable-multi-turn.png)

Quando você seleciona essa opção para um documento importado, a conversa com vários por sua vez pode ser deduzida da estrutura do documento. Se essa estrutura, o QnA Maker criará o prompt de acompanhamento que pares perguntas e respostas de como parte do processo de importação. 

Estrutura de folheio de vários pode ser inferida somente de URLs, PDF, arquivos ou arquivos DOCX. Para obter um exemplo de estrutura, exibir uma imagem de um [arquivo manual de PDF do Microsoft Surface usuário](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf). Devido ao tamanho do arquivo PDF, o recurso do QnA Maker requer uma **pesquisa de tipo de preço** dos **B** (15 índices) ou maior. 

![! [Exemplo de estrutura em um manual do usuário] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Quando você importa o documento PDF, QnA Maker determina acompanhamento solicita da estrutura para criar o fluxo de conversação. 

1. No QnA Maker, selecione **criar uma base de dados de Conhecimento**.
1. Crie ou use um serviço existente do QnA Maker. No exemplo anterior do Microsoft Surface, porque o arquivo PDF é muito grande para uma camada menor, use um serviço QnA Maker com um **serviço de pesquisa** dos **B** (15 índices) ou maior.
1. Insira um nome para sua base de dados de Conhecimento, como **superfície manual**.
1. Selecione o **habilitar ativar a extração de URLs, os arquivos. PDF ou. docx** caixa de seleção. 
1. Selecione a URL manual superfície, **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** .

1. Selecione o **criar seu KB** botão. 

    Depois que a base de dados de Conhecimento é criada, é mostrada uma exibição dos pares de perguntas e respostas.

## <a name="show-questions-and-answers-with-context"></a>Mostrar perguntas e respostas com contexto

Reduza os pares de perguntas e respostas exibidos para somente aqueles com conversas contextuais. 

Selecione **as opções de exibição**e, em seguida, selecione **Mostrar contexto (visualização)** . A lista exibe pares de perguntas e respostas que contêm prompts de acompanhamento. 

![Filtrar pares de perguntas e respostas por conversas contextuais](../media/conversational-context/filter-question-and-answers-by-context.png)

O contexto de vários por sua vez é exibido na primeira coluna.

![! [A coluna de "contexto (visualização)"] (.. / media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Na imagem anterior, **#1** indica o texto em negrito na coluna, o que significa a pergunta atual. A pergunta pai é o item superior na linha. Quaisquer perguntas abaixo dele são os pares de perguntas e respostas vinculados. Esses itens são selecionáveis, para que você pode ir imediatamente para os outros itens de contexto. 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Adicionar um par de pergunta e resposta existente como um prompt de acompanhamento

A pergunta original **minha conta**, tem prompts de acompanhamento, como **contas e entrar no**. 

![A "Contas e entrar no" respostas e solicitações de acompanhamento](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Adicione um prompt de acompanhamento para um par de pergunta e resposta existente que atualmente não está vinculado. Porque a questão não está vinculada a qualquer par de pergunta e resposta, a configuração do modo de exibição atual precisa ser alterado.

1. Para vincular um par de pergunta e resposta existente como um prompt de acompanhamento, selecione a linha para o par de perguntas e respostas. Para a superfície manual, pesquise **sair** para reduzir a lista.
1. Na linha **Signout**, no **resposta** coluna, selecione **prompt de acompanhamento de adicionar**.
1. Nos campos de **prompt de acompanhamento (versão prévia)** janela pop-up, insira os seguintes valores:

    |Campo|Value|
    |--|--|
    |Exibir texto|Insira **desativar o dispositivo**. Isso é texto personalizado a ser exibido no prompt de acompanhamento.|
    |Somente contexto| Marque esta caixa de seleção. Uma resposta é retornada somente se a pergunta Especifica o contexto.|
    |Link para responder|Insira **usar a tela de entrada** para localizar o par de pergunta e resposta existente.|


1.  Uma correspondência é retornada. Selecione esta resposta como o acompanhamento e, em seguida, selecione **salvar**. 

    ![A página de ""acompanhamento prompt (visualização)](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Depois de adicionar o prompt de acompanhamento, selecione **salvar e treinar** no painel de navegação superior.
  
### <a name="edit-the-display-text"></a>Editar o texto de exibição 

Quando um prompt de acompanhamento é criado e um par de pergunta e resposta existente é inserido como o **Link para a resposta**, você pode inserir novos **exibir texto**. Esse texto não substitui a pergunta existente, e ele não adiciona uma nova pergunta alternativa. Ele é separado desses valores. 

1. Para editar o texto de exibição, pesquise e selecione a pergunta a **contexto** campo.
1. Na linha para essa pergunta, selecione o prompt de acompanhamento na coluna de resposta. 
1. Selecione o texto de exibição que você deseja editar e, em seguida, selecione **editar**.

    ![O comando de edição do texto de exibição](../media/conversational-context/edit-existing-display-text.png)

1. No **prompt de acompanhamento** janela pop-up, alterar o texto de exibição existente. 
1. Quando você terminar a edição do texto de exibição, selecione **salvar**. 
1. Na barra de navegação superior, **salvar e treinar**.


<!--

## To find the best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a specific question-and-answer pair but you know, as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base. You can then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, and then the follow-ups are returned.

1. Add metadata to each of the two follow-up question-and-answer pairs:

    |Question|Add metadata|
    |--|--|
    |*Feedback on a QnA Maker service*|"Feature":"all"|
    |*Feedback on an existing feature*|"Feature":"one"|
    
    ![The "Metadata tags" column for adding metadata to a follow-up prompt](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Select **Save and train**. 

    When you send the question **Give feedback** with the metadata filter **Feature** with a value of **all**, only the question-and-answer pair with that metadata is returned. QnA Maker doesn't return both question-and-answer pairs, because both don't match the filter. 

-->

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Adicionar um novo par de pergunta e resposta como um prompt de acompanhamento

Quando você adiciona um novo par de pergunta e resposta para a base de dados de Conhecimento, cada par deve ser vinculada a uma pergunta existente como um prompt de acompanhamento.

1. Na barra de ferramentas da base de Conhecimento, procure e selecione o par de pergunta e resposta existente para **contas e entrar no**. 

1. No **resposta** coluna para essa pergunta, selecione **prompt de acompanhamento de adicionar**. 
1. Sob **prompt de acompanhamento (versão prévia)** , crie um novo prompt de acompanhamento, inserindo os seguintes valores: 

    |Campo|Value|
    |--|--|
    |Exibir texto|*Criar uma conta do Windows*. O texto personalizado para exibir no prompt de acompanhamento.|
    |Somente contexto|Marque esta caixa de seleção. Essa resposta é retornada somente se a pergunta Especifica o contexto.|
    |Link para responder|Insira o texto a seguir como a resposta:<br>*[Crie](https://account.microsoft.com/) uma conta do Windows com uma conta de email do novo ou existente*.<br>Quando você salva e treinar o banco de dados, esse texto será convertido. |
    |||

    ![Criar uma nova solicitação pergunta e resposta](../media/conversational-context/create-child-prompt-from-parent.png)


1. Selecione **criar novo**e, em seguida, selecione **salvar**. 

    Essa ação cria um novo par de perguntas e respostas e os links a pergunta selecionada como um prompt de acompanhamento. O **contexto** coluna, ambas as perguntas, indica uma relação de prompt de acompanhamento. 

1. Selecione **as opções de exibição**e, em seguida, selecione [ **Mostrar contexto (visualização)** ](#show-questions-and-answers-with-context).

    A nova pergunta mostra como ele está vinculado.

    ![Criar um novo prompt de acompanhamento](../media/conversational-context/new-qna-follow-up-prompt.png)

    A pergunta pai exibe uma nova pergunta como uma das suas opções.

    ![! [A coluna de contexto, ambas as perguntas, indica uma relação de prompt acompanhamento] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Depois de adicionar o prompt de acompanhamento, selecione **salvar e treinar** na barra de navegação superior.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Ativar várias ativar durante o teste dos prompts de acompanhamento

Quando você testa a pergunta com acompanhamento prompts na **testar** painel, selecione **Ativar Ativar várias**e, em seguida, digite sua pergunta. A resposta inclui os prompts de acompanhamento.

![A resposta inclui os prompts de acompanhamento](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Se você não habilitar vários por sua vez, a resposta é retornada, mas prompts de acompanhamento não são retornadas.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Uma solicitação JSON para retornar uma resposta inicial e as solicitações de acompanhamento

Use a esvaziar `context` objeto para solicitar a resposta à pergunta do usuário e incluir prompts de acompanhamento. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Uma resposta JSON para retornar uma resposta inicial e as solicitações de acompanhamento

A seção anterior solicitou uma resposta e avisos para acompanhamento **contas e entrar no**. A resposta inclui as informações de aviso, que estão localizadas em *respostas [0] .context*e o texto a ser exibido ao usuário. 

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

O `prompts` matriz fornece o texto em de `displayText` propriedade e o `qnaId` valor. Você pode mostrar essas respostas, como as opções exibidas Avançar na conversa fluam e, em seguida, enviar selecionado `qnaId` volta para o QnA Maker na solicitação a seguir. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Uma solicitação JSON para retornar uma resposta não inicial e as solicitações de acompanhamento

Preencher o `context` objeto para incluir o contexto anterior.

Na solicitação JSON a seguir, a pergunta atual é *usar Windows Hello para entrar no* e a pergunta anterior era *contas e entrar no*. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
``` 

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Uma resposta JSON para retornar uma resposta não inicial e as solicitações de acompanhamento

O QnA Maker _GenerateAnswer_ resposta JSON inclui os prompts de acompanhamento na `context` propriedade do primeiro item no `answers` objeto:

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Consultar a base de dados de conhecimento com a ID do QnA Maker

Na resposta da pergunta inicial, os prompts de acompanhamento e seus associados `qnaId` é retornado. Agora que você tem a ID, você pode passar isso no corpo de solicitação do prompt de acompanhamento. Se o corpo da solicitação contém o `qnaId`e o objeto de contexto (que contém as propriedades do QnA Maker anteriores), então GenerateAnswer retornará a pergunta exata por ID, em vez de usar o algoritmo de classificação para encontrar a resposta, o texto da pergunta. 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>Exibir avisos e enviar o contexto no aplicativo cliente 

Você adicionou prompts em sua base de dados de conhecimento e testado o fluxo no painel de teste. Agora, você precisa usar esses prompts no aplicativo cliente. Para o Bot Framework, os prompts não são automaticamente exibidos nos aplicativos cliente. Você pode exibir os avisos como botões ou ações sugeridas como parte da resposta à consulta do usuário em aplicativos cliente, incluindo isso [exemplo de estrutura do Bot](https://aka.ms/qnamakermultiturnsample) em seu código. O aplicativo cliente deverá armazenar a ID do QnA Maker atual e a consulta de usuário e transmiti-los a [objeto de contexto da API GenerateAnswer](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts) para a próxima consulta de usuário. 

## <a name="display-order-is-supported-in-the-update-api"></a>Ordem de exibição tem suporte na API de atualização

O [exibir o texto e a ordem de exibição](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), retornado na resposta JSON, há suporte para edição pela [Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Criar a base de conhecimento com ativar vários prompts com a API de criação

Você pode criar um caso de dados de conhecimento com prompts de turno multi usando o [criar API do QnA Maker](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Adicionando os prompts na `context` da propriedade `prompts` matriz. 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Adicionar ou excluir ativar vários prompts com a API de atualização

Você pode adicionar ou excluir os prompts de turno multi usando o [API do QnA Maker atualização](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Adicionando os prompts na `context` da propriedade `promptsToAdd` matriz e o `promptsToDelete` matriz. 


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre conversas contextuais deste [exemplo de caixa de diálogo](https://aka.ms/qnamakermultiturnsample) ou Saiba mais sobre [bot conceitual de design para ativar várias conversas](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrar uma base de Conhecimento](../Tutorials/migrate-knowledge-base.md)
