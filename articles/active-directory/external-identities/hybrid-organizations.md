---
title: Colaboração B2B para organizações híbridas-Azure AD
description: Fornece aos parceiros acesso a ambos os recursos locais e na nuvem com a colaboração B2B do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 427f7ad4d6a1b9839b1197ef9f7ca15400ea0f59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87907970"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Colaboração B2B do Azure Active Directory para organizações híbridas

Com a colaboração B2B do Azure Active Directory (Azure AD), fica mais fácil conceder aos seus parceiros externos acesso a aplicativos e recursos da sua organização. Isso é válido mesmo em configurações híbridas nas quais haja recursos locais e baseados na nuvem. Não importa se você gerencia atualmente contas de parceiros externos localmente no seu sistema de identidade local ou se gerencia contas externas na nuvem como usuários B2B do Azure AD. Agora, é possível conceder a esses usuários acesso aos recursos em qualquer local, usando as mesmas credenciais de entrada para ambos os ambientes.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Conceda aos usuários B2B do Azure AD acesso aos seus aplicativos locais

Se sua organização utiliza recursos de colaboração B2B do Azure AD para convidar usuários de organizações parceiras a ingressar no Azure AD, você pode agora conceder a esses usuários B2B acesso a aplicativos locais.

Para aplicativos que utilizam autenticação baseada em SAML, é possível disponibilizar esses aplicativos aos usuários B2B por meio do portal do Azure, usando o proxy de aplicativo do Azure AD para autenticação.

Para aplicativos que utilizam autenticação integrada do Windows (IWA) com delegação restrita de Kerberos (KCD), deve ser usado também o proxy do Azure AD para autenticação. Contudo, para autorizações para trabalhar, é necessário um objeto de usuário no Active Directory local do Windows Server. Há dois métodos que podem ser usados para criar objetos de usuário local que representem usuários B2B convidados.

- Você pode usar o gerenciador de identidades da Microsoft (MIM) 2016 SP1 e o agente de gerenciamento do MIM para Microsoft Graph.
- Você pode usar um script do PowerShell. (Essa solução não exige o MIM.)

Para obter detalhes sobre como implementar essas soluções, consulte [Conceder aos usuários B2B do Azure AD acesso aos seus aplicativos locais](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Conceder às contas de parceiros gerenciadas localmente acesso a recursos na nuvem

Antes do Azure AD, as organizações que possuem sistemas de identidade local gerenciavam tradicionalmente contas de parceiros em seus diretórios locais. Se você for esse tipo de organização, certifique-se de manter o acesso aos seus parceiros após migrar aplicativos e outros recursos para a nuvem. O ideal é que esses usuários utilizem o mesmo conjunto de credenciais para acessar os recursos locais e na nuvem. 

Hoje, oferecemos métodos com os quais é possível usar o Azure AD Connect para sincronizar essas contas locais com a nuvem na forma de "usuários convidados", em que as contas se comportam exatamente como usuários B2B do Azure AD.

Para ajudar a proteger os dados da sua empresa, você pode controlar o acesso a apenas os recursos certos e configurar políticas de autorização que tratem esses usuários convidados de maneira diferente à de seus funcionários.

Para obter detalhes sobre a implementação, consulte [Conceder às contas de parceiros gerenciadas localmente acesso a recursos na nuvem utilizando a colaboração B2B do Azure AD](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Próximas etapas

- [Conceder aos usuários B2B do Microsoft Azure AD acesso aos aplicativos locais](hybrid-cloud-to-on-premises.md)
- [Conceder acesso às contas de parceiros gerenciadas localmente para recursos da nuvem usando a colaboração B2B do Microsoft Azure AD](hybrid-on-premises-to-cloud.md)


