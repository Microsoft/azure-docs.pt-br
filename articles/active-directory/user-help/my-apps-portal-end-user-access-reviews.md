---
title: Gerenciamento do acesso da organização a aplicativos e grupos – Azure AD
description: Saiba como realizar uma revisão de acesso para gerenciar o acesso de segurança para os aplicativos e grupos da sua organização no portal Meus Aplicativos.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 34885e2a364778a2f81f4920aa26aa3bb5f40320
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100095011"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Realização de uma revisão de acesso no portal Meus Aplicativos

Use a sua conta corporativa ou de estudante com o portal **Meus Aplicativos** da Web para realizar revisões de acesso para os seus aplicativos e grupos. As revisões de acesso ajudam a gerenciar informações de acesso desatualizadas ou a alterar os requisitos de acesso e garantir que eles sejam revisados e atualizados.

Se você não tiver acesso ao portal **Meus Aplicativos**, entre em contato com a assistência técnica para obter permissão.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Este conteúdo destina-se aos usuários do portal **Meus Aplicativos**. Se você for administrador, poderá encontrar mais informações sobre como configurar e gerenciar seus aplicativos baseados em nuvem na [Documentação de Gerenciamento do Aplicativo](../manage-apps/index.yml).
>
> Se você vir um erro ao entrar com um conta Microsoft pessoal, ainda poderá entrar usando o nome de domínio da sua organização (como contoso.com) ou a **ID de locatário** da sua organização em uma das seguintes URLs:
>
>   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
>   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*

## <a name="manage-access-reviews"></a>Gerenciar revisões de acesso

Se o administrador tiver concedido permissão para realizar suas próprias revisões de acesso, gerencie o acesso de seus grupos ou aplicativos no bloco **Revisões de acesso** na página do portal **Meus Aplicativos**.

>[!Note]
>Caso não veja o bloco **Revisões de acesso**, isso significa que você não tem permissão para realizar revisões de acesso ou que você não tem nenhuma revisão pendente aguardando sua aprovação. Se você acha que deve ter acesso ao bloco, entre em contato com a assistência técnica para obter ajuda.

## <a name="to-perform-your-access-reviews"></a>Para realizar suas próprias revisões de acesso

1. Entre sua conta corporativa ou de estudante.

1. Abra o navegador da Web e acesse https://myapps.microsoft.com ou use o link fornecido pela sua organização. Por exemplo, você pode ser direcionado a uma página personalizada para sua organização, como https://myapps.microsoft.com/contoso.com.

    A página **Aplicativos** é exibida, mostrando todos os aplicativos baseados em nuvem pertencentes à sua organização e disponíveis para uso.

    ![Página Aplicativos no portal Meus Aplicativos](media/my-apps-portal/my-apps-home.png)

1. Selecione o bloco **Revisões de acesso** para ver uma lista de revisões de acesso aguardando sua aprovação.

    ![Página Revisões de acesso com revisões de acesso pendentes da organização](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

1. Selecione **Iniciar revisão** para iniciar a revisão de acesso.

5. Examine o acesso e determine se ele ainda é necessário.

    ![Página Revisão de acesso, mostrando os detalhes da revisão](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Se for um administrador e tiver permissão para revisar o acesso de sua organização a grupos e aplicativos, você verá uma página diferente. Para obter mais informações sobre como revisar grupos ou aplicativos para sua organização, veja [Revisão de acesso para grupos ou aplicativos nas Revisões de Acesso do Azure AD](../governance/perform-access-review.md).

6. Selecione **Sim** para manter seu acesso ou **Não** remover o acesso.

    Se selecionar **Sim**, dê uma justificativa na caixa **Motivo**.

    ![Página Revisão de acesso, mostrando a caixa Motivo com texto de exemplo](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Selecione **Enviar**.

    A revisão de acesso está concluída e você retorna ao portal **Meus Aplicativos**.

    >[!Note]
    >Você pode alterar o acesso a qualquer momento até que o seu período de revisão de acesso termine. Se remover o acesso a um aplicativo ou grupo, esse acesso não será removido imediatamente. A remoção ocorre após o término do período de revisão de acesso ou quando um administrador fecha a revisão.

## <a name="next-steps"></a>Próximas etapas

- [Acesso e uso de aplicativos no portal Meus Aplicativos](my-apps-portal-end-user-access.md)
- [Alteração das informações do seu perfil](./my-account-portal-settings.md)
- [Exibição e atualização das informações relacionadas a grupos](my-apps-portal-end-user-groups.md)