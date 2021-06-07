---
title: Conversas com vários desligamentos-QnA Maker
description: Use prompts e contexto para gerenciar as várias ativações, conhecidas como Multiturn, para o bot de uma pergunta para outra. A passagem múltipla é a capacidade de ter uma conversa de frente e para trás, na qual o contexto da pergunta anterior influencia a próxima pergunta e resposta.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 313d1a390c30e7e5612b8d9bab7783b6698c35fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618484"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Usar avisos de acompanhamento para criar várias rodadas de uma conversa

Use os prompts e o contexto de acompanhamento para gerenciar os vários ativadores, conhecidos como _Multiturn_, para o bot de uma pergunta para outra.

Para ver como funciona a opção múltipla, veja o vídeo de demonstração a seguir:

[![Conversa de troca múltipla no QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>O que é uma conversa com vários desligamentos?

Algumas perguntas não podem ser respondidas em uma única vez. Quando você projeta suas conversas de aplicativo cliente (bot de bate-papo), um usuário pode fazer uma pergunta que precisa ser filtrada ou refinada para determinar a resposta correta. Você pode fazer esse fluxo seguindo as perguntas possíveis apresentando o usuário com *avisos de acompanhamento*.

Quando um usuário faz uma pergunta, QnA Maker retorna a resposta _e_ quaisquer avisos de acompanhamento. Essa resposta permite que você apresente as perguntas de acompanhamento como opções.

> [!CAUTION]
> As solicitações de troca múltipla não são extraídas dos documentos de perguntas frequentes. Se precisar de extração múltipla, remova os pontos de interrogação que designam os pares QnA como perguntas frequentes.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Exemplo de conversa múltipla com bot de chat

Com o multi-Transform, um bot de chat gerencia uma conversa com um usuário para determinar a resposta final, conforme mostrado na imagem a seguir:

![Uma caixa de diálogo de várias desligamentos com prompts que orientam um usuário por meio de uma conversa](../media/conversational-context/conversation-in-bot.png)

Na imagem anterior, um usuário iniciou uma conversa inserindo **minha conta**. A base de dados de conhecimento tem três pares de perguntas e respostas vinculados. Para refinar a resposta, o usuário seleciona uma das três opções na base de dados de conhecimento. A pergunta (#1), tem três prompts de acompanhamento, que são apresentados no bot de bate-papo como três opções (#2).

Quando o usuário seleciona uma opção (#3), a próxima lista de opções de refinamento (#4) é apresentada. Essa sequência continua (#5) até que o usuário determine a resposta final correta (#6).

### <a name="use-multi-turn-in-a-bot"></a>Usar o multi-Transform em um bot

Depois de publicar sua KB, você pode selecionar o botão **criar bot** para implantar seu bot de QnA Maker no serviço de bot do Azure. Os prompts aparecerão nos clientes de bate-papo que você habilitou para o bot.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Criar uma conversa de passagem múltipla da estrutura do documento

Quando você cria uma base de dados de conhecimento, a seção **popular a KB** exibe uma caixa **de seleção Habilitar extração múltipla de URLs, arquivos. pdf ou. docx** .

![Caixa de seleção para habilitar a extração de vários desligamentos](../media/conversational-context/enable-multi-turn.png)

Quando você seleciona essa opção, QnA Maker extrai a hierarquia presente na estrutura do documento. A hierarquia é convertida em para acompanhamento de prompts e a raiz da hierarquia serve como o QnA pai. Em alguns documentos, a raiz da hierarquia não tem conteúdo que possa servir como resposta, você pode fornecer o ' texto de resposta padrão ' para ser usado como um texto de resposta substituto para extrair tais hierarquias.

A estrutura de vários folheios pode ser inferida apenas de URLs, arquivos PDF ou arquivos DOCX. Para obter um exemplo de estrutura, exiba uma imagem de um [arquivo PDF manual do usuário do Microsoft Surface](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf).

:::image type="content" source="../media/conversational-context/import-file-with-conversational-structure.png" alt-text="Captura de tela mostra um exemplo da estrutura em um manual do usuário." lightbox="../media/conversational-context/import-file-with-conversational-structure.png":::

### <a name="building-your-own-multi-turn-document"></a>Criando seu próprio documento de várias transformações

Se você estiver criando um documento com vários folheios, tenha em mente as seguintes diretrizes:

* Use títulos e subtítulos para denotar hierarquia. Por exemplo, você pode indicar que o pai QnA e H2 denotam o QnA que deve ser levado como prompt. Use pequeno tamanho de título para denotar a hierarquia subsequente. Não use estilo, cor ou algum outro mecanismo para implicar a estrutura em seu documento, QnA Maker não extrairá as solicitações de múltipla ativação.

* O primeiro caractere de título deve estar em letras maiúsculas.

* Não termine um cabeçalho com um ponto de interrogação, `?` .

* Você pode usar o [documento de exemplo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) como um exemplo para criar seu próprio documento com vários folheios.

### <a name="adding-files-to-a-multi-turn-kb"></a>Adicionando arquivos a um KB de várias transformações

Quando você adiciona um documento hierárquico, QnA Maker determina os prompts de acompanhamento da estrutura para criar o fluxo de conversação.

1. Em QnA Maker, selecione uma base de dados de conhecimento existente que foi criada com **habilitar extração múltipla de URLs, arquivos. pdf ou. docx.** habilitado.
1. Vá para a página **configurações** , selecione o arquivo ou a URL a ser adicionada.
1. **Salve e treine** a base de dados de conhecimento.

> [!Caution]
> Não há suporte para o suporte para o uso de um arquivo base de conhecimento multiturns TSV ou XLS exportado como fonte de dados para uma base de conhecimento nova ou vazia. Você precisa **importar** esse tipo de arquivo, na página **configurações** do portal de QnA Maker, para adicionar prompts de multiativação exportados a uma base de dados de conhecimento.

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Criar uma base de dados de conhecimento com prompts de múltipla ativação com a API de criação

Você pode criar um caso de conhecimento com prompts de múltipla ativação usando o [QnA Maker criar API](/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Os prompts estão sendo adicionados na `context` matriz da propriedade `prompts` .

## <a name="show-questions-and-answers-with-context"></a>Mostrar perguntas e respostas com contexto

Reduza os pares de perguntas e respostas exibidos para apenas aqueles com conversas contextuais.

Selecione **Opções de exibição** e, em seguida, selecione **Mostrar contexto**. A lista exibe os pares de perguntas e respostas que contêm avisos de acompanhamento.

![Filtrar pares de perguntas e respostas por conversas contextuais](../media/conversational-context/filter-question-and-answers-by-context.png)

O contexto de múltipla ativação é exibido na primeira coluna.

:::image type="content" source="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png" alt-text="Captura de tela mostra a seção de contexto realçada." lightbox="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png":::

Na imagem anterior, **#1** indica texto em negrito na coluna, que significa a pergunta atual. A pergunta pai é o item superior na linha. As perguntas abaixo são os pares de perguntas e respostas vinculadas. Esses itens são selecionáveis, para que você possa ir imediatamente para os outros itens de contexto.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Adicionar um par de perguntas e respostas existente como um aviso de acompanhamento

A pergunta original, **minha conta**, tem avisos de acompanhamento, como **contas e entrada**.

![As respostas de "contas e entrada" e as solicitações de acompanhamento](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Adicione um aviso de acompanhamento a um par de perguntas e respostas existentes que não esteja vinculado no momento. Como a pergunta não está vinculada a nenhum par de perguntas e respostas, a configuração de exibição atual precisa ser alterada.

1. Para vincular um par de perguntas e respostas existente como um aviso de acompanhamento, selecione a linha para o par de perguntas e respostas. Para o manual da superfície, procure **sair** para reduzir a lista.
1. Na linha de **saída**, na coluna **resposta** , selecione **Adicionar aviso de acompanhamento**.
1. Nos campos na janela pop-up de **aviso de acompanhamento** , insira os seguintes valores:

    |Campo|Valor|
    |--|--|
    |Exibir texto|Digite desligar **o dispositivo**. Esse é o texto personalizado a ser exibido no prompt de acompanhamento.|
    |Somente contexto| Marcar essa caixa de seleção. Uma resposta será retornada somente se a pergunta especificar o contexto.|
    |Link para responder|Digite **usar a tela de entrada** para localizar o par de perguntas e respostas existente.|

1.  Uma correspondência é retornada. Selecione essa resposta como o acompanhamento e, em seguida, selecione **salvar**.

    ![A página "aviso de acompanhamento (visualização)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Depois de adicionar o prompt de acompanhamento, selecione **salvar e treinar** na navegação superior.

### <a name="edit-the-display-text"></a>Editar o texto de exibição

Quando um prompt de acompanhamento é criado e um par de perguntas e respostas existente é inserido como o **link para responder**, você pode inserir o novo texto de **exibição**. Esse texto não substitui a pergunta existente e não adiciona uma nova pergunta alternativa. Ele é separado desses valores.

1. Para editar o texto de exibição, procure e selecione a pergunta no campo **contexto** .
1. Na linha dessa pergunta, selecione o prompt de acompanhamento na coluna resposta.
1. Selecione o texto de exibição que você deseja editar e, em seguida, selecione **Editar**.

    ![O comando Editar para o texto de exibição](../media/conversational-context/edit-existing-display-text.png)

1. Na janela pop-up de **aviso de acompanhamento** , altere o texto de exibição existente.
1. Quando terminar de editar o texto de exibição, selecione **salvar**.
1. Na barra de navegação superior, **salve e treine**.

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Adicionar um novo par de perguntas e respostas como um aviso de acompanhamento

Quando você adiciona um novo par de perguntas e respostas à base de dados de conhecimento, cada par deve ser vinculado a uma pergunta existente como um aviso de acompanhamento.

1. Na barra de ferramentas da base de dados de conhecimento, procure e selecione o par pergunta-e-resposta existente para **contas e entrada**.

1. Na coluna **resposta** dessa pergunta, selecione **Adicionar aviso de acompanhamento**.
1. Em **aviso de acompanhamento (versão prévia)**, crie um novo prompt de acompanhamento inserindo os seguintes valores:

    |Campo|Valor|
    |--|--|
    |Exibir texto|*Crie uma conta do Windows*. O texto personalizado a ser exibido no aviso de acompanhamento.|
    |Somente contexto|Marcar essa caixa de seleção. Essa resposta será retornada somente se a pergunta especificar o contexto.|
    |Link para responder|Digite o seguinte texto como a resposta:<br>*[Crie](https://account.microsoft.com/) uma conta do Windows com uma conta de email nova ou existente*.<br>Quando você salvar e treinar o banco de dados, esse texto será convertido. |
    |||

    ![Criar uma nova pergunta e resposta de prompt](../media/conversational-context/create-child-prompt-from-parent.png)

1. Selecione **criar novo** e, em seguida, selecione **salvar**.

    Essa ação cria um novo par de perguntas e respostas e vincula a pergunta selecionada como um aviso de acompanhamento. A coluna de **contexto** , para as duas perguntas, indica uma relação de aviso de acompanhamento.

1. Selecione **Opções de exibição** e, em seguida, selecione [**Mostrar contexto (versão prévia)**](#show-questions-and-answers-with-context).

    A nova pergunta mostra como ele está vinculado.

    ![Criar um novo aviso de acompanhamento](../media/conversational-context/new-qna-follow-up-prompt.png)

    A pergunta pai exibe uma nova pergunta como uma de suas escolhas.

    :::image type="content" source="../media/conversational-context/child-prompt-created.png" alt-text="Captura de tela mostra a coluna de contexto, para ambas as perguntas, indica uma relação de acompanhamento de aviso." lightbox="../media/conversational-context/child-prompt-created.png":::

1. Depois de adicionar o prompt de acompanhamento, selecione **salvar e treinar** na barra de navegação superior.

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>Exibir várias transformações durante o teste de avisos de acompanhamento

Quando você testa a pergunta com os prompts de acompanhamento no painel de **teste** , a resposta inclui os prompts de acompanhamento.

![A resposta inclui os prompts de acompanhamento](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Uma solicitação JSON para retornar uma resposta inicial e prompts de acompanhamento

Use o `context` objeto vazio para solicitar a resposta à pergunta do usuário e incluir avisos de acompanhamento.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Uma resposta JSON para retornar uma resposta inicial e prompts de acompanhamento

A seção anterior solicitou uma resposta e quaisquer avisos de acompanhamento para **contas e logon**. A resposta inclui as informações do prompt, que estão localizadas em *respostas [0]. contexto* e o texto a ser exibido para o usuário.

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
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n  Go to Start, and right-click your name. Then select Sign out. ",
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

A `prompts` matriz fornece texto na `displayText` propriedade e o `qnaId` valor. Você pode mostrar essas respostas como as próximas opções exibidas no fluxo de conversa e, em seguida, enviar o `qnaId` back selecionado para QnA Maker na solicitação a seguir.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Uma solicitação JSON para retornar uma resposta não inicial e prompts de acompanhamento

Preencha o `context` objeto para incluir o contexto anterior.

Na solicitação JSON a seguir, a pergunta atual é *usar o Windows Hello para entrar* e a pergunta anterior era *contas e entrando*.

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Uma resposta JSON para retornar uma resposta não inicial e prompts de acompanhamento

A resposta JSON do QnA Maker _GenerateAnswer_ inclui os prompts de acompanhamento na `context` Propriedade do primeiro item no `answers` objeto:

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
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Consultar a base de dados de conhecimento com a ID de QnA Maker

Se você estiver criando um aplicativo personalizado usando o recurso de troca múltipla. Na resposta da pergunta inicial, qualquer prompt de acompanhamento e seu associado `qnaId` são retornados. Agora que você tem a ID, é possível passá-la no corpo da solicitação do prompt de acompanhamento. Se o corpo da solicitação contiver o `qnaId` , e o objeto de contexto (que contém as propriedades de QnA Maker anteriores), GenerateAnswer retornará a pergunta exata por ID, em vez de usar o algoritmo de classificação para localizar a resposta pelo texto da pergunta.

## <a name="display-order-is-supported-in-the-update-api"></a>A ordem de exibição tem suporte na API de atualização

O [texto de exibição e a ordem de exibição](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), retornados na resposta JSON, têm suporte para edição pela [API de atualização](/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Adicionar ou excluir prompts de várias ativações com a API de atualização

Você pode adicionar ou excluir prompts de várias ativações usando a [API de atualização de QnA Maker](/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Os prompts estão adicionando na `context` matriz da propriedade `promptsToAdd` e na `promptsToDelete` matriz.

## <a name="export-knowledge-base-for-version-control"></a>Exportar base de dados de conhecimento para controle de versão

O QnA Maker dá suporte ao controle de versão, incluindo etapas de conversa de passagem múltipla no arquivo exportado.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre conversas contextuais neste [exemplo de caixa de diálogo](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/adaptive-dialog/07.qnamaker/QnAMaker.csproj) ou saiba mais sobre [design de bot conceitual para conversas com vários folheios](/azure/bot-service/bot-builder-conversations).

> [!div class="nextstepaction"]
> [Migrar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)