---
title: Gerenciar QnA Maker QnA Maker de aplicativos
description: O QnA Maker permite que várias pessoas colaborem em uma base de dados de conhecimento. O QnA Maker oferece uma capacidade para melhorar a qualidade de sua base de dados de conhecimento com o aprendizado ativo. É possível revisar, aceitar ou rejeitar e adicionar sem remover ou alterar as perguntas existentes.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 49bfca118e53bbe3e4287b2ce25e5baffa717175
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217322"
---
# <a name="manage-qna-maker-app"></a>Gerenciar QnA Maker aplicativo

QnA Maker permite que você colabore com autores e editores de conteúdo diferentes, oferecendo uma capacidade de restringir o acesso do colaborador com base na função do colaborador.
Saiba mais sobre os [conceitos de autenticação do colaborador do QnA Maker](../Concepts/role-based-access-control.md).

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Adicionar controle de acesso baseado em função do Azure (RBAC do Azure)

QnA Maker permite que várias pessoas colaborem em todas as bases de dados de conhecimento no mesmo recurso de QnA Maker. Esse recurso é fornecido com o [controle de acesso baseado em função do Azure (RBAC do Azure)](../../../role-based-access-control/role-assignments-portal.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Acesso no nível de recurso QnA Maker

Não é possível compartilhar uma base de dados de conhecimento específica em um serviço QnA Maker. Se você quiser um controle de acesso mais granular, considere distribuir suas bases de dados de conhecimento em diferentes QnA Maker recursos e, em seguida, adicionar funções a cada recurso.

## <a name="add-a-role-to-a-resource"></a>Adicionar uma função a um recurso

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Adicionar uma conta de usuário ao recurso de QnA Maker

As etapas a seguir usam a função de colaborador, mas qualquer uma das funções pode ser adicionada usando estas etapas

1. Entre no portal [do Azure](https://portal.azure.com/) e vá para o recurso QnA Maker.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Abra a guia **Controle de Acesso (IAM)**.

    ![IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Selecione **Adicionar**.

    ![Adicionar IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Selecione uma função na lista a seguir:

    |Função|
    |--|
    |Proprietário|
    |Colaborador|
    |QnA Maker leitor de serviços cognitivas|
    |Editor de QnA Maker de serviços cognitivas|
    |Usuário dos Serviços Cognitivos|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker Adicionar função IAM.":::

1. Insira o endereço de email do usuário e pressione **salvar**.

    ![Adicionar no IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Exibir QnA Maker base de dados de conhecimento

Quando a pessoa que você compartilhou seu serviço de QnA Maker com logs no [portal de QnA Maker](https://qnamaker.ai), ele pode ver todas as bases de dados de conhecimento nesse serviço com base em sua função.

Quando eles selecionam uma base de dados de conhecimento, sua função atual nesse QnA Maker recurso fica visível ao lado do nome da base de dados de conhecimento.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Captura de tela da base de dados de conhecimento no modo de edição com o nome da função entre parênteses ao lado do nome da base de dados de conhecimento no canto superior esquerdo da página da Web.":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](./manage-knowledge-bases.md)
