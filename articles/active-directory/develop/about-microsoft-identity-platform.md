---
title: Evolução da plataforma de identidade da Microsoft – Azure
description: Saiba mais sobre a plataforma de identidade da Microsoft, uma evolução da plataforma de desenvolvedor e serviço de identidade do Azure AD (Azure Active Directory).
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
ms.date: 06/03/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dc4c29d3a12902b3ce01f9505cba5ac02e921eb
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625355"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Evolução da plataforma de identidade da Microsoft

A plataforma de identidade da Microsoft é uma evolução da plataforma de desenvolvedor do Azure AD (Azure Active Directory). Ela permite que os desenvolvedores criem aplicativos que conectam usuários e obtêm tokens para chamar APIs, como o Microsoft Graph, ou APIs criadas pelos desenvolvedores. Consiste em um serviço de autenticação, bibliotecas open-source, registro de aplicativo e configuração (por meio de um portal de desenvolvedor e de uma API de aplicativo), documentação completa do desenvolvedor, exemplos de início rápido, exemplos de código, tutoriais, guias de instruções e outros tipos de conteúdo do desenvolvedor. A plataforma de identidade da Microsoft dá suporte a protocolos padrão do setor, como OAuth 2.0 e OpenID Connect.

Até agora, a maioria dos desenvolvedores trabalhou com a plataforma do Azure AD v1.0 para autenticar contas corporativas e de estudante (provisionadas pelo Azure AD) solicitando tokens do ponto de extremidade do Azure AD v1.0, usando a ADAL (Biblioteca de Autenticação do Azure AD), o portal do Azure para registro de aplicativo e configuração e a API do Graph do Azure AD para configuração programática de aplicativo.

Com a plataforma de identidade da Microsoft (v2.0), expanda seu alcance para estes tipos de usuários:

- contas corporativas e de estudante (contas do Azure AD provisionadas)
- contas pessoais (como Outlook.com ou Hotmail.com)
- clientes que trazem seus próprios emails ou identidades sociais (como LinkedIn, Facebook e Google) por meio da oferta do Azure AD B2C

Com a plataforma de identidade unificada da Microsoft, você pode escrever o código uma vez e autenticar qualquer identidade da Microsoft em seu aplicativo. Para várias plataformas, há uma biblioteca open-source com suporte completo, chamada MSAL (Biblioteca de Autenticação da Microsoft). A MSAL é simples de usar, fornece ótimas experiências de SSO (logon único) para seus usuários, ajuda você a alcançar alta confiabilidade e desempenho e foi desenvolvida usando o Microsoft SDL (Secure Development Lifecycle). Ao chamar APIs, você pode configurar seu aplicativo para aproveitar o consentimento incremental, que permite atrasar a solicitação de consentimento para escopos mais invasivos até que o uso do aplicativo garanta isso em tempo de execução.

Use o portal do Azure para registrar e configurar seu aplicativo e a API do Microsoft Graph para a configuração programática de aplicativo.

Atualize o aplicativo em seu próprio ritmo. Ainda há suporte para os aplicativos criados com bibliotecas ADAL. Também há suporte para portfólios de aplicativos mistos, que consistem em aplicativos criados com a ADAL e aplicativos criados com bibliotecas MSAL. Isso significa que os aplicativos que usam a ADAL e a MSAL mais recentes oferecerão SSO no portfólio, fornecido pelo cache de token compartilhado entre essas bibliotecas. Os aplicativos atualizados da ADAL para a MSAL manterão o estado de entrada do usuário após a atualização.

## <a name="microsoft-identity-platform-experience"></a>Experiência da plataforma de identidade da Microsoft

O diagrama a seguir mostra a experiência de identidade da Microsoft de modo geral, incluindo a experiência de registro do aplicativo, SDKs, pontos de extremidade e identidades com suporte.

![A plataforma de identidade da Microsoft hoje](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Experiência de registro de aplicativo

A experiência de **[Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908)** do portal do Azure é a experiência de portal para gerenciar em um único portal todos os aplicativos integrados à plataforma de identidade da Microsoft. Se você usar o Portal de Registro de Aplicativo, comece a usar a experiência de registro de aplicativo do portal do Azure.

Para integração ao Azure AD B2C (na autenticação de identidades sociais ou locais), você precisará registrar seu aplicativo em um locatário B2C. Essa experiência também faz parte do portal do Azure.

Atualmente, a **API de aplicativo no Microsoft Graph** está em versão prévia. Use essa API para configurar de forma programática os aplicativos integrados à plataforma de identidade da Microsoft para autenticar qualquer identidade da Microsoft. No entanto, até que essa API alcance disponibilidade geral, você deverá usar a API do Graph 1.6 do Azure AD e o manifesto do aplicativo.

### <a name="msal-libraries"></a>Bibliotecas MSAL

Você pode usar a biblioteca MSAL para criar aplicativos que autenticam todas as identidades da Microsoft. As bibliotecas MSAL no .NET e JavaScript estão em disponibilidade geral. As bibliotecas MSAL para iOS e Android estão em versão prévia e são adequadas para uso em um ambiente de produção. Fornecemos o mesmo suporte no nível de produção para bibliotecas MSAL em versão prévia que fornecemos para versões da MSAL e da ADAL que estão em disponibilidade geral.

Use também as bibliotecas MSAL para integrar seu aplicativo ao Azure AD B2C.

As bibliotecas do servidor para a criação de aplicativos Web e APIs Web estão em disponibilidade geral: [ASP.NET](https://docs.microsoft.com/aspnet/overview) e [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>ponto de extremidade da plataforma de identidade da Microsoft

O ponto de extremidade da plataforma de identidade da Microsoft (v2.0) agora tem certificação OIDC. Ele funciona com a MSAL (Bibliotecas de Autenticação da Microsoft) ou qualquer outra biblioteca em conformidade com os padrões. Ele implementa escopos legíveis por humanos, de acordo com os padrões do setor.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o v1.0 e o v2.0.

* [Visão geral da plataforma de identidade da Microsoft (v2.0)](v2-overview.md)
* [Visão geral do Azure Active Directory para desenvolvedores (v1.0)](v1-overview.md)
