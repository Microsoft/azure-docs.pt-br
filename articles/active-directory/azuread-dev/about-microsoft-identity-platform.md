---
title: Evolução da plataforma de identidade da Microsoft – Azure
description: Saiba mais sobre a plataforma de identidade da Microsoft, uma evolução da plataforma de desenvolvedor e serviço de identidade do Azure AD (Azure Active Directory).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 12/09/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv, marsma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ab0057f55ddb5bf76f6fa74b0211b5ea5af24288
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96608207"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Evolução da plataforma de identidade da Microsoft

A [plataforma de identidade da Microsoft](../develop/index.yml) é uma evolução da plataforma de desenvolvedor do Azure AD (Azure Active Directory). Ela permite que os desenvolvedores criem aplicativos que conectam usuários e obtêm tokens para chamar APIs, como o Microsoft Graph, ou APIs criadas pelos desenvolvedores. Consiste em um serviço de autenticação, bibliotecas open-source, registro de aplicativo e configuração (por meio de um portal de desenvolvedor e de uma API de aplicativo), documentação completa do desenvolvedor, exemplos de início rápido, exemplos de código, tutoriais, guias de instruções e outros tipos de conteúdo do desenvolvedor. A plataforma de identidade da Microsoft dá suporte a protocolos padrão do setor, como OAuth 2.0 e OpenID Connect.

Muitos desenvolvedores trabalharam anteriormente com a plataforma Azure AD v1.0 para autenticar contas corporativas e de estudante (provisionadas pelo Azure AD) solicitando tokens do ponto de extremidade do Azure AD v1.0, usando a ADAL (Biblioteca de Autenticação do Azure AD), o portal do Azure para registro de aplicativo e configuração e a API do Microsoft Graph para configuração programática de aplicativo.

Com a plataforma de identidade unificada da Microsoft (v2.0), você pode escrever o código uma vez e autenticar qualquer identidade da Microsoft em seu aplicativo. Para várias plataformas, a MSAL (Biblioteca de Autenticação da Microsoft) de software livre totalmente compatível é recomendada para uso em pontos de extremidade da plataforma de identidade. A MSAL é simples de usar, fornece ótimas experiências de SSO (logon único) para seus usuários, ajuda você a alcançar alta confiabilidade e desempenho e foi desenvolvida usando o Microsoft SDL (Secure Development Lifecycle). Ao chamar APIs, você pode configurar seu aplicativo para aproveitar o consentimento incremental, que permite atrasar a solicitação de consentimento para escopos mais invasivos até que o uso do aplicativo garanta isso em runtime.  A MSAL também é compatível com o Azure Active Directory B2C permitindo que seus clientes usem suas identidades de conta social, empresarial ou local preferenciais para obter acesso de logon único a seus aplicativos e APIs.

Com a plataforma de identidade da Microsoft, expanda seu alcance para estes tipos de usuários:

- contas corporativas e de estudante (contas do Azure AD provisionadas)
- contas pessoais (como Outlook.com ou Hotmail.com)
- Seus clientes que trazem os próprios emails ou identidades sociais (como LinkedIn, Facebook e Google) por meio da MSAL e do Azure AD B2C

Use o portal do Azure para registrar e configurar seu aplicativo e a API do Microsoft Graph para a configuração programática de aplicativo.

Atualize o aplicativo em seu próprio ritmo. Ainda há suporte para os aplicativos criados com bibliotecas ADAL. Também há suporte para portfólios de aplicativos mistos, que consistem em aplicativos criados com a ADAL e aplicativos criados com bibliotecas MSAL. Isso significa que os aplicativos que usam a ADAL e a MSAL mais recentes oferecerão SSO no portfólio, fornecido pelo cache de token compartilhado entre essas bibliotecas. Os aplicativos atualizados da ADAL para a MSAL manterão o estado de entrada do usuário após a atualização.

## <a name="microsoft-identity-platform-experience"></a>Experiência da plataforma de identidade da Microsoft

O diagrama a seguir mostra a experiência de identidade da Microsoft de modo geral, incluindo a experiência de registro do aplicativo, SDKs, pontos de extremidade e identidades com suporte.

![A plataforma de identidade da Microsoft hoje](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Experiência de registro de aplicativo

A experiência de **[Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908)** do portal do Azure é a experiência de portal para gerenciar em um único portal todos os aplicativos integrados à plataforma de identidade da Microsoft. Se você usar o Portal de Registro de Aplicativo, comece a usar a experiência de registro de aplicativo do portal do Azure.

Para integração ao Azure AD B2C (na autenticação de identidades sociais ou locais), você precisará registrar seu aplicativo em um locatário do Azure AD B2C. Essa experiência também faz parte do portal do Azure.

Use a [API de aplicativo](/graph/api/resources/application) para configurar de maneira programática os aplicativos integrados à plataforma de identidade da Microsoft a fim de autenticar qualquer identidade da Microsoft.

### <a name="msal-libraries"></a>Bibliotecas MSAL

Você pode usar a biblioteca MSAL para criar aplicativos que autenticam todas as identidades da Microsoft. As bibliotecas MSAL no .NET e JavaScript estão em disponibilidade geral. As bibliotecas MSAL para iOS e Android estão em versão prévia e são adequadas para uso em um ambiente de produção. Fornecemos o mesmo suporte no nível de produção para bibliotecas MSAL em versão prévia que fornecemos para versões da MSAL e da ADAL que estão em disponibilidade geral.

Use também as bibliotecas MSAL para integrar seu aplicativo ao Azure AD B2C.

### <a name="microsoft-identity-platform-endpoint"></a>ponto de extremidade da plataforma de identidade da Microsoft

O ponto de extremidade da plataforma de identidade da Microsoft (v2.0) tem certificação OIDC. Ele funciona com a MSAL (Bibliotecas de Autenticação da Microsoft) ou qualquer outra biblioteca em conformidade com os padrões. Ele implementa escopos legíveis por humanos, de acordo com os padrões do setor.

## <a name="next-steps"></a>Próximas etapas

Saiba mais na [documentação da plataforma de identidade da Microsoft](../develop/index.yml).
