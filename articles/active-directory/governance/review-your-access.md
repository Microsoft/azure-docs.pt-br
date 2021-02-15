---
title: Examine o acesso a grupos & aplicativos nas revisões de acesso – Azure AD
description: Saiba como revisar seu próprio acesso a grupos ou aplicativos em Azure Active Directory revisões de acesso.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3fef2f85ca7e7b4034c8582477796d49446ea44
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746772"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Examinar o acesso a grupos ou aplicativos nas revisões de acesso do Azure AD

O Azure Active Directory (AD do Azure) simplifica como as empresas gerenciam o acesso a grupos ou aplicativos no Azure AD e outros serviços online da Microsoft com um recurso chamado revisões de acesso do Azure AD.

Este artigo descreve como examinar seu próprio acesso a um grupo ou a um aplicativo.

## <a name="review-your-access-using-my-apps"></a>Examine seu acesso usando meus aplicativos

A primeira etapa para executar uma revisão de acesso é encontrar e abrir a revisão de acesso.

>[!IMPORTANT]
> Pode haver atrasos no recebimento de email e em alguns casos pode levar até 24 horas. Adicione azure-noreply@microsoft.com à sua lista de destinatários seguros para certificar-se de que você está recebendo todos os emails.

1. Procure um email da Microsoft que solicita que você examine o acesso. Aqui está um email de exemplo para examinar o acesso a um grupo.

    ![Email de exemplo da Microsoft para revisar o acesso a um grupo](./media/review-your-access/access-review-email.png)

1. Clique no link **examinar acesso** para abrir a revisão de acesso.

Se você não tiver o email, poderá encontrar suas revisões de acesso pendentes seguindo estas etapas.

1. Entre no portal meus aplicativos em [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Portal de meus aplicativos Listando aplicativos aos quais você tem permissões](./media/review-your-access/myapps-access-panel.png)

1. No canto superior direito da página, clique no símbolo do usuário para exibir seu nome e a organização padrão. Se houver mais de uma organização listada, selecione a organização que solicitou uma análise de acesso.

1. No lado direito da página, clique no bloco **revisões de acesso** para ver uma lista das revisões de acesso pendentes.

    Se o bloco não estiver visível, não haverá revisões de acesso para executar nessa organização e nenhuma ação será necessária no momento.

    ![Lista de revisões de acesso pendente para seus aplicativos e grupos](./media/review-your-access/access-reviews-list.png)

1. Clique no link **Iniciar revisão** para a revisão de acesso que você deseja executar.

### <a name="perform-the-access-review"></a>Executar a revisão de acesso

Depois de abrir a revisão de acesso, você poderá ver seu acesso.

1. Examine seu acesso e decida se você ainda precisa de acesso.

    Se a solicitação for revisar o acesso para outros, a página terá uma aparência diferente. Para obter mais informações, consulte [examinar o acesso a grupos ou aplicativos](perform-access-review.md).

    ![Captura de tela que mostra uma revisão de acesso aberto que pergunta se você ainda precisa de acesso a um grupo.](./media/review-your-access/perform-access-review.png)

1. Clique em **Sim** para manter seu acesso ou clique em **não** para remover o acesso.

1. Se você clicar em **Sim**, talvez seja necessário especificar uma justificativa na caixa **motivo** .

    ![Captura de tela que mostra uma revisão de acesso concluída que pergunta se você ainda precisa de acesso a um grupo, com "Sim" selecionado.](./media/review-your-access/perform-access-review-submit.png)

1. Clique em **Enviar**.

    Sua seleção é enviada e você retornou para o portal meus aplicativos.

    Se você quiser alterar sua resposta, reabra a página de revisões de acesso e atualize sua resposta. Você pode alterar sua resposta a qualquer momento até que a revisão de acesso seja encerrada.

    > [!NOTE]
    > Se você indicou que não precisa mais de acesso, não será removido imediatamente. Você será removido quando a revisão terminar ou quando um administrador parar a revisão.

## <a name="review-your-own-access-using-my-access-new"></a>Examine seu próprio acesso usando meu acesso (novo)

Você pode experimentar a nova experiência com a interface do usuário atualizada em meu acesso de duas maneiras diferentes:

### <a name="my-apps-portal"></a>Portal de meus aplicativos

1. Entre no portal meus aplicativos em [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Portal de meus aplicativos Listando aplicativos aos quais você tem permissões](./media/review-your-access/myapps-access-panel.png)

2. Clique no bloco **revisões de acesso** para ver uma lista de revisões de acesso pendentes.

    > [!NOTE]
    > Se o bloco de **revisões de acesso** não estiver visível, não haverá nenhuma análise de acesso a ser executada para essa organização e nenhuma ação será necessária no momento.

3. Clique em **experimentar!** na faixa na parte superior da página para ir para a nova experiência meu acesso.

    ![Lista de revisões de acesso pendente para aplicativos e grupos com a nova faixa de experiência disponível exibida durante a visualização](./media/review-your-access/banner-your-access.png)

4. Continuar na seção **executar a revisão de acesso**

### <a name="email"></a>Email

>[!IMPORTANT]
> Pode haver atrasos no recebimento de email e em alguns casos pode levar até 24 horas. Adicione azure-noreply@microsoft.com à sua lista de destinatários seguros para certificar-se de que você está recebendo todos os emails.

1. Procure um email da Microsoft solicitando que você examine o acesso. Você pode ver um exemplo de mensagem de email abaixo:

 ![Email de exemplo da Microsoft para revisar o acesso a um grupo](./media/review-your-access/access-review-email-preview.png)

2. Clique no link **examinar acesso** para abrir a revisão de acesso.

3. Continuar na seção **executar a revisão de acesso**

>[!NOTE]
>Se clicar em Iniciar revisão levar você para **meus aplicativos** , siga as etapas listadas na seção acima denominada **portal meus aplicativos**.

### <a name="directly-at-my-access"></a>Diretamente no meu acesso

Você também pode exibir suas revisões de acesso pendentes usando seu navegador para abrir meu acesso.

1. Entre no meu acesso em https://myaccess.microsoft.com/

2. Selecione **revisões de acesso** no menu na barra do lado esquerdo para ver uma lista de revisões de acesso pendentes atribuídas a você.

   ![revisões de acesso no menu](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>Executar a revisão de acesso

1. Em grupos e aplicativos, você pode ver:
    
    - **Nome** do O nome da revisão de acesso.
    - **Devido** A data de conclusão da revisão. Após essa data, os usuários com negação podem ser removidos do grupo ou do aplicativo que está sendo revisado.
    - **Recurso** do O nome do recurso em revisão.
    - Em **andamento** O número de usuários revisados no número total de usuários que fazem parte desta revisão de acesso.
    
2. Clique no nome de uma revisão de acesso para começar.

   ![Lista de revisões de acesso pendente para aplicativos e grupos](./media/review-your-access/access-reviews-list-preview.png)

3. Examine seu acesso e decida se você ainda precisa de acesso.

    Se a solicitação for revisar o acesso para outros, a página terá uma aparência diferente. Para obter mais informações, consulte [examinar o acesso a grupos ou aplicativos](perform-access-review.md).

    ![Abra a revisão de acesso perguntando se você ainda precisa de acesso a um grupo](./media/review-your-access/review-access-preview.png)

1. Selecione **Sim** para manter seu acesso ou selecione **não** para remover o acesso.

1. Se você clicar em **Sim**, talvez seja necessário especificar uma justificativa na caixa **motivo** .

    ![Revisão de acesso concluída perguntando se você ainda precisa de acesso a um grupo](./media/review-your-access/review-access-yes-preview.png)

1. Clique em **Enviar**.

    Sua seleção é enviada e você retornou para a página meu acesso.

    Se você quiser alterar sua resposta, reabra a página de revisões de acesso e atualize sua resposta. Você pode alterar sua resposta a qualquer momento até que a revisão de acesso seja encerrada.

    > [!NOTE]
    > Se você indicou que não precisa mais de acesso, não será removido imediatamente. Você será removido quando a revisão terminar ou quando um administrador parar a revisão.

## <a name="next-steps"></a>Próximas etapas

- [Concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)
