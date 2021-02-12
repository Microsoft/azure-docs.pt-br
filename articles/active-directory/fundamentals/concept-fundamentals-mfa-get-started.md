---
title: Autenticação multifator do Azure AD para sua organização-Azure Active Directory
description: Saiba mais sobre os recursos disponíveis da autenticação multifator do Azure AD para sua organização com base em seu modelo de licença
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: daveba
author: daveba
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85e7f02f8cbda6218396bf4a9a4654a113b7817c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836735"
---
# <a name="overview-of-azure-ad-multi-factor-authentication-for-your-organization"></a>Visão geral da autenticação multifator do Azure AD para sua organização

Há várias maneiras de habilitar a autenticação multifator do Azure AD para seus usuários do Azure Active Directory (AD) com base nas licenças que sua organização possui. 

![Investigar sinais e impor a MFA, se necessário](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Com base em nossos estudos, sua conta tem uma probabilidade mais de 99,9% menor de ser comprometida se você usar a MFA (autenticação multifator).

Então, como a sua organização ativa a MFA mesmo gratuitamente antes de se tornar uma estatística?

## <a name="free-option"></a>Opção gratuita

Os clientes que estão aproveitando os benefícios gratuitos do Azure AD podem usar [padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md) para habilitar a autenticação multifator no ambiente.

## <a name="microsoft-365-business-e3-or-e5"></a>Microsoft 365 Business, E3 ou E5

Para clientes com Microsoft 365, há duas opções:

* A autenticação multifator do Azure AD está habilitada ou desabilitada para todos os usuários, para todos os eventos de entrada. Não é possível habilitar a autenticação multifator para um subconjunto de usuários ou somente em determinados cenários. O gerenciamento é por meio do portal do Office 365. 
* Para ter uma experiência de usuário aprimorada, atualize para o Azure AD Premium P1 ou P2 e use o acesso condicional. Para obter mais informações, consulte proteger recursos de Microsoft 365 com a autenticação multifator.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Para clientes com Azure AD Premium P1 ou licenças semelhantes que incluem essa funcionalidade, como Enterprise Mobility + Security E3, Microsoft 365 F1 ou Microsoft 365 E3: 

Use o [Acesso condicional do Azure AD](../authentication/tutorial-enable-azure-mfa.md) para solicitar aos usuários a autenticação multifator durante determinados cenários ou eventos para atender às suas necessidades de negócios.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Para clientes com Azure AD Premium P2 ou licenças semelhantes que incluem essa funcionalidade, como Enterprise Mobility + Security E5 ou Microsoft 365 E5: 

Fornece a posição de segurança mais forte e a melhor experiência do usuário. Adiciona [Acesso condicional baseado em risco](../conditional-access/howto-conditional-access-policy-risk.md) aos recursos do Azure AD Premium P1 que se adapta aos padrões do usuário e minimiza os prompts da autenticação multifator.

## <a name="authentication-methods"></a>Métodos de autenticação

| Método | Padrões de segurança | Todos os outros métodos |
| --- | --- | --- |
| Notificação pelo aplicativo móvel | X | X |
| O código de verificação do aplicativo móvel ou token de hardware |   | X |
| Mensagem de texto para telefone |   | X |
| Ligue para o telefone |   | X |

## <a name="next-steps"></a>Próximas etapas

Para começar, consulte o tutorial para [proteger eventos de entrada do usuário com a autenticação multifator do Azure ad](../authentication/tutorial-enable-azure-mfa.md).

Para obter mais informações sobre licenciamento, consulte [recursos e licenças para a autenticação multifator do Azure ad](../authentication/concept-mfa-licensing.md).
