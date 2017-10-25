---
title: "Gestão do acesso do usuário com revisões de acesso do Azure AD | Microsoft Docs"
description: "Saiba como gerenciar o acesso de usuários como membros de um grupo ou a atribuição de um aplicativo com revisões de acesso do Azure Active Directory"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 0459eb5cc71939202c8491f6b2714e28bd8e202d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Gestão do acesso do usuário com revisões de acesso do Azure AD

Com o Azure Active Directory, você pode garantir facilmente que os usuários tenham acesso adequado ao convidar usuários, ou um tomador de decisões, para participar de uma revisão de acesso e renovar (ou "atestar") o certificado do acesso do usuário.  Revisores podem fornecer a sua entrada na necessidade de cada usuário de acesso contínuo, com base nas sugestões do Azure AD. Quando uma revisão de acesso for concluída, você poderá alterar e remover o acesso de usuários que não precisam mais dela.

> [!NOTE]
> Se você quiser revisar somente o acesso de usuários convidados, em vez de revisar todos os tipos de acesso de usuários, confira [Gerenciamento de acesso de usuários convidados com revisões de acesso](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md).  E se você desejar revisar a associação do usuário em funções administrativas, como o administrador global, confira [Como iniciar uma revisão de acesso no Azure AD PIM](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Pré-requisitos 

As revisões de acesso estão disponíveis com a edição Premium P2 do Azure Active Directory, inclusa no EMS E5. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).  Cada usuário que interage com esse recurso, para criar uma análise, revisar o acesso ou aplicar uma revisão, deve ter uma licença.


## <a name="creating-and-performing-an-access-review"></a>Criação e execução de uma revisão de acesso

Você pode ter um ou mais usuários como revisores de uma revisão de acesso.  

1. Selecione um grupo no Azure Active Directory que tenha um ou mais membros, ou um aplicativo conectado ao Azure Active Directory que tenha um ou mais usuários atribuídos a ele. 
2. Decida se cada usuário revisará o próprio acesso ou se um ou mais usuários revisarão o acesso de todos.
3. Permita que as revisões de acesso apareçam nos painéis de acesso do revisor.  Como administrador global, acesse a [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 
4. Inicie a revisão de acesso. Leia mais em [Como criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md).
5. Solicite que os revisores forneçam a entrada. Por padrão, todos receberão um email do Azure AD com um link para o painel de acesso, onde poderão [executar sua revisão de acesso](active-directory-azure-ad-controls-perform-access-review.md).
6. Se os revisores não forneceram a entrada, você pode pedir ao Azure AD para enviar um lembrete a eles.  Por padrão, o Azure AD enviará automaticamente um lembrete na metade para o fim do prazo final para revisores que ainda não responderam.
7. Depois dos revisores enviarem a entrada, pare a revisão de acesso e aplique as alterações. Leia mais em [Como concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Próximas etapas

- [Como criar uma revisão de acesso para os membros de um grupo ou para o acesso a um aplicativo](active-directory-azure-ad-controls-create-access-review.md)




