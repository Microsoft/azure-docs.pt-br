---
title: Colaborando na base de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker permite que várias pessoas colaborem em uma base de dados de conhecimento. Esse recurso é fornecido com o Controle de Acesso Baseado em Função do Azure.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: ca754f197a46fc41b6f1b432611a2177ec0afafa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61374756"
---
# <a name="collaborate-on-your-knowledge-base"></a>Colaborar com sua base de dados de conhecimento

O QnA Maker permite que várias pessoas colaborem em uma base de dados de conhecimento. Esse recurso é fornecido com o [Controle de Acesso Baseado em Função](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) do Azure. 

Execute as seguintes etapas para compartilhar seu serviço QnA Maker com alguém:

1. Faça logon no portal do Azure e vá para o recurso QnA Maker.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Abra a guia **Controle de Acesso (IAM)**.

    ![IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Selecione **Adicionar**.

    ![Adicionar IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Selecione a função de **Proprietário** ou **Colaborador**. Você não pode conceder acesso somente leitura por meio do Controle de Acesso Baseado em Função. Função de Proprietário e Colaborador tem direito de acesso de leitura/gravação ao serviço QnA Maker.

    ![Adicionar função no IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Insira o email com o qual você deseja compartilhar com e clique em Salvar.

    ![Adicionar no IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Agora quando a pessoa com a qual você compartilhou o serviço QnA Maker, fizer logon no [portal do QnA Maker](https://qnamaker.ai) ela pode ver todas as bases de dados de conhecimento do serviço.

Lembre-se de que não é possível compartilhar uma base de Conhecimento específica em um serviço do QnA Maker. Se desejar um controle de acesso mais granular, considere distribuir as bases de dados de conhecimento em diferentes serviços do QnA Maker.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Testar uma base de dados de conhecimento](./test-knowledge-base.md)
