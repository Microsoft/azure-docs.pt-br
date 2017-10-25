---
title: 'Azure Active Directory B2C: Suporte | Microsoft Docs'
description: "Como arquivar solicitações de suporte para o Active Directory B2C do Azure"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 51922008-6fbd-487c-b2c7-a5db57d154b4
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: ad02321c99c57d0f83f7eacb354e47d8c6d4d14f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: solicitações de suporte a arquivo
Você pode arquivar solicitações de suporte para o Azure AD (Azure Active Directory) B2C no portal do Azure usando as seguintes etapas:

1. Siga estas etapas para [navegar até a folha de recursos do B2C no Portal do Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Alterne do locatário B2C para outro que tenha uma assinatura do Azure associada a ele. Normalmente, o segundo é o locatário de funcionário ou o locatário padrão criado quando você se inscreveu para uma assinatura do Azure. Para saber mais, consulte [como uma assinatura do Azure está relacionada ao Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md).
   
    ![Suporte - Alternar locatários](./media/active-directory-b2c-support/support-switch-dir.png)
3. Depois de alternar os locatários, clique em **Ajuda + suporte**.
   
    ![Suporte - Ajuda + Suporte](./media/active-directory-b2c-support/support-support.png)
4. Clique em **Nova solicitação de suporte**.
   
    ![Suporte - Novo](./media/active-directory-b2c-support/support-new.png)
5. Na folha **Fundamentos**, use esses detalhes e clique em **Próximo**.
   
   * O **Tipo de emissão** é **Técnico**.
   * Escolha a **Assinatura**apropriada.
   * O **Serviço** é **Active Directory**.
   * Escolha o **Plano de suporte**apropriado. Se não tiver um, inscreva-se para ter um [aqui](https://azure.microsoft.com/en-us/support/plans/).
     
     ![Suporte - Noções básicas](./media/active-directory-b2c-support/support-basics.png)
6. Na folha **Problema**, use esses detalhes e clique em **Próximo**.
   
   * Escolha o nível de **Gravidade** apropriado.
   * O **Tipo de problema** é **B2C**.
   * Escolha a **Categoria**apropriada.
   * Descreva seu problema no campo **Detalhes** . Forneça detalhes como o nome do locatário B2C, a descrição do problema, a mensagens de erro, as IDs de correlação (se estiverem disponíveis) e assim por diante.
   * No campo **Período** , forneça a data e a hora (incluindo o fuso horário) de quando o problema ocorreu.
   * Em **Upload do arquivo**, carregue todas as capturas de tela e arquivos que você acha que ajudariam na resolução do problema.
     
     ![Suporte - Problema](./media/active-directory-b2c-support/support-problem.png)
7. Na folha **Informações de contato** , adicione suas informações de contato. Clique em **Criar**.
   
    ![Suporte - Contato](./media/active-directory-b2c-support/support-contact.png)
8. Depois de enviar a solicitação de suporte, você pode monitorá-lo clicando em **Ajuda + suporte** no quadro inicial, então, em **Gerenciar solicitações de suporte**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Problema conhecido: o preenchimento de uma solicitação de suporte no contexto de um locatário B2C
Se tiver ignorado a etapa 2 descrita acima e tentar criar uma solicitação de suporte no contexto do seu locatário B2C, você verá o erro a seguir.

> [!IMPORTANT]
> Não tente se inscrever em uma nova assinatura do Azure em seu locatário B2C.  
> 
> 

![Suporte — sem assinatura](./media/active-directory-b2c-support/support-no-sub.png)

