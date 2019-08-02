---
title: Visão geral da plataforma de identidade da Microsoft (v2.0) – Azure
description: Saiba mais sobre a plataforma e o ponto de extremidade da plataforma de identidade da Microsoft (v2.0).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93e4337f0593933a4e877f391df8132a9b2cd4af
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702693"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Visão geral da plataforma de identidade da Microsoft (v2.0)

A plataforma de identidade da Microsoft é uma evolução da plataforma de desenvolvedor do Azure AD (Azure Active Directory). Ela permite que os desenvolvedores criem aplicativos que conectem todas as identidades da Microsoft e obtenham tokens para chamar APIs da Microsoft, como o Microsoft Graph ou APIs que os desenvolvedores criaram. A plataforma de identidade da Microsoft consiste em:

- **Serviço de autenticação em conformidade com o padrão do OAuth 2.0 e do OpenID Connect** que permite aos desenvolvedores autenticar qualquer identidade da Microsoft, incluindo:
  - Contas corporativa ou de estudante (provisionadas pelo Azure AD)
  - Contas pessoais da Microsoft (como Skype, Xbox e Outlook.com)
  - Contas sociais ou locais (por meio do Azure AD B2C)
- **Bibliotecas open-source**: MSAL (Bibliotecas de Autenticação da Microsoft) e suporte a outras bibliotecas em conformidade com o padrão
- **Portal de gerenciamento de aplicativos**: Uma experiência de registro e configuração criada no portal do Azure, juntamente com todas as outras funcionalidades de gerenciamento do Azure.
- **API de configuração de aplicativos e PowerShell**: que permitem a configuração programática de seus aplicativos por meio da API REST (Microsoft Graph e Azure Active Directory Graph 1.6) e do PowerShell, para que você possa automatizar suas tarefas de DevOps.
- **Conteúdo para desenvolvedores**: documentação conceitual e de referência, exemplos de início rápido, exemplos de código, tutoriais e guias de instruções.

Para desenvolvedores, a plataforma de identidade da Microsoft oferece uma integração perfeita em inovações no espaço de identidade e segurança, como autenticação sem senha, autenticação Step-up e Acesso Condicional.  Você não precisa implementar essa funcionalidade por conta própria: aplicativos integrados à plataforma de identidade da Microsoft se beneficiam nativamente dessas inovações.

Com a plataforma de identidade da Microsoft, você pode escrever código uma vez e abranger qualquer usuário. Você pode criar um aplicativo uma vez e ter ele funcionando em várias plataformas ou criar um aplicativo que funcione como um cliente, bem como um aplicativo de recurso (API).

## <a name="getting-started"></a>Introdução

Trabalhar com identidade não precisa ser difícil. Escolha um cenário que se aplique a você – o caminho de cada cenário conta com um guia de início rápido e uma página de visão geral para ajudá-lo a colocar tudo em funcionamento em minutos:

- [Crie um aplicativo de página única](scenario-spa-overview.md)
- [Crie um aplicativo Web que conecte os usuários](scenario-web-app-sign-user-overview.md)
- [Crie um aplicativo Web que chame as APIs Web](scenario-web-app-call-api-overview.md)
- [Crie uma API Web protegida](scenario-protected-web-api-overview.md)
- [Crie uma API Web que chame as APIs Web](scenario-web-api-call-api-overview.md)
- [Crie um aplicativo da área de trabalho](scenario-desktop-overview.md)
- [Crie um aplicativo daemon](scenario-daemon-overview.md)
- [Crie um aplicativo móvel](scenario-mobile-overview.md)

O gráfico a seguir descreve os cenários comuns do aplicativo de autenticação – use-o como uma referência ao integrar a plataforma de identidade da Microsoft com seu aplicativo.

[![Cenários de aplicativos na plataforma de identidade da Microsoft](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Próximas etapas

Se você quiser saber mais sobre os principais conceitos de autenticação, recomendamos iniciar com estes tópicos:

- [Noções básicas de autenticação](authentication-scenarios.md)
- [Aplicativo e entidades de serviço](app-objects-and-service-principals.md)
- [Públicos-alvo](v2-supported-account-types.md)
- [Permissões e consentimento](v2-permissions-and-consent.md)
- [Tokens de ID](id-tokens.md) e [tokens de acesso](access-tokens.md)

Crie um aplicativo de dados avançado que chama o [Microsoft Graph](https://docs.microsoft.com/graph/overview).

Quando estiver pronto para iniciar seu aplicativo em um **ambiente de produção**, examine estas melhores práticas:

- [Habilitar registro em log](msal-logging.md) em seu aplicativo.
- Habilitar telemetria em seu aplicativo.
- Habilitar [proxies e personalizar clientes HTTP](msal-net-provide-httpclient.md).
- Teste sua integração seguindo a [lista de verificação de integração da plataforma de identidade da Microsoft](identity-platform-integration-checklist.md).

## <a name="learn-more"></a>Saiba mais

Se estiver planejando criar um aplicativo voltado ao cliente que se conecte a identidades locais e sociais, confira a [Visão geral do Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).
