---
title: "O que é o licenciamento baseado em grupo no Azure Active Directory? | Microsoft Docs"
description: "Descrição de licenciamento baseado em grupo do Azure Active Directory, como ele funciona e práticas recomendadas"
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/29/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: d6c76af713a73b965e87a2f470125f2e65565975
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Noções básicas de licenciamento baseado em grupo no Azure Active Directory

O uso de serviços de nuvem pagos da Microsoft, como Office 365, Enterprise Mobility + Security, Dynamics CRM e outros produtos similares, exige licenças. Essas licenças são atribuídas a cada usuário que precisa acessar esses serviços. Para gerenciar licenças, os administradores usam um dos portais de gerenciamento (Office ou Azure) e cmdlets do PowerShell. O Azure AD (Azure Active Directory) é a infraestrutura subjacente que dá suporte ao gerenciamento de identidade para todos os serviços de nuvem da Microsoft. O Azure AD armazena informações sobre estados de atribuição de licença para usuários.

Até agora, as licenças podiam ser atribuídas apenas no nível de usuário individual, o que pode dificultar o gerenciamento em larga escala. Por exemplo, para adicionar ou remover licenças de usuário com base nas alterações organizacionais, como usuários ingressando na organização ou em um departamento ou saindo deles, com frequência um administrador deve escrever um script complexo do PowerShell. Esse script faz chamadas individuais para o serviço de nuvem.

Para enfrentar esses desafios, o Azure AD agora inclui licenciamento baseado em grupo. Você pode atribuir uma ou mais licenças de produto a um grupo. O Azure AD faz com que as licenças sejam atribuídas a todos os membros do grupo. Os novos membros que ingressam no grupo recebem as licenças apropriadas. Quando eles deixam o grupo, essas licenças são removidas. Isso elimina a necessidade de automatizar o gerenciamento de licenças por meio do PowerShell para refletir as alterações na estrutura da organização e dos departamentos por usuário.

## <a name="features"></a>Recursos

Aqui estão os principais recursos do licenciamento baseado em grupo:

- As licenças podem ser atribuídas a qualquer grupo de segurança no Azure AD. Os grupos de segurança podem ser sincronizados localmente usando o Azure AD Connect. Você também pode criar grupos de segurança diretamente no Azure AD (também chamados de grupos somente de nuvem) ou automaticamente por meio do recurso de grupo dinâmico do Azure AD.

- Quando uma licença de produto é atribuída a um grupo, o administrador pode desabilitar um ou mais planos de serviço no produto. Normalmente, isso é feito quando a organização ainda não está pronta para começar a usar um serviço incluído em um produto. Por exemplo, o administrador pode atribuir o Office 365 a um departamento, mas desabilitar o serviço do Yammer temporariamente.

- Todos os serviços de nuvem da Microsoft que exigem licenciamento no nível do usuário têm suporte. Isso inclui todos os produtos do Office 365, Enterprise Mobility + Security e Dynamics CRM.

- Atualmente, o licenciamento baseado em grupo está disponível apenas por meio [do portal do Azure](https://portal.azure.com). Se você usa outros portais de gerenciamento principalmente para gerenciamento de usuário e de grupo, como o portal do Office 365, pode continuar a fazê-lo. Mas você deve usar o portal do Azure para gerenciar licenças no nível do grupo.

- O Azure AD gerencia automaticamente as modificações de licença que resultam das alterações de associação de grupo. Normalmente, as modificações de licença entram em vigor dentro de minutos após uma alteração de associação.

- Um usuário pode ser membro de vários grupos com políticas de licença especificadas. Um usuário também pode ter algumas licenças que foram atribuídas diretamente, fora de todos os grupos. O estado do usuário resultante é uma combinação de todas as licenças de produto e de serviço atribuídas.

- Em alguns casos, as licenças não podem ser atribuídas a um usuário. Por exemplo, talvez não haja licenças suficientes disponíveis no locatário ou serviços conflitantes foram atribuídos ao mesmo tempo. Os administradores têm acesso às informações sobre os usuários que não puderam receber licenças de grupo do Azure AD. Eles podem agir corretivamente com base nessas informações.

- Durante a visualização pública, uma assinatura paga ou de avaliação para as edições Azure AD Basic ou Premium é necessária no locatário para usar o gerenciamento de licenças baseado em grupo.

## <a name="your-feedback-is-welcome"></a>Agradecemos seus comentários!

Se você tiver comentários ou solicitações de recurso, compartilhe-os conosco usando [este fórum](https://feedback.azure.com/forums/169401-azure-active-directory/category/317677-group-based-licensing).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outros cenários de gerenciamento de licenças por meio de licenciamento baseado em grupo, confira:

* [Introdução às licenças do Azure Active Directory](active-directory-licensing-get-started-azure-portal.md)
* [Atribuição de licenças a um grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](active-directory-licensing-group-advanced.md)
