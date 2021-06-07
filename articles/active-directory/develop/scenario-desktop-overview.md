---
title: Criação de um aplicativo da área de trabalho que chama APIs Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo da área de trabalho que chama APIs Web (visão geral)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ea6ecf456bbcea01bf4c1eef5377d918bf0918fd
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798945"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Cenário: Aplicativo da área de trabalho que chama APIs Web

Saiba tudo o que você precisa para criar um aplicativo da área de trabalho que chama APIs Web.

## <a name="get-started"></a>Introdução

Se você ainda não fez isso, crie seu primeiro aplicativo concluindo um início rápido:

- [Início Rápido: Adquirir um token e chamar a API do Microsoft Graph de um aplicativo da área de trabalho do Windows](./quickstart-v2-windows-desktop.md)
- [Início Rápido: Adquirir um token e chamar a API do Microsoft Graph de um aplicativo da UWP](./quickstart-v2-uwp.md)
- [Início Rápido: Adquirir um token e chamar a API do Microsoft Graph de um aplicativo nativo do macOS](./quickstart-v2-ios.md)
- [Início rápido: adquirir um token e chamar Microsoft Graph API de um aplicativo Node.js & de aplicativos](./quickstart-v2-nodejs-desktop.md)

## <a name="overview"></a>Visão geral

Você escreve um aplicativo da área de trabalho e deseja conectar usuários ao seu aplicativo e chamar APIs Web, como Microsoft Graph, outras APIs da Microsoft ou sua própria API Web. Você tem várias opções:

- Usar a aquisição de token interativo:

  - Se o seu aplicativo da área de trabalho dá suporte a controles gráficos, por exemplo, se é um aplicativo Windows.Form, um aplicativo WPF ou um aplicativo nativo do macOS.
  - Ou, se é um aplicativo do .NET Core e você concorda em fazer a interação de autenticação com o Azure Active Directory (Azure AD) no navegador do sistema.
  - Ou, se for um aplicativo de Node.js de aplicativos, que é executado em uma instância de Chromium.

- Para aplicativos hospedados do Windows, também é possível para aplicativos em execução em computadores ingressados em um domínio do Windows ou ingressados no Azure AD adquirir um token silenciosamente usando a Autenticação Integrada do Windows.
- Por fim, e embora não seja recomendado, é possível usar o nome de usuário e a senha em aplicativos cliente públicos. Isso ainda é necessário em alguns cenários, tais como o DevOps. E usá-los impõe restrições em seus aplicativos. Por exemplo, não é possível conectar um usuário que precisa realizar [autenticação multifator](../authentication/concept-mfa-howitworks.md) (acesso condicional). Além disso, seus aplicativos também não se beneficiam do logon único (SSO).

  Isso também vai contra os princípios da autenticação moderna e só é oferecido por ser herdado.

  ![Aplicativo da área de trabalho](media/scenarios/desktop-app.svg)

- Se você gravar uma ferramenta de linha de comando portátil, provavelmente um aplicativo .NET Core que é executado no Linux ou no Mac, e aceitar que a autenticação será delegada ao navegador do sistema, você poderá usar a autenticação interativa. O .NET Core não fornece um [navegador da Web](https://aka.ms/msal-net-uses-web-browser), portanto, a autenticação ocorre no navegador do sistema. Caso contrário, a melhor opção é usar o fluxo de código do dispositivo. Esse fluxo também é usado para aplicativos sem um navegador, como aplicativos baseados em IoT.

  ![Aplicativo sem uso de navegador](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Especificações

Os aplicativos da área de trabalho têm várias especificidades. Eles baseiam-se principalmente no uso ou não de autenticação interativa pelo seu aplicativo.

## <a name="recommended-reading"></a>Leitura recomendada

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, registro de [aplicativo](scenario-desktop-app-registration.md).
