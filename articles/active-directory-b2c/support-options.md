---
title: Suporte para o Azure Active Directory B2C | Microsoft Docs
description: Como registrar solicitações de suporte para o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5195241003b1ce4ea505002e2cc3c10410e6cde1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78183713"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: solicitações de suporte a arquivo
Você pode arquivar solicitações de suporte para Azure Active Directory B2C (Azure AD B2C) no portal do Azure usando as seguintes etapas:

1. Alterne do locatário B2C para outro que tenha uma assinatura do Azure associada a ele. Normalmente, o segundo é o locatário de funcionário ou o locatário padrão criado quando você se inscreveu para uma assinatura do Azure. Para saber mais, consulte [como uma assinatura do Azure está relacionada ao Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    ![portal do Azure com seleção de locatário realçada](./media/support-options/support-switch-dir.png)

1. Depois de alternar os locatários, clique em **Ajuda + suporte**.

    ![Bloco de ajuda + suporte realçado em portal do Azure](./media/support-options/support-support.png)

1. Clique em **nova solicitação de suporte**.

    ![Novo bloco de solicitação de suporte realçado em portal do Azure](./media/support-options/support-new.png)

1. Na folha **Fundamentos**, use esses detalhes e clique em **Próximo**.

    * O **Tipo de emissão** é **Técnico**.
    * Escolha a **Assinatura**apropriada.
    * O **Serviço** é **Active Directory**.
    * Escolha o **Plano de suporte**apropriado. Se não tiver um, inscreva-se para ter um [aqui](https://azure.microsoft.com/support/plans/).

     ![Página noções básicas com o botão Avançar realçado em portal do Azure](./media/support-options/support-basics.png)

1. Na folha **Problema**, use esses detalhes e clique em **Próximo**.

    * Escolha o nível de **Gravidade** apropriado.
    * O **Tipo de problema** é **B2C**.
    * Escolha a **Categoria**apropriada.
    * Descreva seu problema no campo **Detalhes** . Forneça detalhes como o nome do locatário B2C, a descrição do problema, a mensagens de erro, as IDs de correlação (se estiverem disponíveis) e assim por diante.
    * No campo **Período** , forneça a data e a hora (incluindo o fuso horário) de quando o problema ocorreu.
    * Em **Upload do arquivo**, carregue todas as capturas de tela e arquivos que você acha que ajudariam na resolução do problema.

     ![Página do problema com o botão Avançar realçado em portal do Azure](./media/support-options/support-problem.png)

1. Na folha **Informações de contato** , adicione suas informações de contato. Clique em **Criar**.

    ![Página de informações de contato com o botão criar realçado no portal](./media/support-options/support-contact.png)

1. Depois de enviar a solicitação de suporte, você pode monitorá-lo clicando em **Ajuda + suporte** no quadro inicial, então, em **Gerenciar solicitações de suporte**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Problema conhecido: o preenchimento de uma solicitação de suporte no contexto de um locatário B2C

Se tiver ignorado a etapa 2 descrita acima e tentar criar uma solicitação de suporte no contexto do seu locatário B2C, você verá o erro a seguir.

> [!IMPORTANT]
> Não tente se inscrever em uma nova assinatura do Azure em seu locatário B2C.

![Você não tem um erro de assinatura exibido no portal do Azure](./media/support-options/support-no-sub.png)
