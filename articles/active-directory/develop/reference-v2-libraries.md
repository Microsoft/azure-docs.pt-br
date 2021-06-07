---
title: Bibliotecas de autenticação da plataforma de identidade da Microsoft | Azure
description: Lista de bibliotecas de cliente e middleware compatível com a plataforma de identidade da Microsoft. Use essas bibliotecas para adicionar suporte para entrada de usuário (autenticação) e acesso à API Web protegido (autorização) para seus aplicativos.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 590e57d587c8e6e254811892b5c5e740b511c302
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104690646"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Bibliotecas de autenticação da plataforma de identidade da Microsoft

As tabelas a seguir mostram o suporte da biblioteca de autenticação da Microsoft para vários tipos de aplicativos. Eles incluem links para o código-fonte da biblioteca, onde obter o pacote para o projeto do aplicativo e se a biblioteca dá suporte à entrada do usuário (autenticação), acesso a APIs da Web protegidas (autorização) ou ambos.

A plataforma de identidade da Microsoft foi certificada pelo OpenID Foundation como um [provedor de OpenID certificado](https://openid.net/certification/). Se você preferir usar uma biblioteca diferente da MSAL (biblioteca de autenticação da Microsoft) ou outra biblioteca com suporte da Microsoft, escolha uma com uma [implementação certificada do OpenID Connect](https://openid.net/developers/certified/).

Se você optar por codificar manualmente sua própria implementação de nível de protocolo do [OAuth 2,0 ou do OpenID connect 1,0](active-directory-v2-protocols.md), preste atenção às considerações de segurança na especificação de cada padrão e siga uma metodologia de SDL (ciclo de vida de desenvolvimento de software) como o [SDL da Microsoft][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Aplicativo de página única (SPA)

Um aplicativo de página única é executado inteiramente na superfície do navegador e busca dados da página (HTML, CSS e JavaScript) dinamicamente ou em tempo de carregamento do aplicativo. Ele pode chamar APIs Web para interagir com fontes de dados back-end.

Como o código de uma SPA é executado inteiramente no navegador, ele é considerado um *cliente público* que não pode armazenar segredos com segurança.

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="web-application"></a>Aplicativo Web

Um aplicativo Web executa o código em um servidor que gera e envia HTML, CSS e JavaScript para o navegador da Web de um usuário a ser renderizado. A identidade do usuário é mantida como uma sessão entre o navegador do usuário (o front-end) e o servidor Web (o back-end).

Como o código de um aplicativo Web é executado no servidor Web, ele é considerado um *cliente confidencial* que pode armazenar segredos com segurança.

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

## <a name="desktop-application"></a>Aplicativo da área de trabalho

Um aplicativo de área de trabalho normalmente é um código binário (compilado) que mostra uma interface do usuário e destina-se a ser executado na área de trabalho de um usuário.

Como um aplicativo de área de trabalho é executado na área de trabalho do usuário, ele é considerado um *cliente público* que não pode armazenar segredos com segurança.

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="mobile-application"></a>Aplicativo móvel

Um aplicativo móvel normalmente é um código binário (compilado) que mostra uma interface do usuário e destina-se a ser executado em um dispositivo móvel do usuário.

Como um aplicativo móvel é executado no dispositivo móvel do usuário, ele é considerado um *cliente público* que não pode armazenar segredos com segurança.

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="service--daemon"></a>Serviço/daemon

Os serviços e daemons são comumente usados para comunicação de servidor para servidor e outras comunicações autônomas (às vezes chamadas de sem *periféricos*). Como não há nenhum usuário no teclado para inserir as credenciais ou consentir o acesso aos recursos, esses aplicativos são autenticados como eles mesmos, e não um usuário, ao solicitar acesso autorizado aos recursos de uma API Web.

Um serviço ou daemon executado em um servidor é considerado um *cliente confidencial* que pode armazenar seus segredos com segurança.

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a biblioteca de autenticação da Microsoft, consulte a [visão geral da biblioteca de autenticação da Microsoft (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
