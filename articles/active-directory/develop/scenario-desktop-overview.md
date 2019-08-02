---
title: Aplicativo de desktop que chamadas de web APIs (visão geral) – plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo de Desktop que chamadas de web APIs (visão geral)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 476703b52813e6b3081dcfb3ab5a2fb4f3a7bfc5
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785628"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Cenário: Aplicativo da área de trabalho que chama APIs Web

Aprenda tudo o que você precisa para compilar um aplicativo da área de trabalho que chama as APIs da web

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Se você ainda não fez isso, crie seu primeiro aplicativo seguindo o início rápido da área de trabalho do .NET ou o guia de início rápido UWP:

> [!div class="nextstepaction"]
> [Início Rápido: Adquirir um token e chamar a API do Microsoft Graph de um aplicativo de desktop do Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Início Rápido: Adquirir um token e chamar a API do Microsoft Graph em um aplicativo UWP](./quickstart-v2-uwp.md)

## <a name="overview"></a>Visão geral

Você escreve um aplicativo da área de trabalho, e você deseja conectar usuários ao seu aplicativo e chamar APIs, como o Microsoft Graph, outras APIs da Microsoft ou sua própria API web da web. Você tem várias possibilidades:

- Você pode usar a aquisição de token interativa:

  - Se seu aplicativo da área de trabalho oferece suporte a controles de gráficos, por exemplo se ele é um aplicativo de Windows.Form ou um aplicativo WPF.
  - De se ele for um aplicativo .NET Core e você concorda que a interação de autenticação com o Azure AD acontecer no navegador do sistema

- Para aplicativos hospedado do Windows, também é possível para aplicativos executados em computadores que ingressaram em um domínio do Windows ou ingressado no AAD para adquirir um token silenciosamente usando a autenticação integrada do Windows.
- Por fim, e embora não seja recomendado, você pode usar o nome de usuário e senha em aplicativos de cliente público. Ele ainda é necessário em alguns cenários (como o DevOps), mas lembre-se de que a usá-lo impõem restrições em seu aplicativo. Por exemplo, ele não pode entrar no usuário que precisa para realizar a autenticação multifator (acesso condicional). Também seu aplicativo não se beneficiar do logon único (SSO).

  Ele também é contra os princípios da autenticação moderna e é fornecido apenas por motivos de herança.

  ![Aplicativo da área de trabalho](media/scenarios/desktop-app.svg)

- Se você estiver escrevendo uma ferramenta de linha de comando - provavelmente um aplicativo .NET Core em execução no Linux ou Mac - portáteis e se você aceitar que a autenticação ser designado como o navegador do sistema, você poderá usar a autenticação interativa. (.NET core não fornece ainda [navegador da Web](https://aka.ms/msal-net-uses-web-browser) e, portanto, a autenticação ocorre no navegador system), caso contrário, a melhor opção nesse caso é usar o fluxo de código do dispositivo. Esse fluxo também é usado para aplicativos sem um navegador, como aplicativos de IoT

  ![Aplicativo browserless](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Informações específicas

Aplicativos da área de trabalho têm um número de specificities, que depende principalmente se o seu aplicativo usa a autenticação interativa ou não.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicativo de desktop - registro de aplicativo](scenario-desktop-app-registration.md)
