---
title: Perguntas frequentes e solução de problemas de unidades administrativas-Azure Active Directory | Microsoft Docs
description: Investigue as unidades administrativas para conceder permissões com escopo restrito no Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1f41dca3b52ce75ba2342506f621cca0618a3bf
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565879"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Unidades administrativas do Azure AD: solução de problemas e perguntas frequentes

Para um controle administrativo mais granular no Azure Active Directory (AD do Azure), você pode atribuir usuários a uma função do Azure AD com um escopo limitado a uma ou mais unidades administrativas. Para ver os scripts de exemplo do PowerShell para tarefas comuns, consulte [trabalhar com unidades administrativas](/powershell/azure/active-directory/working-with-administrative-units).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: por que não posso criar uma unidade administrativa?**

**R:** Somente um administrador *global* ou *administrador de função com privilégios* pode criar uma unidade administrativa no Azure AD. Verifique se o usuário que está tentando criar a unidade administrativa é atribuído à função *administrador global* ou *administrador de função privilegiada* .

**P: adicionei um grupo a uma unidade administrativa. Por que os membros do grupo ainda não estão aparecendo lá?**

**R:** Quando você adiciona um grupo a uma unidade administrativa, isso não resulta na adição de todos os membros do grupo a ele. Os usuários devem ser atribuídos diretamente à unidade administrativa.

**P: Acabei de adicionar (ou remover) um membro da unidade administrativa. Por que o membro não está aparecendo (ou ainda aparecendo) na interface do usuário?**

**R:** Às vezes, a adição ou remoção de um ou mais membros de uma unidade administrativa pode levar alguns minutos para ser refletida no painel de **unidades administrativas** . Como alternativa, você pode ir diretamente para as propriedades do recurso associado e ver se a ação foi concluída. Para obter mais informações sobre usuários e grupos em unidades administrativas, consulte [exibir uma lista de unidades administrativas para um usuário](admin-units-add-manage-users.md) e [exibir uma lista de unidades administrativas para um grupo](admin-units-add-manage-groups.md).

**P: sou administrador de senha delegado em uma unidade administrativa. Por que não consigo redefinir a senha de um usuário específico?**

**R:** Como administrador de uma unidade administrativa, você pode redefinir senhas somente para usuários atribuídos à sua unidade administrativa. Verifique se o usuário cuja redefinição de senha está falhando pertence à unidade administrativa à qual você foi atribuído. Se o usuário pertencer à mesma unidade administrativa, mas você ainda não puder redefinir a senha do usuário, verifique as funções atribuídas ao usuário. 

Para evitar uma elevação de privilégio, um administrador com escopo de unidade administrativa não pode redefinir a senha de um usuário que está atribuído a uma função com um escopo de toda a organização.

**P: por que as unidades administrativas são necessárias? Não poderíamos ter usado grupos de segurança como a maneira de definir um escopo?**

**R:** Os grupos de segurança têm um modelo de finalidade e autorização existente. Um *administrador de usuário*, por exemplo, pode gerenciar a associação de todos os grupos de segurança na organização do Azure AD. A função pode usar grupos para gerenciar o acesso a aplicativos como o Salesforce. Um *administrador de usuário* não deve ser capaz de gerenciar o próprio modelo de delegação, que seria o resultado se os grupos de segurança fossem estendidos para dar suporte a cenários de "Agrupamento de recursos". 

As unidades administrativas, como as unidades organizacionais no Windows Server Active Directory, têm a finalidade de fornecer uma maneira de delimitar a administração de uma grande variedade de objetos de diretório. Os próprios grupos de segurança podem ser membros de escopos de recursos. Usar grupos de segurança para definir o conjunto de grupos de segurança que um administrador pode gerenciar pode se tornar confuso.

**P: o que significa adicionar um grupo a uma unidade administrativa?**

**R:** A adição de um grupo a uma unidade administrativa coloca o grupo em si no escopo de gerenciamento de qualquer *administrador de usuário* que também esteja no escopo dessa unidade administrativa. Os administradores de usuários da unidade administrativa podem gerenciar o nome e a associação do grupo em si. Ele não concede permissões de *administrador de usuário* para gerenciar os usuários do grupo (por exemplo, para redefinir suas senhas). Para conceder ao *administrador do usuário* a capacidade de gerenciar usuários, os usuários precisam ser membros diretos da unidade administrativa.

**P: um recurso (usuário ou grupo) pode ser membro de mais de uma unidade administrativa?**

**R:** Sim, um recurso pode ser membro de mais de uma unidade administrativa. O recurso pode ser gerenciado por todos os administradores com escopo de unidade administrativa e em toda a organização que têm permissões sobre o recurso.

**P: as unidades administrativas estão disponíveis em organizações B2C?**

**R:** Não, as unidades administrativas não estão disponíveis para organizações B2C.

**P: há suporte para unidades administrativas aninhadas?**

**R:** Não, não há suporte para unidades administrativas aninhadas.

**P: há suporte para unidades administrativas no PowerShell e no API do Graph?**

**R:** Sim. Você encontrará suporte para unidades administrativas na [documentação do cmdlet do PowerShell](/powershell/module/Azuread/) e [scripts de exemplo](/powershell/azure/active-directory/working-with-administrative-units).

Encontre suporte para o [tipo de recurso administrativeUnit](/graph/api/resources/administrativeunit) em Microsoft Graph.

## <a name="next-steps"></a>Próximas etapas

- [Restringir o escopo para funções usando unidades administrativas](administrative-units.md)
- [Gerenciar unidades administrativas](admin-units-manage.md)
