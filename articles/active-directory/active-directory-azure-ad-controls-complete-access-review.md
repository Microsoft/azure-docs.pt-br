---
title: "Concluir uma revisão de acesso de membros de um grupo ou o acesso a um aplicativo no Azure AD | Microsoft Docs"
description: "Saiba como concluir uma revisão de acesso para membros de um grupo ou os usuários com acesso a um aplicativo no Azure Active Directory."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: e83bc42d658c4d6304f98b99d0e0942595bd65d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Concluir uma revisão de acesso de membros de um grupo ou o acesso a um aplicativo no Azure AD

Os administradores podem solicitar uma revisão dos membros do grupo ou os usuários atribuídos a um aplicativo [criando uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md). O Azure AD envia automaticamente para os revisores um email solicitando que analisem o acesso. Se um usuário não recebeu um email, você pode enviar para ele as instruções em [como analisar o acesso](active-directory-azure-ad-controls-perform-access-review.md).  Depois que o período de revisão de acesso tiver acabado ou se o administrador tiver interrompido a revisão de acesso, siga as etapas deste artigo para ver e aplicar os resultados.

## <a name="viewing-an-access-review-in-the-azure-portal"></a>Exibição de uma revisão de acesso no portal do Azure

1. Vá para a [página de revisão de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), altere para a guia **Programas** e selecione o programa que contém o controle de revisão de acesso.
2. Clique em gerenciar e selecione o controle de revisão de acesso.  Se houver muitos controles no programa, você poderá filtrar os controles somente de um tipo específico, classificar por seu status ou procurar pelo nome do controle de revisão de acesso ou pelo nome de exibição do proprietário que o criou. 

## <a name="stopping-a-review-that-has-not-yet-completed"></a>Interrupção de uma revisão que ainda não foi concluída

Se a revisão ainda não tiver atingido a data de término agendada, um administrador pode parar a análise clicando no botão **Parar** para concluí-la no início.  Se quaisquer usuários ainda não tiverem sido examinados até este momento, eles não poderão ser após você parar a análise. Não é possível reiniciar uma análise após ela ter sido interrompida.

## <a name="applying-the-changes"></a>Aplicação das alterações 

Depois que uma revisão de acesso for concluída, porque atingiu a data de término ou um administrador a interrompeu manualmente, o botão **Aplicar** implementa o resultado da análise, atualizando o grupo ou aplicativo. Se o acesso do usuário tiver sido negado na revisão, então, quando o administrador clicar neste botão, o Azure AD removerá sua atribuição de associação ou aplicativo. 

Clicar no botão Aplicar não terá um efeito para um grupo gerado em um diretório local, ou para um grupo dinâmico.  Se você quiser alterar um grupo que se origina no local, em seguida, baixe os resultados e aplicar essas alterações para a representação do grupo no diretório.

## <a name="downloading-the-results-of-the-review"></a>Download dos resultados da revisão

Para recuperar os resultados da revisão, mude para a guia Aprovações e clique no botão **Baixar**.  O arquivo CSV resultante pode ser exibido no Excel ou em outros programas que abrem os arquivos CSV.

## <a name="optional-deleting-a-review"></a>Opcional: exclusão de revisão
Se você não estiver mais interessado na revisão, exclua-a. O botão **Excluir** remove a revisão do Azure AD.

> [!IMPORTANT]
> Não há nenhum aviso antes da exclusão, portanto, certifique-se de que deseja excluir a revisão.
> 
> 

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar o acesso do usuário com revisões de acesso ao Azure AD](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Gerenciar o acesso de convidado com revisões de acesso do Azure AD](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Gerenciar os programas e os controles para análises de acesso do Azure AD](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Como criar uma revisão de acesso para os membros de um grupo ou para o acesso a um aplicativo](active-directory-azure-ad-controls-create-access-review.md)
- [Criar uma revisão de acesso de usuários em uma função administrativa do Azure AD](active-directory-privileged-identity-management-how-to-start-security-review.md)
