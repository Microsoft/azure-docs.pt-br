---
title: Adicionar convidados B2B sem um link de convite ou email-Azure AD
description: É possível permitir que um usuário convidado adicione outros usuários convidados ao Azure AD sem resgatar um convite na colaboração B2B do Azure Active Directory.
documentationcenter: ''
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: b18279e44bc5a3fd668d2ec4af6be29229af1b6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87908109"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Adicionar usuários convidados de colaboração B2B sem um link ou email de convite

Agora você pode convidar usuários convidados enviando um [link direto](redemption-experience.md#redemption-through-a-direct-link) para um aplicativo compartilhado. Com esse método, usuários convidados não precisam mais usar o email de convite, exceto em alguns casos especiais. Um usuário convidado clica no link do aplicativo, analisa e aceita os termos de privacidade e, em seguida, acessa diretamente o aplicativo. Para obter mais informações, consulte [Resgate do convite de colaboração B2B](redemption-experience.md).

Antes desse novo método ser disponibilizado, era possível convidar usuários sem a necessidade de um email de convite, adicionando um emissor do convite (de sua organização ou de uma organização parceira) à função do diretório **emissor do convite convidado** e, em seguida, solicitando ao emissor do convite que adicionasse usuários convidados ao diretório, grupos ou aplicativos por meio da interface do usuário ou do PowerShell. (Se estiver usando o PowerShell, você poderá suprimir o email de convite completamente). Por exemplo:

1. Um usuário na organização host (por exemplo, WoodGrove) convida um usuário da organização parceira (por exemplo, Sam@litware.com) como Convidado.
2. O administrador da organização host [define as políticas](delegate-invitations.md) que permitem ao Sam identificar e adicionar outros usuários da organização parceira (Litware). (Sam deve ser adicionado à função **emissor do convite convidado**.)
3. Agora, Sam pode adicionar outros usuários da Litware ao diretório, grupos ou aplicativos da WoodGrove sem a necessidade de resgatar os convites. Se Davi tiver os privilégios apropriados de enumeração na Litware, isso ocorrerá automaticamente.
 
Esse método original ainda funciona. No entanto, há uma pequena diferença no comportamento. Se você usar o PowerShell, observará que uma conta de convidado agora tem um status **PendingAcceptance** em vez de mostrar imediatamente **Accepted**. Embora o status esteja pendente, o usuário convidado ainda pode entrar e acessar o aplicativo sem clicar em um link de convite por email. O status pendente significa que o usuário ainda não passou pela [experiência de consentimento](redemption-experience.md#consent-experience-for-the-guest), em que aceita os termos de privacidade da organização que o convidou. O usuário convidado vê esta tela de consentimento quando entra pela primeira vez. 

Se você convidar um usuário para o diretório, o convidado deverá acessar o portal do Azure do recurso específico do locatário diretamente na URL (como https://portal.azure.com/*resourcetenant*.onmicrosoft.com) para exibir e aceitar os termos de privacidade.

## <a name="next-steps"></a>Próximas etapas

- [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Delegar convites para a colaboração do Azure Active Directory B2B](delegate-invitations.md)
- [Como os operadores de informação adicionam usuários de colaboração B2B?](add-users-information-worker.md)

