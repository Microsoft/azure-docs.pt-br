---
title: Visão geral da plataforma de identidade da Microsoft – Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os componentes da plataforma de identidade da Microsoft e como eles podem ajudá-lo a criar suporte a IAM (gerenciamento de identidade e acesso) em seus aplicativos.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 07/09/2020
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: identityplatformtop40, contperf-fy21q2
ms.openlocfilehash: a4ce8242bd3110fee038ac826973e6a134413344
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426834"
---
# <a name="what-is-the-microsoft-identity-platform"></a>O que é a plataforma de identidade da Microsoft?

A plataforma de identidade da Microsoft ajuda você a fornecer acesso autorizado às APIs ou APIs da Microsoft, como o Microsoft Graph, além de criar aplicativos em que seus usuários e clientes podem entrar usando as respectivas identidades da Microsoft ou contas de redes sociais.

Há vários componentes que compõem a plataforma de identidade da Microsoft:

- **Serviço de autenticação em conformidade com o padrão do OAuth 2.0 e do OpenID Connect**, que permite aos desenvolvedores autenticar vários tipos de identidade, incluindo:
  - Contas corporativas ou de estudante, provisionadas pelo Azure AD
  - Conta Microsoft pessoal, como Skype, Xbox e Outlook.com
  - Contas sociais ou locais por meio do Azure AD B2C
- **Bibliotecas open-source**: MSAL (Bibliotecas de Autenticação da Microsoft) e suporte a outras bibliotecas em conformidade com o padrão
- **Portal de gerenciamento de aplicativos**: Uma experiência de registro e configuração no portal do Azure, juntamente com as outras funcionalidades de gerenciamento do Azure.
- **API de configuração de aplicativo e PowerShell**: configuração programática dos seus aplicativos por meio da API do Microsoft Graph e PowerShell, de modo que você possa automatizar suas tarefas de DevOps.
- **Conteúdo de desenvolvedor**: documentação técnica, incluindo inícios rápidos, tutoriais, guias de instruções e exemplos de código.

Para desenvolvedores, a plataforma de identidade da Microsoft oferece uma integração perfeita de inovações modernas no espaço de identidade e segurança, como autenticação sem senha, autenticação step-up e acesso condicional. Você não precisa implementar essa funcionalidade por conta própria: aplicativos integrados à plataforma de identidade da Microsoft se beneficiam nativamente dessas inovações.

Com a plataforma de identidade da Microsoft, você pode escrever código uma vez e alcançar qualquer usuário. Você pode criar um aplicativo uma vez e ter ele funcionando em várias plataformas ou criar um aplicativo que funcione como um cliente, bem como um aplicativo de recurso (API).

Para obter uma visão geral em vídeo da plataforma e uma demonstração da experiência de autenticação, confira [O que é a plataforma de identidade da Microsoft para desenvolvedores?](https://youtu.be/uDU1QTSw7Ps).

## <a name="getting-started"></a>Introdução

Escolha o [cenário de aplicativo](authentication-flows-app-scenarios.md) que você gostaria de criar. Cada um desses caminhos de cenário começa com uma visão geral e vincula a um guia de início rápido para ajudá-lo a colocar as coisas em funcionamento:

- [SPA (aplicativo de página única)](scenario-spa-overview.md)
- [Aplicativo Web que conecta os usuários](scenario-web-app-sign-user-overview.md)
- [Aplicativo Web que chama as APIs Web](scenario-web-app-call-api-overview.md)
- [API Web protegida](scenario-protected-web-api-overview.md)
- [API Web que chama as APIs Web](scenario-web-api-call-api-overview.md)
- [Aplicativo da área de trabalho](scenario-desktop-overview.md)
- [Aplicativo daemon](scenario-daemon-overview.md)
- [Aplicativo móvel](scenario-mobile-overview.md)

Ao trabalhar com a plataforma de identidade da Microsoft para integrar a autenticação e a autorização em seus aplicativos, você pode ver essa imagem que descreve os cenários de aplicativo mais comuns e os respectivos componentes de identidade. Selecione a imagem para vê-la em tamanho normal.

[![Mapa em estilo de metrô mostrando vários cenários de aplicativos na plataforma de identidade da Microsoft](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="learn-authentication-concepts"></a>Saiba mais sobre os conceitos de autenticação

Saiba como os principais conceitos de autenticação do Azure AD se aplicam à plataforma de identidade da Microsoft neste conjunto recomendado de artigos:

- [Noções básicas de autenticação](./authentication-vs-authorization.md)
- [Aplicativo e entidades de serviço](app-objects-and-service-principals.md)
- [Públicos-alvo](v2-supported-account-types.md)
- [Permissões e consentimento](v2-permissions-and-consent.md)
- [Tokens de ID](id-tokens.md)
- [Tokens de acesso](access-tokens.md)
- [Fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)

## <a name="more-identity-and-access-management-options"></a>Mais opções de gerenciamento de identidade e de acesso

[Azure AD B2C](../../active-directory-b2c/overview.md) – crie aplicativos voltados para o cliente nos quais seus usuários podem entrar usando as respectivas contas sociais, como Facebook ou Google, ou usando um endereço de email e senha.

[Azure AD B2B](../external-identities/what-is-b2b.md) – convide usuários externos em seu locatário do Azure AD como usuários "convidados" aos quais você pode atribuir permissões para autorização, enquanto eles usam as respectivas credenciais existentes para autenticação.

[Azure Active Directory para desenvolvedores (v1.0)](../azuread-dev/v1-overview.md) – Mostrado aqui para desenvolvedores com aplicativos existentes que usam o ponto de extremidade v1.0 mais antigo. **Não** use a v1.0 em novos projetos.

## <a name="next-steps"></a>Próximas etapas

Se você tem uma conta do Azure, isso significa que já tem acesso a um locatário do Azure Active Directory, mas a maioria dos desenvolvedores da plataforma de identidade da Microsoft precisa ter os próprios locatários do Azure AD para uso durante o desenvolvimento de aplicativos, um "locatário de desenvolvimento".

Saiba como criar seu locatário para uso ao criar seus aplicativos:

[Início Rápido: configurar um locatário do Azure AD](quickstart-create-new-tenant.md)
