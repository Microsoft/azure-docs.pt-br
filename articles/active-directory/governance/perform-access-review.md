---
title: Examinar o acesso a grupos & aplicativos em revisões de acesso – Azure AD
description: Saiba como revisar o acesso de membros do grupo ou acesso ao aplicativo em Azure Active Directory revisões de acesso.
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
ms.openlocfilehash: b5cd7a5737b2d13e63eabbbddd076cfc7aff83ac
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746789"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Examinar o acesso a grupos e aplicativos nas revisões de acesso do Azure AD

O Azure Active Directory (AD do Azure) simplifica como as empresas gerenciam o acesso a grupos e aplicativos no Azure AD e outros serviços online da Microsoft com um recurso chamado revisões de acesso do Azure AD. Este artigo abordará como um revisor designado executa uma revisão de acesso para membros de um grupo ou usuários com acesso a um aplicativo. Se você quiser examinar, o acesso a um pacote Leia o [acesso de um pacote de acesso no gerenciamento de direitos do Azure ad](entitlement-management-access-reviews-review-access.md)

## <a name="perform-access-review-using-my-apps"></a>Executar análise de acesso usando meus aplicativos

Você pode iniciar o processo de revisão de acesso do email de notificação ou indo diretamente para o site.

- **Email**:

>[!IMPORTANT]
> Pode haver atrasos no recebimento de email e em alguns casos pode levar até 24 horas. Adicione azure-noreply@microsoft.com à sua lista de destinatários seguros para certificar-se de que você está recebendo todos os emails.

1. Procure um email da Microsoft solicitando que você examine o acesso. Aqui está um email de exemplo para examinar o acesso de um grupo.

    ![Captura de tela que mostra um email de exemplo da Microsoft para revisar o acesso a um grupo.](./media/perform-access-review/access-review-email.png)

1. Clique no link **Iniciar revisão** para abrir a revisão de acesso.

- **Se você não tiver o email**, poderá encontrar suas revisões de acesso pendentes seguindo estas etapas.

    1. Entre no portal meus aplicativos em [https://myapps.microsoft.com](https://myapps.microsoft.com) .

        ![Portal de meus aplicativos Listando aplicativos aos quais você tem permissões](./media/perform-access-review/myapps-access-panel.png)

    1. No canto superior direito da página, clique no usuário ao lado de seu nome e organização padrão. Se houver mais de uma organização listada, selecione a organização que solicitou uma análise de acesso.

    1. Clique no bloco **revisões de acesso** para ver uma lista de revisões de acesso pendentes.

        > [!NOTE]
        > Se o bloco de **revisões de acesso** não estiver visível, não haverá nenhuma análise de acesso a ser executada para essa organização e nenhuma ação será necessária no momento.

        ![Captura de tela que mostra a lista de revisões de acesso pendentes para aplicativos e grupos.](./media/perform-access-review/access-reviews-list.png)

    1. Clique no link **Iniciar revisão** para a revisão de acesso que você deseja executar.

Depois de abrir a revisão de acesso, você verá os nomes dos usuários que precisam ter seu acesso revisado.

Se a solicitação for revisar seu próprio acesso, a página terá uma aparência diferente. Para obter mais informações, consulte [examinar o acesso a grupos ou aplicativos](review-your-access.md).

![Abrir revisão de acesso listando os usuários a serem revisados](./media/perform-access-review/perform-access-review.png)

Há duas maneiras de aprovar ou negar o acesso:

- Você pode aprovar ou negar o acesso para um ou mais usuários ' manualmente ' escolhendo a ação apropriada para cada solicitação de usuário.
- Você pode aceitar as recomendações do sistema.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Aprovar ou negar acesso para um ou mais usuários

1. Examine a lista de usuários e decida se deseja aprovar ou negar o acesso contínuo.

    - Para aprovar ou negar o acesso de um único usuário, clique na linha para abrir uma janela a fim de especificar a ação a ser tomada. 
    - Para aprovar ou negar acesso para vários usuários, adicione marcas de seleção ao lado dos usuários e, em seguida, clique no botão **revisar X usuário (s)** para abrir uma janela para especificar a ação a ser tomada.

1. Clique em **aprovar** ou **negar**. 

    ![Janela de ação que inclui opções aprovar, negar e não sei](./media/perform-access-review/approve-deny.png)
    >[!NOTE]
    > Se não tiver certeza, você pode clicar em **não saber**. e o usuário fica para manter seu acesso e sua escolha é registrada nos logs de auditoria.

1. O administrador da revisão de acesso pode exigir que você forneça um motivo na caixa de **motivo** para sua decisão. Mesmo quando um motivo não é necessário. Você ainda pode fornecer um motivo para sua decisão e as informações que você incluir estarão disponíveis para outros revisores.

1. Depois de especificar a ação a ser tomada, clique em **salvar**.

    >[!NOTE]
    > Você pode alterar sua resposta a qualquer momento antes que a revisão de acesso seja encerrada. Se você quiser alterar sua resposta, selecione a linha e atualize a resposta. Por exemplo, você pode aprovar um usuário negado anteriormente ou negar um usuário aprovado anteriormente.

    >[!IMPORTANT]
    > - Se um usuário tiver acesso negado, ele não será removido imediatamente. Eles são removidos quando o período de revisão termina ou quando um administrador para a revisão se a [aplicação automática](complete-access-review.md#apply-the-changes) estiver habilitada.
    > - Se houver vários revisores, a última resposta enviada será registrada. Considere um exemplo em que um administrador designa dois revisores – Alice e Bob. Alice abre a revisão de acesso primeiro e aprova a solicitação de acesso do usuário. Antes de o período de revisão terminar, Bob abre a revisão de acesso e nega o acesso na mesma solicitação anteriormente aprovada por Alice. A última decisão que nega o acesso é a resposta que é registrada.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Aprovar ou negar o acesso com base nas recomendações

Para fazer análises de acesso mais fáceis e rápidas para você, também fornecemos recomendações que você pode aceitar com um único clique. As recomendações são geradas com base na atividade de entrada do usuário.

1. Na barra azul na parte inferior da página, clique em **aceitar recomendações**.

    ![Captura de tela que mostra a listagem de revisão de acesso aberto com o botão "aceitar recomendações" selecionado.](./media/perform-access-review/accept-recommendations.png)

    Você verá um resumo das ações recomendadas.

    ![Janela que exibe um resumo das ações recomendadas](./media/perform-access-review/accept-recommendations-summary.png)

1. Clique em **OK** para aceitar as recomendações.

## <a name="perform-access-review-using-my-access-new"></a>Executar análise de acesso usando meu acesso (novo)

Você pode chegar à nova experiência do revisor com a interface do usuário atualizada em meu acesso de duas maneiras diferentes:

### <a name="my-apps-portal"></a>Portal de meus aplicativos

1. Entre em meus aplicativos em [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Portal de meus aplicativos Listando aplicativos aos quais você tem permissões](./media/perform-access-review/myapps-access-panel.png)

2. Clique no bloco **revisões de acesso** para ver uma lista de revisões de acesso pendentes.

    > [!NOTE]
    > Se o bloco de **revisões de acesso** não estiver visível, não haverá nenhuma análise de acesso a ser executada para essa organização e nenhuma ação será necessária no momento.

![Lista de revisões de acesso pendente para aplicativos e grupos com a nova faixa de experiência disponível exibida durante a visualização](./media/perform-access-review/banner.png)

3. Clique em **experimentar!** na faixa na parte superior da página. Isso levará você à nova experiência meu acesso.
  
### <a name="email"></a>Email

  >[!IMPORTANT]
> Pode haver atrasos no recebimento de email e em alguns casos pode levar até 24 horas. Adicione azure-noreply@microsoft.com à sua lista de destinatários seguros para certificar-se de que você está recebendo todos os emails.

   1. Procure um email da Microsoft solicitando que você examine o acesso. Você pode ver um exemplo de mensagem de email abaixo:

   ![Email de exemplo da Microsoft para revisar o acesso a um grupo](./media/perform-access-review/access-review-email-preview.png)

   2. Clique no link **Iniciar revisão** para abrir a revisão de acesso.

>[!NOTE]
>Se clicar em Iniciar revisão levar você para **meus aplicativos** , siga as etapas listadas na seção acima denominada **portal meus aplicativos**.

### <a name="navigate-to-my-access-directly"></a>Navegar até o meu acesso diretamente

Você também pode exibir suas revisões de acesso pendentes usando seu navegador para abrir meu acesso.

1. Entre no meu acesso em https://myaccess.microsoft.com/

2. Selecione **revisões de acesso** no menu na barra do lado esquerdo para ver uma lista de revisões de acesso pendentes atribuídas a você.

   ![revisões de acesso no menu](./media/perform-access-review/access-review-menu.png)

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Aprovar ou negar acesso para um ou mais usuários

Depois de abrir meu acesso em grupos e aplicativos, você pode ver:

- **Nome** do O nome da revisão de acesso.
- **Devido** A data de conclusão da revisão. Após essa data, os usuários com negação podem ser removidos do grupo ou do aplicativo que está sendo revisado.
- **Recurso** do O nome do recurso em revisão.
- Em **andamento** O número de usuários revisados no número total de usuários que fazem parte desta revisão de acesso.

Clique no nome de uma revisão de acesso para começar.

![Lista de revisões de acesso pendente para aplicativos e grupos](./media/perform-access-review/access-reviews-list-preview.png)

Depois de abrir, você verá a lista de usuários no escopo para a revisão de acesso. Se a solicitação for revisar seu próprio acesso, a página terá uma aparência diferente. Para obter mais informações, consulte [examinar o acesso a grupos ou aplicativos](review-your-access.md).

Há duas maneiras de aprovar ou negar o acesso:

- Você pode aprovar ou negar manualmente o acesso para um ou mais usuários.
- Você pode aceitar as recomendações do sistema.

#### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Aprovar ou negar manualmente o acesso para um ou mais usuários

1. Examine a lista de usuários e decida se deseja aprovar ou negar o acesso contínuo.
2. Selecione um ou mais usuários clicando no círculo ao lado de seus nomes.
3. Selecione **aprovar** ou **negar** na barra acima.
    - Se não tiver certeza, você pode clicar em **não saber**. O usuário fica para manter seu acesso e sua escolha é registrada nos logs de auditoria. É importante ter em mente que qualquer informação que você fornecer estará disponível para outros revisores. Eles podem ler seus comentários e levá-los em conta ao examinarem a solicitação.

    ![Abrir revisão de acesso listando os usuários que precisam de revisão](./media/perform-access-review/user-list-preview.png)

4. O administrador da revisão de acesso pode exigir que você forneça um motivo na caixa de **motivo** para sua decisão. Mesmo quando um motivo não é necessário. Você ainda pode fornecer um motivo para sua decisão e as informações que você incluir estarão disponíveis para outros Aprovadores para revisão.

5. Clique em **Enviar**.
    - Você pode alterar sua resposta a qualquer momento até que a revisão de acesso seja encerrada. Se você quiser alterar sua resposta, selecione a linha e atualize a resposta. Por exemplo, você pode aprovar um usuário negado anteriormente ou negar um usuário aprovado anteriormente.

 >[!IMPORTANT]
 > - Se um usuário tiver acesso negado, ele não será removido imediatamente. Eles são removidos quando o período de revisão termina ou quando um administrador para a revisão. 
 > - Se houver vários revisores, a última resposta enviada será registrada. Considere um exemplo em que um administrador designa dois revisores – Alice e Bob. Alice abre a revisão de acesso primeiro e aprova a solicitação de acesso do usuário. Antes de o período de revisão terminar, Bob abre a revisão de acesso e nega o acesso na mesma solicitação anteriormente aprovada por Alice. A última decisão que nega o acesso é a resposta que é registrada.

#### <a name="approve-or-deny-access-based-on-recommendations"></a>Aprovar ou negar o acesso com base nas recomendações

Para fazer análises de acesso mais fáceis e rápidas para você, também fornecemos recomendações que você pode aceitar com um único clique. As recomendações são geradas com base na atividade de entrada do usuário.

1. Selecione um ou mais usuários e clique em **aceitar recomendações**.

    ![Abrir a listagem de revisão de acesso mostrando o botão aceitar recomendações](./media/perform-access-review/accept-recommendations-preview.png)

1. Clique em **Enviar** para aceitar as recomendações.

Para aceitar recomendações para todos os usuários, certifique-se de que nenhum esteja selecionado e clique no botão **aceitar recomendações** na barra superior.

>[!NOTE]
>Quando você aceita recomendações, as decisões anteriores não serão alteradas.

## <a name="next-steps"></a>Próximas etapas

- [Concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)
