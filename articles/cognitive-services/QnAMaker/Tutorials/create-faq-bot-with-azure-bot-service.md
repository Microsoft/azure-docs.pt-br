---
title: 'Tutorial: criar um bot de perguntas frequentes com o Serviço de Bot do Azure'
description: Neste tutorial, crie um bot de perguntas frequentes sem código com o QnA Maker e o Serviço de Bot do Azure.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 08/31/2020
ms.openlocfilehash: ab6607175c596a0d82cf75f0ad786a76e85b6959
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612143"
---
# <a name="tutorial-create-an-faq-bot-with-azure-bot-service"></a>Tutorial: criar um bot de perguntas frequentes com o Serviço de Bot do Azure
Crie um bot de perguntas frequentes com o QnA Maker e o [Serviço de Bot](https://azure.microsoft.com/services/bot-service/) do Azure sem código.

Neste tutorial, você aprenderá como:

<!-- green checkmark -->
> [!div class="checklist"]
> * Vincular uma base de dados de conhecimento do QnA Maker a um Serviço de Bot do Azure
> * Implantar o bot
> * Bater papo com o bot no Webchat
> * Disponibilizar o bot entre os canais compatíveis

## <a name="create-and-publish-a-knowledge-base"></a>Criar e publicar uma base de dados de conhecimento

Siga o [início rápido](../Quickstarts/create-publish-knowledge-base.md) para criar uma base de dados de conhecimento. Depois que a base de dados de conhecimento for publicada com sucesso, você chegará à página abaixo.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

![Captura de tela da publicação bem-sucedida](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

![Captura de tela da publicação bem-sucedida do gerenciado](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint-managed.png)

---

## <a name="create-a-bot"></a>Criar um bot

Após a publicação, é possível criar um bot na página **Publicar**:

* Você pode criar vários bots rapidamente, todos apontando para a mesma base de dados conhecimento para diferentes regiões ou planos de preço para os bots individuais.
* Se você quiser apenas um bot para a base de dados de conhecimento, use o link **Exibir todos os bots no portal do Azure** para exibir uma lista de seus bots atuais.

Quando você faz alterações na base de dados de conhecimento e republica, não é necessário fazer mais nada com o bot. Ele já está configurado para trabalhar com a base de dados de conhecimento e trabalha com todas as alterações futuras na base de dados de conhecimento. Sempre que você publica uma base de dados de conhecimento, todos os bots conectados a ela são atualizados automaticamente.

1. No portal do QnA Maker, na página **Publicar**, selecione **Criar bot**. Esse botão será exibido somente depois que você tiver publicado a base de dados de conhecimento.

     # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

    ![Captura de tela da criação de um bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

    ![Captura de tela da criação de um bot gerenciado em versão prévia](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page-managed.png)

    ---
    

1. É aberta uma nova guia do navegador para o portal do Azure com a página de criação do Serviço de Bot do Azure. Configure o serviço de bot do Azure. O bot e o QnA Maker podem compartilhar o plano do serviço de aplicativo Web, mas não podem compartilhar o aplicativo Web. Isso significa que o **nome do aplicativo** para o bot deve ser diferente do nome do aplicativo usado para o serviço do QnA Maker.

    * **Deve ser feito**
        * Alterar o identificador de bot se ele não for exclusivo.
        * Selecionar a linguagem de programação do SDK. Depois que o bot for criado, você poderá baixar o código em seu ambiente de desenvolvimento local e continuar o processo de desenvolvimento.
    * **O que não fazer**
        * Alterar as configurações a seguir no portal do Azure ao criar o bot. Elas são preenchidas previamente para sua base de dados de conhecimento existente:
           * Chave de autenticação do QnA
           * Plano e local do serviço de aplicativo


1. Depois que o bot for criado, abra o recurso **Serviço de bot**.
1. Em **Gerenciamento de Bot**, selecione **Testar no Webchat**.
1. No prompt de chat de **Digite sua mensagem**, digite:

    `Azure services?`

    O chatbot responde com uma resposta de sua base de dados de conhecimento.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Insira uma consulta de usuário no Webchat de teste.":::
1. Disponibilizar o bot entre os [canais compatíveis](/azure/bot-service/bot-service-manage-channels) adicionais.