---
title: Aplicativos nativos no Azure Active Directory
description: Descreve quais são os aplicativos nativos e as noções básicas sobre fluxo de protocolo, registro e expiração de token para esse tipo de aplicativo.
services: active-directory
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
ms.openlocfilehash: 9ecf711f5442b6f21de53d2735ad1c94d7cb6223
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154790"
---
# <a name="native-apps"></a>Aplicativos nativos

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Aplicativos nativos são aplicativos que chamam uma API Web em nome de um usuário. Este cenário se baseia no tipo de concessão de código de autorização OAuth 2.0 com um cliente público, conforme descrito na seção 4.1 da [especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749). O aplicativo nativo obtém um token de acesso para o usuário usando o protocolo OAuth 2.0. Esse token de acesso é enviado na solicitação para a API da Web, que autoriza o usuário e retorna o recurso desejado.

## <a name="diagram"></a>Diagrama

![Diagrama de aplicativo nativo para API da Web](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>Fluxo do protocolo

Se você estiver usando as bibliotecas de autenticação do AD, a maioria dos detalhes do protocolo descritos abaixo será manipulada para você, como o pop-up do navegador, o armazenamento em cache do token e a manipulação de tokens de atualização.

1. Usando um pop-up de navegador, o aplicativo nativo faz uma solicitação para o ponto de extremidade de autorização no Azure AD. Essa solicitação inclui a ID do Aplicativo e o URI de redirecionamento do aplicativo nativo, conforme mostrado no portal do Azure, e o URI de ID do aplicativo para a API da Web. Se o usuário ainda não tiver entrado, será solicitado que ele entre novamente
1. O Azure AD autentica o usuário. Se for um aplicativo multilocatário e o consentimento for necessário para usar o aplicativo, o usuário será solicitado a consentir se ele ainda não tiver feito isso. Depois de conceder consentimento e após a autenticação bem-sucedida, o Azure AD emite uma resposta de código de autorização de volta para o URI de redirecionamento do aplicativo cliente.
1. Quando o Azure AD emite uma resposta de código de autorização para o URI de redirecionamento, o aplicativo cliente interrompe a interação com o navegador e extrai o código de autorização da resposta. Usando esse código de autorização, o aplicativo cliente envia uma solicitação ao ponto de extremidade do token do Azure AD que inclui o código de autorização, detalhes sobre o aplicativo cliente (ID do aplicativo e URI de redirecionamento) e o recurso desejado (URI de ID do aplicativo para a API da Web).
1. O código de autorização e informações sobre o aplicativo cliente e a API da Web são validados pelo Azure AD. Após a validação bem-sucedida, o Azure AD retorna dois tokens: um token de acesso JWT e um token de atualização JWT. Além disso, o Azure AD retorna informações básicas sobre o usuário, como seu nome de exibição e ID do locatário.
1. Via HTTPS, o aplicativo cliente usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "portador" no cabeçalho de autorização da solicitação para a API da Web. A API da Web, em seguida, valida o token JWT e, se a validação for bem-sucedida, retorna o recurso desejado.
1. Quando o token de acesso expira, o aplicativo cliente recebe um erro que indica que o usuário precisa se autenticar novamente. Se o aplicativo tiver um token de atualização válido, ele pode ser usado para adquirir um novo token de acesso sem que um novo logon do usuário seja solicitado. Se o token de atualização expirar, o aplicativo precisará autenticar interativamente o usuário novamente.

> [!NOTE]
> O token de atualização emitido pelo Azure AD pode ser usado para acessar vários recursos. Por exemplo, se você tiver um aplicativo cliente que tenha permissão para chamar duas APIs da Web, o token de atualização pode ser usado para obter um token de acesso para a outra API da Web também.

## <a name="code-samples"></a>Exemplos de código

Consulte os exemplos de código para os cenários de aplicativo nativo para API da Web. E volte sempre, pois novos exemplos são adicionados com frequência. [Aplicativo nativo para API da Web](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Registro de aplicativo

Para registrar um aplicativo com o ponto de extremidade v 1.0 do Azure AD, consulte [registrar um aplicativo](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Locatário único – O aplicativo nativo e a API Web devem ser registrados no mesmo diretório no Azure AD. A API da Web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso do aplicativo nativo a seus recursos. Em seguida, o aplicativo cliente seleciona as permissões desejadas no menu suspenso "permissões para outros aplicativos" no portal do Azure.
* Multilocatário-primeiro, o aplicativo nativo já se registrou apenas no diretório do desenvolvedor ou do editor. Em segundo lugar, o aplicativo nativo é configurado para indicar as permissões necessárias para que seja funcional. Essa lista de permissões necessárias é mostrada em uma caixa de diálogo quando um usuário ou administrador no diretório de destino dá consentimento para o aplicativo, o que o torna disponível para sua organização. Alguns aplicativos exigem apenas permissões de nível de usuário, que qualquer usuário na organização pode conceder. Outros aplicativos exigem permissões de nível administrativo, que um usuário na organização não pode conceder. Somente um administrador do diretório pode dar consentimento para aplicativos que exigem esse nível de permissões. Quando o usuário ou administrador concede sua permissão, somente a API da Web é registrada no diretório. 

## <a name="token-expiration"></a>Expiração do token

Quando o aplicativo nativo usa seu código de autorização para obter um token de acesso JWT, ele também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser usado para autenticar o usuário novamente sem a necessidade de fazer logon novamente. Esse token de atualização é usado para autenticar o usuário, o que resulta em um novo token de acesso e token de atualização.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre outros [Tipos e cenários de aplicativo](app-types.md)
- Saiba mais sobre as [noções básicas de autenticação](v1-authentication-scenarios.md) do Azure AD
