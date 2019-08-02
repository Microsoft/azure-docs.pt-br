---
title: Criar, treinar e publicar base de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: É possível criar uma KB (base de dados de conhecimento) do QnA Maker com base no seu próprio conteúdo, como perguntas frequentes ou manuais de produto. A base de dados de conhecimento do QnA Maker é criada com base em uma página da Web simples de perguntas frequentes, a fim de solucionar dúvidas sobre a recuperação da chave do BitLocker.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 03ce1047dd175ae4a676fa1461632a8e23122a8d
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249721"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>Criar, treinar e publicar sua base de dados de conhecimento do QnA Maker

É possível criar uma KB (base de dados de conhecimento) do QnA Maker com base no seu próprio conteúdo, como perguntas frequentes ou manuais de produto. Este artigo inclui um exemplo de como criar uma base de dados de conhecimento do QnA Maker com base em uma página da Web simples de perguntas frequentes para responder perguntas sobre a recuperação da chave do BitLocker.

## <a name="prerequisite"></a>Pré-requisito

> [!div class="checklist"]
> * Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-qna-maker-knowledge-base"></a>Criar uma base de dados de conhecimento do QnA Maker

1. Entre no portal [QnAMaker.ai](https://QnAMaker.ai) com suas credenciais do Azure.

1. No portal do QnA Maker, selecione **Criar uma base de dados de conhecimento**.

   ![Captura de tela do portal do QnA Maker](../media/qna-maker-create-kb.png)

1. Na página **Criar**, na etapa 1, selecione **Criar um serviço de QnA**. Você será direcionado para o [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) para configurar o serviço do QnA Maker na sua assinatura. Se o portal do Azure atingir o tempo limite, selecione **Tentar novamente** no site. Depois que você se conectar, o painel do Azure será exibido.

1. Após a criação bem-sucedida de um novo serviço do QnA Maker no Azure, retorne para qnamaker.ai/create. Selecione o serviço do QnA Maker nas listas suspensas da etapa 2. Se você tiver criado um serviço do QnA Maker, não se esqueça de atualizar a página.

   ![Captura de tela da seleção de uma base de dados de conhecimento do serviço do QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Na etapa 3, dê à sua base de dados de conhecimento o nome **Minha base de dados de QnA de amostra**.

1. Para adicionar conteúdo à sua base de dados de conhecimento, selecione os três tipos de fontes de dados. Na etapa 4, em **Popular a KB**, adicione a URL [Perguntas frequentes sobre a recuperação do BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) na caixa **URL**.

   ![Captura de tela da adição de fontes de dados](../media/qnamaker-quickstart-kb/add-datasources.png)

1. Na etapa 5, selecione **Criar sua KB**.

1. Enquanto o QnA Maker cria o banco de dados de conhecimento, uma janela pop-up é exibida. O processo de extração leva alguns minutos para ler a página HTML e identificar perguntas e respostas.

1. Depois que o QnA Maker criar com êxito a base de dados de conhecimento, a página **Base de dados de conhecimento** será aberta. Você pode editar o conteúdo da base de dados de conhecimento nesta página.

## <a name="edit-the-knowledge-base"></a>Editar a base de dados de conhecimento

1. No portal do QnA Maker, na seção **Editar**, selecione **Adicionar par de QnA** para adicionar uma nova linha à base de dados de conhecimento. Em **Pergunta**, insira **Oi.** Em **Resposta**, insira **Olá. Pergunte-me sobre o BitLocker.**

    ![Captura de tela do portal do QnA Maker](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. Na parte superior direito, selecione **Salvar e treinar** para salvar suas edições e treinar o modelo do QnA Maker. As edições não serão mantidas se não forem salvas.

## <a name="test-the-knowledge-base"></a>Testar a base de dados de conhecimento

1. No portal do QnA Maker, selecione **Testar** para verificar se as alterações entraram em vigor. Insira `hi there` na caixa e pressione Enter. A resposta que você criou será exibida.

1. Selecione **Inspecionar** para examinar a resposta mais detalhadamente. A janela de teste é usada para testar as alterações na base de dados de conhecimento antes que elas sejam publicadas.

    ![Captura de tela do painel de teste](../media/qnamaker-quickstart-kb/inspect.png)

1. Selecione **Testar** novamente para fechar o item pop-up **Testar**.

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento

Ao publicar uma base de dados de conhecimento, o conteúdo de perguntas e respostas base de dados de conhecimento é movido do índice de teste para um índice de produção no Azure Search.

![Captura de tela da movimentação do conteúdo de sua base de dados de conhecimento](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. No portal do QnA Maker, no menu ao lado de **Editar**, selecione **Publicar**. Para confirmar, selecione **Publicar** na página.

1. O serviço do QnA Maker foi publicado com êxito. É possível usar o ponto de extremidade no aplicativo ou código de bot.

    ![Captura de tela da publicação bem-sucedida](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Criar um bot

Após a publicação, é possível criar um bot na página **Publicar**: 

* Você pode criar vários bots rapidamente, todos apontando para a mesma base de dados conhecimento para diferentes regiões ou planos de preço para os bots individuais. 
* Se você quiser apenas um bot para a base de dados de conhecimento, use o link **Exibir todos os bots no portal do Azure** para exibir uma lista de seus bots atuais. 

Quando você faz alterações na base de dados de conhecimento e republica, não é necessário fazer mais nada com o bot. Ele já está configurado para trabalhar com a base de dados de conhecimento e trabalha com todas as alterações futuras na base de dados de conhecimento. Sempre que você publica uma base de dados de conhecimento, todos os bots conectados a ela são atualizados automaticamente.

1. No portal do QnA Maker, na página **Publicar**, selecione **Criar bot**. Esse botão será exibido somente depois que você tiver publicado a base de dados de conhecimento.

    ![Captura de tela da criação de um bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. É aberta uma nova guia do navegador para o portal do Azure com a página de criação do Serviço de Bot do Azure. Configure o serviço de bot do Azure. Para saber mais sobre essas definições de configuração, confira [Criar um Bot do QnA com o Serviço de Bot do Azure v4](../tutorials/create-qna-bot.md).
    
    * Não altere as configurações a seguir no portal do Azure ao criar o bot. Elas são preenchidas previamente para sua base de dados de conhecimento existente: 
        * Chave de autenticação do QnA
        * Plano e local do serviço de aplicativo
        * Armazenamento do Azure
    * O bot e o QnA Maker podem compartilhar o plano do serviço de aplicativo Web, mas não podem compartilhar o aplicativo Web. Isso significa que o **nome do aplicativo** deve ser diferente do nome do aplicativo usado ao criar o serviço do QnA Maker. 


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](../How-To/create-knowledge-base.md)
