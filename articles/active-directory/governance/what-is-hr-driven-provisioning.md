---
title: O que é provisionamento controlado por RH com o Azure Active Directory? | Microsoft Docs
description: Descreve a visão geral do provisionamento controlado por RH.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ffe8c1397525348e472e965a407909fee36152d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168440"
---
# <a name="what-is-hr-driven-provisioning"></a>O que é provisionamento controlado por RH?

![Provisionamento de RH](./media/what-is-hr-driven-provisioning/cloud2a.png)

O provisionamento controlado por RH é o processo de criar identidades digitais com base em um sistema de recursos humanos.  Os sistemas de RH se tornam o início de autoridade para essas identidades digitais recém-criadas e geralmente é o ponto de partida para vários processos de provisionamento.  Por exemplo, se um novo funcionário ingressar na sua empresa, ele será criado no sistema de recursos humanos.  A criação dispara o provisionamento de uma conta de usuário para o Active Directory e o Azure AD Connect provisiona essa conta para o Azure AD etc.

O provisionamento controlado por RH pode ser baseado no local ou na nuvem.

## <a name="on-premises-based-hr-provisioning"></a>Provisionamento de RH baseado no local
O provisionamento de RH baseado no local é feito usando um sistema de RH local e um meio de provisionar novas identidades digitais.

Os sistemas de RH vêm em uma variedade de pacotes e conjuntos de software e podem usar servidores SQL, diretórios LDAP etc.

No momento, as soluções de provisionamento de RH locais da Microsoft usam o Microsoft Identity Manager para disparar o provisionamento quando uma identidade é criada nesses sistemas de RH.

Usando o MIM, você pode provisionar usuários dos seus sistemas de RH locais no Active Directory ou no Azure AD.

Para obter informações sobre o Microsoft Identity Manager e os sistemas aos quais ele dá suporte, confira a documentação [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016).

[!INCLUDE [active-directory-hr-provisioning.md](../../../includes/active-directory-hr-provisioning.md)]



## <a name="next-steps"></a>Próximas etapas 
- [O que é o gerenciamento do ciclo de vida de identidades](what-is-identity-lifecycle-management.md)
- [O que é provisionamento?](what-is-provisioning.md)
- [O que é provisionamento de aplicativos?](what-is-app-provisioning.md)
- [O que é provisionamento entre diretórios?](what-is-inter-directory-provisioning.md)