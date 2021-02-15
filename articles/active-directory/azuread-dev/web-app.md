---
title: Aplicativos Web no Azure Active Directory
description: Descreve quais são os aplicativos Web e as noções básicas sobre fluxo de protocolo, registro e expiração de token para esse tipo de aplicativo.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80154399"
---
# <a name="web-apps"></a>Aplicativos Web

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Os aplicativos Web são aplicativos que autenticam um usuário em um navegador da Web para um aplicativo Web. Nesse cenário, o aplicativo Web direciona o navegador do usuário para conectá-lo ao Azure AD. O Azure AD retorna uma resposta de entrada por meio do navegador do usuário, que contém declarações sobre o usuário em um token de segurança. Este cenário oferece suporte ao logon usando os protocolos WS-Federation, SAML 2.0 e OpenID Connect.

## <a name="diagram"></a>Diagrama

![Fluxo de autenticação do navegador para aplicativo Web](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>Fluxo do protocolo

1. Quando um usuário visita o aplicativo e precisa fazer logon, eles são redirecionados por meio de uma solicitação de logon para o ponto de extremidade de autenticação no Azure AD.
1. O usuário faz logon na página de logon.
1. Se a autenticação for bem-sucedida, o Azure AD criará um token de autenticação e retornará uma resposta de entrada para a URL de resposta do aplicativo que foi configurada no portal do Azure. Para um aplicativo de produção, essa URL de resposta deve ser HTTPS. O token retornado inclui declarações sobre o usuário e o Azure AD que são exigidas pelo aplicativo para validar o token.
1. O aplicativo valida o token usando uma chave de assinatura pública e informações do emissor disponíveis no documento de metadados de federação para o Azure AD. Depois que o aplicativo valida o token, ele inicia uma nova sessão com o usuário. Esta sessão permite que o usuário acesse o aplicativo até expirar.

## <a name="code-samples"></a>Exemplos de código

Veja os exemplos de código do navegador da Web para cenários de aplicativo Web. E volte sempre, pois adicionamos novos exemplos com frequência.

## <a name="app-registration"></a>Registro do aplicativo

Para registrar um aplicativo Web, consulte [registrar um aplicativo](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Locatário único – se você estiver criando um aplicativo apenas para sua organização, ele deverá ser registrado no diretório da sua empresa usando o portal do Azure.
* Multilocatário – se você estiver criando um aplicativo que pode ser usado por usuários fora da sua organização, ele deve ser registrado no diretório da sua empresa, mas também deve ser registrado no diretório de cada organização que usará o aplicativo. Para disponibilizar seu aplicativo em seu diretório, você pode incluir um processo de inscrição para os clientes que os permita ter autorização para seu aplicativo. Ao se inscreverem para seu aplicativo, uma caixa de diálogo será apresentada, mostrando as permissões exigidas pelo aplicativo e, em seguida, a opção de consentimento. Dependendo das permissões necessárias, um administrador na outra organização talvez precise dar consentimento. Quando o usuário ou administrador der seu consentimento, o aplicativo é registrado em seu diretório.

## <a name="token-expiration"></a>Expiração do token

A sessão do usuário expira quando o tempo de vida do token emitido pelo AD do Azure expira. Seu aplicativo pode encurtar esse período de tempo, se desejado, por exemplo, desconectando usuários com base em um período de inatividade. Quando a sessão expirar, o usuário deverá fazer logon novamente.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [Tipos e cenários de aplicativo](app-types.md)
* Saiba mais sobre as [noções básicas de autenticação](v1-authentication-scenarios.md) do Azure AD
