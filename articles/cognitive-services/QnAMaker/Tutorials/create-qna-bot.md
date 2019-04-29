---
title: Bot do QnA – Serviço de Bot do Azure – QnA Maker
titleSuffix: Azure Cognitive Services
description: Crie um chat bot do QnA da página de publicação de uma base de dados de conhecimento existente. Este bot usa o SDK do Bot Framework v4. Você não precisar escrever nenhum código para criar o bot, todo o código é fornecido para você.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.openlocfilehash: 85b0004288a06a834b61f6e3d50017d35d66ce86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599097"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Tutorial: Crie um Bot do QnA com o Azure Bot Service v4

Criar um chat bot do QnA do **publicar** página para uma base de dados de conhecimento existente. Este bot usa o SDK do Bot Framework v4. Você não precisar escrever nenhum código para criar o bot, todo o código é fornecido para você.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um serviço de Bot do Azure de uma base de dados de conhecimento existente
> * Conversar com o bot para verificar se o código está funcionando 

## <a name="prerequisites"></a>Pré-requisitos

Você precisa ter uma base de dados de conhecimento publicada para este tutorial. Se você não tiver uma, siga as etapas em [Create e resposta de KB](create-publish-query-in-portal.md) tutorial para criar uma base de dados de Conhecimento de QnA Maker com perguntas e respostas.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Criar um bot de QnA

Crie um bot como um aplicativo cliente para a base de Conhecimento. 

1. No portal do QnA Maker, vá para o **publicar** página e, em seguida, publique sua base de dados de Conhecimento. Selecione **criação de Bot**. 

    ![No portal do QnA Maker, vá para a página Publicar e publique sua base de dados de conhecimento. Selecione Criar Bot.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Abre o portal do Azure com a configuração de criação de bot.

1.  Insira as configurações para criar o bot:

    |Configuração|Value|Finalidade|
    |--|--|--|
    |Nome do bot|`my-tutorial-kb-bot`|Esse é o nome de recurso do Azure para o bot.|
    |Assinatura|Consulte finalidade.|Selecione a mesma assinatura que você usou para criar os recursos do QnA Maker.|
    |Grupo de recursos|`my-tutorial-rg`|O grupo de recursos usado para todos os relacionados ao bot recursos do Azure.|
    |Local padrão|`west us`|Local do recurso do Azure do bot.|
    |Tipo de preço|`F0`|A camada gratuita para o serviço de bot do Azure.|
    |Nome do aplicativo|`my-tutorial-kb-bot-app`|Isso é um aplicativo web para dar suporte a apenas seu bot. Isso não deve ser o mesmo nome do aplicativo, pois o serviço QnA Maker já está usando. Não há suporte para o compartilhamento de aplicativo de web do QnA Maker com qualquer outro recurso.|
    |Idioma do SDK|C#|Isso é a linguagem de programação subjacente usada pelo bot framework SDK. Suas opções são C# ou Node. js.|
    |Chave de autenticação do QnA|**Não alterar**|Esse valor é preenchido para você.|
    |Local/plano de serviço de aplicativo|**Não alterar**|Para este tutorial, o local não é importante.|
    |Armazenamento do Azure|**Não alterar**|Dados de conversa são armazenados nas tabelas de armazenamento do Azure.|
    |Application Insights|**Não alterar**|Registro em log é enviado ao Application Insights.|
    |ID do Aplicativo da Microsoft|**Não alterar**|Usuário do Active Directory e a senha é necessária.|

    ![Crie o bot de base de conhecimento com essas configurações.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Aguarde alguns minutos até que a notificação de processo de criação de bot será relatada com êxito.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Conversar com o bot

1. No portal do Azure, abra o novo recurso de bot a notificação. 

    ![No portal do Azure, abra o novo recurso de bot a notificação.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. Partir **gerenciamento de Bot**, selecione **teste na Web bate-papo** e insira: `How large can my KB be?`. O bot responderá com: 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Teste o novo bot de base de Conhecimento.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Para obter mais informações sobre os Bots do Azure, consulte [Use o QnA Maker para responder a perguntas](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="related-to-qna-maker-bots"></a>Relacionadas aos bots do QnA Maker

* O bot de Ajuda do QnA Maker, usado no portal do QnA Maker, está disponível como um [exemplo de bot](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot).
    ![Ícone de bot de Ajuda do QnA Maker é robô vermelho](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Serviços de saúde bots](https://docs.microsoft.com/HealthBot/qna_model_howto) use o QnA Maker como um dos seus [modelos de linguagem](https://docs.microsoft.com/HealthBot/qna_model_howto).

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar com o bot deste tutorial, remova-o no portal do Azure. 

Se você criou um novo grupo de recursos para recursos do bot, exclua o grupo de recursos. 

Se você não tiver criado um novo grupo de recursos, você precisa localizar os recursos associados com o bot. A maneira mais fácil é procurar pelo nome do aplicativo bot e bot. Os recursos de bot incluem:

* O plano do Serviço de Aplicativo
* O serviço Search
* O serviços Cognitivos
* O serviço de Aplicativo
* Opcionalmente, também pode incluir o serviço application insights e o armazenamento para os dados do application insights

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conceito: base de dados de conhecimento](../concepts/knowledge-base.md)

## <a name="see-also"></a>Consulte também

- [Gerencie sua base de dados de conhecimento](https://qnamaker.ai)
- [Habilite o bot em canais diferentes](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
