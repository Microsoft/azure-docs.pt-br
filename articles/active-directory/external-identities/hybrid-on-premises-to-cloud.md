---
title: Sincronizar contas de parceiros locais para nuvem como usuários B2B-Azure AD
description: Conceda aos parceiros externos gerenciados localmente acesso a recursos locais e da nuvem usando as mesmas credenciais com a colaboração B2B do Microsoft Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/03/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29aeca30e1fbdd28d3f69597d902a9b714056cd5
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575916"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Conceder acesso às contas de parceiros gerenciadas localmente aos recursos da nuvem usando a colaboração B2B do Azure AD

Antes do Microsoft Azure AD (Azure Active Directory), as organizações com sistemas de identidade locais gerenciavam tradicionalmente as contas de parceiros no diretório local. Em uma organização desse porte, ao começar a mover os aplicativos para o Microsoft Azure AD, você quer certificar-se de que seus parceiros poderão acessar os recursos necessários. Não importa se os recursos estão no local ou na nuvem. Além disso, você quer que os usuários parceiros possam usar as mesmas credenciais de entrada para ambos os recursos locais e do Microsoft Azure AD. 

Se você criar contas para os parceiros externos no diretório local (por exemplo, criar uma conta com um nome de entrada "wmoran" para um usuário externo chamado Wendy Moran no domínio partners.contoso.com), agora poderá sincronizar essas contas para a nuvem. Especificamente, você pode usar Azure AD Connect para sincronizar as contas de parceiro com a nuvem, que cria uma conta de usuário com UserType = convidado. Isso permite que os usuários parceiros acessem os recursos da nuvem usando as mesmas credenciais das contas locais, sem conceder mais acesso que os usuários precisam.

> [!NOTE]
> Consulte também como [convidar usuários internos para a colaboração B2B](invite-internal-users.md). Com esse recurso, você pode convidar usuários internos convidados a usar a colaboração B2B, independentemente de você ter sincronizado suas contas de seu diretório local para a nuvem. Depois que o usuário aceitar o convite para usar a colaboração B2B, ele poderá usar suas próprias identidades e credenciais para entrar nos recursos que você deseja que eles acessem. Você não precisará manter as senhas ou gerenciar ciclos de vida da conta.

## <a name="identify-unique-attributes-for-usertype"></a>Identificar os atributos exclusivos para UserType

Antes de habilitar a sincronização do atributo UserType, primeiro você deve decidir como o atributo UserType será derivado do Active Directory. Em outras palavras, quais parâmetros em seu ambiente local são exclusivos para seus colaboradores externos? Determine um parâmetro que distingua esses colaboradores externos dos membros de sua organização.

Duas abordagens comuns para isso são:

- Designe um atributo do Active Directory local não utilizado (por exemplo, extensionAttribute1) a ser usado como o atributo de origem. 
- Como alternativa, derive o valor do atributo UserType de outras propriedades. Por exemplo, você deseja sincronizar todos os usuários como convidados se seu local Active Directory atributo UserPrincipalName termina com o domínio *\@ Partners.contoso.com*.
 
Para mais detalhes sobre os requisitos de atributo, consulte [Habilitar a sincronização de UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Configurar o Azure AD Connect para sincronizar usuários com a nuvem

Depois de identificar o atributo exclusivo, você pode configurar Azure AD Connect para sincronizar esses usuários com a nuvem, que cria uma conta de usuário com UserType = convidado. De um ponto de vista de autorização, não há diferença entre esses usuários e os usuários B2B criados pelo processo de convite de colaboração do B2B do Azure Active Directory.

Para obter instruções de implementação, consulte [Habilitar a sincronização de UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Próximas etapas

- [Colaboração do Azure Active Directory B2B para organizações híbridas](hybrid-organizations.md)
- [Conceder aos usuários B2B do Microsoft Azure AD acesso aos aplicativos locais](hybrid-cloud-to-on-premises.md)
- Para uma visão geral do Azure AD Connect, consulte [Integrar seus diretórios locais ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

