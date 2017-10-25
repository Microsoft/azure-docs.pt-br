---
title: "Criar uma revisão de acesso de membros de um grupo ou usuários com acesso a um aplicativo no Azure AD | Microsoft Docs"
description: "Saiba como criar uma revisão de acesso para os membros de um grupo ou usuários para o acesso a um aplicativo."
services: active-directory
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
ms.openlocfilehash: c2c5aee3db11255be5a6fe405424c2cbb92c5b95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Criar uma revisão de acesso de membros do grupo ou acesso do aplicativo com o Azure AD

As atribuições de acesso se tornam "obsoletas" quando os usuários possuem um acesso que não precisam mais.  Para reduzir os riscos associados às atribuições de acesso obsoletas, os administradores podem solicitar uma revisão dos membros do grupo ou os usuários atribuídos a um aplicativo por meio da criação de uma revisão de acesso. As guias para [gerenciamento de acesso de usuário](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) e [gerenciamento de acesso de convidado](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md) fornecem mais informações sobre esses cenários.  

## <a name="how-to-create-an-access-review"></a>Como criar uma revisão de acesso


1. Como administrador global, vá para a [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) e altere para a guia **Programas**.
2. Clique no programa que armazenará o controle de revisão de acesso que você deseja criar.  Há um programa, "Programa padrão", sempre presente, ou você pode criar um programa diferente.  Por exemplo, você pode optar por ter um programa para cada iniciativa de conformidade ou meta de negócios.
3. Dentro do programa, clique em Controles e, em seguida, clique no botão **Adicionar** para adicionar um controle.
4. Cada revisão de acesso precisa ter um nome e opcionalmente pode também ter uma descrição.  O nome é mostrado para os revisores.  
5. Por padrão, uma revisão de acesso é iniciada no mesmo dia em que é criada, e é agendada para finalização em um mês.  Você pode alterar as datas de início e término para iniciar uma revisão de acesso numa data futura e que dure quantos dias você desejar.
6. As revisões de acesso podem ser de membros de um grupo ou usuários que foram atribuídos a um aplicativo.  Você pode detalhar ainda mais o escopo da revisão de acesso para examinar apenas os usuários convidados que são membros (ou atribuídos ao aplicativo), em vez de examinar todos os usuários que são membros ou que possuem acesso ao aplicativo.
7. Você pode selecionar uma ou mais pessoas para examinar todos os usuários no escopo, ou você pode optar por permitir que os membros revisem os seus respectivos acessos.  Se o recurso for um grupo, você pode pedir que a revisão seja realizada pelos proprietários de grupo.  Você também pode exigir que os revisores forneçam um motivo ao aprovar o acesso.
8. Por fim, clique em **Iniciar**.


## <a name="managing-the-access-review"></a>Gerenciar a revisão de acesso

Por padrão, o Azure AD envia um email para os revisores logo após o início da análise.  Se você optar pelo não envio do email pelo Azure AD, certifique-se de informar aos revisores que eles devem concluir uma revisão de acesso pendente.  Você pode exibir as instruções para [como revisar o acesso](active-directory-azure-ad-controls-perform-access-review.md), ou se sua revisão for para convidados que revisam o próprio acesso, as instruções para [como revisar seu próprio acesso](active-directory-azure-ad-controls-perform-access-review.md).

Observe que se alguns revisores são convidados, os convidados serão apenas notificados por email se eles já tiverem aceitado seus respectivos convites.


Você pode acompanhar o andamento conforme os revisores concluem suas revisões no painel do Azure AD na seção de revisões de acesso. Nenhum direito de acesso será alterado no diretório até a [análise ser concluída](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Próximas etapas

Quando uma revisão de acesso é iniciada, o Azure AD enviará automaticamente aos revisores um email solicitando que eles revisem o acesso. Se um usuário não recebeu um email, você pode enviar para ele as instruções em [como analisar o acesso](active-directory-azure-ad-controls-perform-access-review.md).  

Depois que o período de revisão de acesso tiver acabado ou se o administrador tiver interrompido a revisão de acesso, siga as etapas em [concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md) para ver e aplicar os resultados.


