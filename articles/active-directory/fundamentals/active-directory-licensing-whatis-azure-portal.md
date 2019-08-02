---
title: O que é o licenciamento baseado em grupo – Azure Active Directory | Microsoft Docs
description: Aprenda sobre o licenciamento baseado em grupo do Azure Active Directory, incluindo como ele funciona e as melhores práticas.
services: active-directory
keywords: Licenciamento do AD do Azure
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d41c8edf3985e77e0f67a54013d1b59abbaf72f1
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297556"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>O que é o licenciamento baseado em grupo no Azure Active Directory?

Os serviços de nuvem pagos da Microsoft, como Office 365, Enterprise Mobility + Security, Dynamics 365 e outros produtos similares, exigem licenças. Essas licenças são atribuídas a cada usuário que precisa acessar esses serviços. Para gerenciar licenças, os administradores usam um dos portais de gerenciamento (Office ou Azure) e cmdlets do PowerShell. O Azure AD (Azure Active Directory) é a infraestrutura subjacente que dá suporte ao gerenciamento de identidade para todos os serviços de nuvem da Microsoft. O Azure AD armazena informações sobre estados de atribuição de licença para usuários.

Até agora, as licenças podiam ser atribuídas apenas no nível de usuário individual, o que pode dificultar o gerenciamento em larga escala. Por exemplo, para adicionar ou remover licenças de usuário com base nas alterações organizacionais, como usuários ingressando na organização ou em um departamento ou saindo deles, com frequência um administrador deve escrever um script complexo do PowerShell. Esse script faz chamadas individuais para o serviço de nuvem.

Para enfrentar esses desafios, o Azure AD agora inclui licenciamento baseado em grupo. Você pode atribuir uma ou mais licenças de produto a um grupo. O Azure AD faz com que as licenças sejam atribuídas a todos os membros do grupo. Os novos membros que ingressam no grupo recebem as licenças apropriadas. Quando eles deixam o grupo, essas licenças são removidas. Esse gerenciamento de licenciamento elimina a necessidade de automatizar o gerenciamento de licenças por meio do PowerShell para refletir as alterações na estrutura da organização e dos departamentos por usuário.

## <a name="licensing-requirements"></a>Requisitos de licenciamento
É necessário ter uma das seguintes licenças para usar o licenciamento baseado em grupo:

- Assinatura paga ou de avaliação para Azure AD Basic e acima

- Edição paga ou de avaliação do Office 365 Enterprise E3 ou Office 365 a3 ou Office 365 GCC G3 e superior

### <a name="required-number-of-licenses"></a>Número necessário de licenças
Para os grupos com uma licença atribuída, também é necessário ter uma licença para cada membro exclusivo. Embora você não precise atribuir uma licença a cada membro do grupo, você deve ter, pelo menos, licenças suficientes para incluir todos os membros. Por exemplo, se você tiver 1.000 membros exclusivos que façam parte de grupos licenciados em seu locatário, deverá ter, pelo menos, 1.000 licenças para cumprir o contrato de licenciamento.

## <a name="features"></a>Recursos

Aqui estão os principais recursos do licenciamento baseado em grupo:

- As licenças podem ser atribuídas a qualquer grupo de segurança no Azure AD. Os grupos de segurança podem ser sincronizados localmente usando o Azure AD Connect. Você também pode criar grupos de segurança diretamente no Azure AD (também chamados de grupos somente de nuvem) ou automaticamente por meio do recurso de grupo dinâmico do Azure AD.

- Quando uma licença de produto é atribuída a um grupo, o administrador pode desabilitar um ou mais planos de serviço no produto. Normalmente, essa atribuição é feita quando a organização ainda não está pronta para começar a usar um serviço incluído em um produto. Por exemplo, o administrador pode atribuir o Office 365 a um departamento, mas desabilitar o serviço do Yammer temporariamente.

- Todos os serviços de nuvem da Microsoft que exigem licenciamento no nível do usuário têm suporte. Esse suporte inclui todos os produtos do Office 365, Enterprise Mobility + Security e Dynamics 365.

- Atualmente, o licenciamento baseado em grupo está disponível apenas por meio do [portal do Azure](https://portal.azure.com). Se você usa principalmente outros portais de gerenciamento para gerenciamento de usuários e grupos, como o [centro de administração Microsoft 365](https://admin.microsoft.com), você pode continuar a fazer isso. Mas você deve usar o portal do Azure para gerenciar licenças no nível do grupo.

- O Azure AD gerencia automaticamente as modificações de licença que resultam das alterações de associação de grupo. Normalmente, as modificações de licença entram em vigor dentro de minutos após uma alteração de associação.

- Um usuário pode ser membro de vários grupos com políticas de licença especificadas. Um usuário também pode ter algumas licenças que foram atribuídas diretamente, fora de todos os grupos. O estado do usuário resultante é uma combinação de todas as licenças de produto e de serviço atribuídas. Se um usuário receber a mesma licença de várias fontes, a licença será consumida apenas uma vez.

- Em alguns casos, as licenças não podem ser atribuídas a um usuário. Por exemplo, talvez não haja licenças suficientes disponíveis no locatário ou serviços conflitantes foram atribuídos ao mesmo tempo. Os administradores têm acesso às informações sobre os usuários que não puderam receber licenças de grupo do Azure AD. Eles podem agir corretivamente com base nessas informações.

## <a name="your-feedback-is-welcome"></a>Agradecemos seus comentários!

Caso tenha comentários ou solicitações de recurso, compartilhe-os conosco usando [o fórum de administradores do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outros cenários de gerenciamento de licenças por meio de licenciamento baseado em grupo, confira:

* [Atribuição de licenças a um grupo no Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Como migrar usuários entre licenças de produto usando o licenciamento baseado em grupo no Microsoft Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
* [Exemplos do PowerShell para licenciamento baseado em grupo no Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
