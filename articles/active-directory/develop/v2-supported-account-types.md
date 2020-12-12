---
title: Tipos de conta com suporte-plataforma Microsoft Identity | Azure
description: Documentação conceitual sobre públicos-alvo e tipos de conta com suporte em aplicativos
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f0895cfc14b0237db8f2f762602fee949354e6cb
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347448"
---
# <a name="supported-account-types"></a>Tipos de conta com suporte

Este artigo explica quais tipos de conta (às vezes chamados de *públicos*) têm suporte nos aplicativos da plataforma de identidade da Microsoft.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>Tipos de conta na nuvem pública

Na nuvem pública Microsoft Azure, a maioria dos tipos de aplicativos pode conectar usuários com qualquer público-alvo:

- Se você estiver escrevendo um aplicativo de linha de negócios (LOB), poderá conectar usuários em sua própria organização. Esse aplicativo, às vezes, é chamado *de locatário único*.
- Se você for um ISV, poderá escrever um aplicativo que assine os usuários:

  - em qualquer organização. Esse aplicativo é chamado de aplicativo Web *multilocatário* . Às vezes, você lerá que ele conecta os usuários com suas contas corporativas ou de estudante.
  - Com suas contas corporativas ou de estudante ou da Microsoft pessoais.
  - Somente com contas pessoais da Microsoft.
    
- Se você estiver escrevendo um aplicativo de negócios para o consumidor, também poderá conectar usuários com suas identidades sociais usando Azure Active Directory B2C (Azure AD B2C).

## <a name="account-type-support-in-authentication-flows"></a>Suporte ao tipo de conta em fluxos de autenticação

Alguns tipos de conta não podem ser usados ​​com determinados fluxos de autenticação. Por exemplo, em aplicativos desktop, UWP ou daemon:

- Os aplicativos daemon podem ser usados somente com as organizações do Azure AD. Não faz sentido tentar usar aplicativos de daemon para manipular contas pessoais da Microsoft. O consentimento do administrador nunca será concedido.
- Você pode usar o fluxo de autenticação do Windows integrado somente com contas corporativas ou de estudante (em sua organização ou em qualquer organização). A autenticação integrada do Windows funciona com contas de domínio e requer que os computadores sejam ingressados no domínio ou no Azure AD. Esse fluxo não faz sentido para contas pessoais da Microsoft.
- A [concessão de credenciais de senha do proprietário do recurso](./v2-oauth-ropc.md) (nome de usuário/senha) não pode ser usada com contas pessoais da Microsoft. As contas pessoais da Microsoft exigem que o usuário contenha o acesso a recursos pessoais em cada sessão de entrada. É por isso que esse comportamento não é compatível com fluxos não interativos.

## <a name="account-types-in-national-clouds"></a>Tipos de conta em nuvens nacionais

Os aplicativos também podem conectar usuários em [nuvens nacionais](authentication-national-cloud.md). No entanto, as contas pessoais da Microsoft não têm suporte nessas nuvens. É por isso que os tipos de conta com suporte são reduzidos, para essas nuvens, para sua organização (locatário único) ou para quaisquer organizações (aplicativos multilocatários).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [aluguel em Azure Active Directory](./single-and-multi-tenant-apps.md).
- Saiba mais sobre as [nuvens nacionais](./authentication-national-cloud.md).
