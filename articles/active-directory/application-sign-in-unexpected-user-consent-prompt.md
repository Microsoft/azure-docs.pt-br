---
title: "Solicitação de consentimento inesperada ao entrar em um aplicativo | Microsoft Docs"
description: "Como solucionar problemas quando um usuário vê uma solicitação de consentimento para um aplicativo integrado com o Azure AD inesperada"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: e5b823e1251a7221f73efe6838d439f827f9665d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Um usuário vê uma solicitação de consentimento inesperada ao entrar em um aplicativo

Muitos aplicativos que se integram com o Azure Active Directory exigem permissões a vários recursos para serem executados. Quando esses recursos também são integrados com o Azure Active Directory, as permissões para acessá-los são solicitadas usando a estrutura de consentimento do Azure AD. 

Isso resulta em uma solicitação de consentimento que é exibida na primeira vez em que um aplicativo é usado o que, frequentemente, é uma operação única. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Cenários nos quais os usuários visualizam solicitações de consentimento

Solicitações adicionais podem ser esperadas em vários cenários:

* O conjunto de permissões exigidas pelo aplicativo foi alterado.

* O usuário que originalmente consentiu para o aplicativo não era um administrador e agora um Usuário diferente (não administrador) está usando o aplicativo pela primeira vez.

* O usuário que originalmente consentiu para o aplicativo era um administrador, mas ele não consentiu em nome de toda a organização.

* O aplicativo está usando [consentimento incremental e dinâmico](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) para solicitar permissões adicionais após consentimento inicialmente concedido. Isso é frequentemente usado quando recursos opcionais de um aplicativo adicional exigem permissões além das exigidas para a funcionalidade de linha de base.

* O consentimento foi revogado após ter sido inicialmente concedido.

* O desenvolvedor configurou o aplicativo para solicitar uma solicitação de consentimento sempre que for usado (observação: essa não é a prática recomendada).

## <a name="next-steps"></a>Próximas etapas

-   [Aplicativos, permissões e consentimento no Azure Active Directory (ponto de extremidade v1.0)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Escopos, permissões e consentimento no Azure Active Directory (ponto de extremidade v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


