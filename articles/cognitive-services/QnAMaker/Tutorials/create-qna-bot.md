---
title: Bot do QnA – Serviço de Bot do Azure – QnA Maker
titleSuffix: Azure Cognitive Services
description: Crie um chatbot do QnA a partir da página Publicar para uma base de conhecimento existente. Este bot usa o Bot Framework SDK v4. Não é preciso escrever qualquer código para construir o bot, todo o código é fornecido para você.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: 4bb987a5a091871bec2c0cc8cec6d9ab804bb244
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698001"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Tutorial: Criar um Bot do QnA com o Serviço de Bot do Azure v4

Crie um chatbot do QnA a partir da página **Publicar** para uma base de conhecimento existente. Este bot usa o Bot Framework SDK v4. Não é preciso escrever qualquer código para construir o bot, todo o código é fornecido para você.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um Serviço de Bot do Azure a partir de uma base de conhecimento existente
> * Conversar com o bot para verificar se o código está funcionando 

## <a name="prerequisites"></a>Pré-requisitos

Você precisa ter uma base de dados de conhecimento publicada para este tutorial. Se você não tiver uma, siga as etapas no tutorial [Criar e responder a partir de KB](create-publish-query-in-portal.md) para criar uma base de conhecimento do QnA Maker com perguntas e respostas.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Criar um bot de QnA

Crie um bot como um aplicativo cliente para a base de conhecimento. 

1. No portal do QnA Maker, vá para a página **Publicar** e publique sua base de dados de conhecimento. Selecione **Criar Bot**. 

    ![No portal do QnA Maker, vá para a página Publicar e publique sua base de dados de conhecimento. Selecione Criar Bot.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    O portal do Azure é aberto com a configuração de criação do bot.

1.  Insira as configurações para criar o bot:

    |Configuração|Valor|Finalidade|
    |--|--|--|
    |Nome do bot|`my-tutorial-kb-bot`|Esse é o nome do recurso do Azure para o bot.|
    |Subscription|Confira a finalidade.|Selecione a mesma assinatura que você usou para criar os recursos do QnA Maker.|
    |Resource group|`my-tutorial-rg`|O grupo de recursos usado para todos os recursos do Azure relacionados ao bot.|
    |Location|`west us`|O local do recurso do Azure do bot.|
    |Tipo de preço|`F0`|A camada de serviço gratuita para o serviço de bot do Azure.|
    |Nome do aplicativo|`my-tutorial-kb-bot-app`|Este é um aplicativo da Web para oferecer suporte apenas ao seu bot. Não deve ser o mesmo nome do aplicativo que o seu serviço do QnA Maker já está usando. Não há suporte para o compartilhamento do aplicativo da Web do QnA Maker com qualquer outro recurso.|
    |Linguagem do SDK|C#|Esta é a linguagem de programação subjacente usada pelo bot framework SDK. Suas opções são [C#](https://github.com/Microsoft/botbuilder-dotnet) ou [Node. js](https://github.com/Microsoft/botbuilder-js).|
    |Chave de autenticação do QnA|**Não alterar**|Esse valor é preenchido para você.|
    |Local/plano de serviço de aplicativo|**Não alterar**|Para este tutorial, a localização não é importante.|
    |Armazenamento do Azure|**Não alterar**|Os dados de conversa são armazenados nas tabelas do Armazenamento do Azure.|
    |Application Insights|**Não alterar**|O registro em log é enviado para o Application Insights.|
    |ID do Aplicativo da Microsoft|**Não alterar**|O usuário e a senha do Active Directory são obrigatórios.|

    ![Crie o bot de base de conhecimento com essas configurações.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Aguarde alguns minutos até que a notificação do processo de criação do bot relate sucesso.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Conversar com o bot

1. No portal do Azure, abra o novo recurso de bot a partir da notificação. 

    ![No portal do Azure, abra o novo recurso de bot a partir da notificação.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. Em **Gerenciamento de bot**, selecione **Testar no Webchat** e digite: `How large can my KB be?`. O bot responderá com: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![testar o novo bot de base de conhecimento.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Para saber mais sobre os bots do Azure, confira [Usar o QnA Maker para responder perguntas](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar com o bot deste tutorial, remova-o no portal do Azure. 

Se você criou um novo grupo de recursos para os recursos do bot, exclua o grupo de recursos. 

Se você não criou um novo grupo de recursos, precisará encontrar os recursos associados ao bot. A maneira mais fácil é pesquisar pelo nome do bot e do aplicativo do bot. Os recursos de bot incluem:

* O plano do Serviço de Aplicativo
* O serviço Search
* O serviços Cognitivos
* O serviço de Aplicativo
* Opcionalmente, também pode incluir o serviço application insights e o armazenamento para os dados do application insights


## <a name="related-to-qna-maker-bots"></a>Bots relacionados do QnA Maker

* O bot de ajuda do QnA Maker, usado no portal QnA Maker, está disponível como uma [amostra de bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support).
    ![O ícone de bot de ajuda do QnA Maker é um robô vermelho](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* Os [bots de assistência médica](https://docs.microsoft.com/HealthBot/qna_model_howto) usam o QnA Maker como um de seus [modelos de linguagem](https://docs.microsoft.com/HealthBot/qna_model_howto).


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conceito: base de dados de conhecimento](../concepts/knowledge-base.md)

## <a name="see-also"></a>Consulte também

- [Gerencie sua base de dados de conhecimento](https://qnamaker.ai)
- [Habilite o bot em canais diferentes](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
