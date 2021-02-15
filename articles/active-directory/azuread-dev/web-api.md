---
title: Aplicativos da API Web no Azure Active Directory
description: Descreve quais são os aplicativos da API Web e as noções básicas sobre fluxo de protocolo, registro e expiração de token para esse tipo de aplicativo.
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
ms.openlocfilehash: 9cf5a9c81ca1d7a42a5a8e342dee55f335656c3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80154416"
---
# <a name="web-api"></a>API Web

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Os Aplicativos de API Web são aplicativos Web que precisam obter recursos de uma API Web. Nesse cenário, há dois tipos de identidade que o aplicativo Web pode usar para autenticar e chamar a API Web:

- **Identidade de aplicativo**: esse cenário usa a concessão de credenciais do cliente OAuth 2.0 para se autenticar como o aplicativo e acessar a API Web. Ao usar uma identidade de aplicativo, a API da Web só pode detectar que o aplicativo Web a está chamando, uma vez que a API Web não recebe nenhuma informação sobre o usuário. Se o aplicativo receber informações sobre o usuário, ele será enviado por meio do protocolo do aplicativo e ele não será assinado pelo Azure AD. A API da Web confia que o aplicativo Web autenticou o usuário. Por esse motivo, esse padrão é chamado de subsistema confiável.
- **Identidade de usuário delegado**: o cenário pode ser realizado de duas maneiras, OpenID Connect e concessão de código de autorização OAuth 2.0 com um cliente confidencial. O aplicativo Web obtém um token de acesso para o usuário, o que prova para a API da Web que o usuário foi autenticado com êxito para o aplicativo Web e que o aplicativo Web foi capaz de obter uma identidade de usuário delegado para chamar a API da Web. Esse token de acesso é enviado na solicitação para a API da Web, que autoriza o usuário e retorna o recurso desejado.

A identidade do aplicativo e os tipos de identidade do usuário delegado são discutidos no fluxo abaixo. A principal diferença entre eles é que a identidade de usuário delegado deve primeiramente adquirir um código de autorização antes que o usuário possa fazer logon e acessar a API da Web.

## <a name="diagram"></a>Diagrama

![Diagrama de aplicativo Web para API da Web](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>Fluxo do protocolo

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identidade do aplicativo com concessão de credenciais do cliente OAuth 2.0

1. Um usuário está conectado ao Azure AD no aplicativo Web (confira a seção **Aplicativos Web** para saber mais).
1. O aplicativo Web precisa adquirir um token de acesso para que ele possa autenticar a API da Web e recuperar o recurso desejado. Ele faz uma solicitação para o ponto de extremidade do token do Azure AD, fornecendo a credencial, a ID do aplicativo e o URI da ID do aplicativo da API Web.
1. O Azure AD autentica o aplicativo e retorna um token de acesso JWT que é usado para chamar a API da Web.
1. Via HTTPS, o aplicativo Web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "portador" no cabeçalho de autorização da solicitação para a API da Web. A API da Web, em seguida, valida o token JWT e, se a validação for bem-sucedida, retorna o recurso desejado.

### <a name="delegated-user-identity-with-openid-connect"></a>Identidade de usuário delegado com OpenID Connect

1. Um usuário está conectado a um aplicativo Web usando o AD do Azure (confira a seção Navegador da Web para aplicativo Web acima). Se o usuário do aplicativo Web ainda não tiver consentido para permitir que o aplicativo Web chame a API da Web em seu nome, o usuário precisará de consentimento. O aplicativo exibirá as permissões que ele exige e se qualquer uma dessas permissões for de nível administrativo, um usuário normal no diretório não poderá conceder a permissão. Esse processo de consentimento só se aplica a aplicativos multilocatários, não a aplicativos de locatário único, uma vez que o aplicativo já terá as permissões necessárias. Quando o usuário tiver feito login, o aplicativo terá recebido um token de ID com informações sobre o usuário, bem como um código de autorização.
1. Usando o código de autorização emitido pelo AD do Azure, o aplicativo Web envia uma solicitação ao ponto de extremidade do token do Azure AD que inclui o código de autorização, detalhes sobre o aplicativo cliente (ID do aplicativo e URI de redirecionamento) e o recurso desejado (URI de ID do aplicativo para a API da Web).
1. O código de autorização e informações sobre o aplicativo Web e a API da Web são validados pelo Azure AD. Após a validação bem-sucedida, o Azure AD retorna dois tokens: um token de acesso JWT e um token de atualização JWT.
1. Via HTTPS, o aplicativo Web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "portador" no cabeçalho de autorização da solicitação para a API da Web. A API da Web, em seguida, valida o token JWT e, se a validação for bem-sucedida, retorna o recurso desejado.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identidade de usuário delegado com concessão de código de autorização do OAuth 2.0

1. Um usuário já está conectado a um aplicativo web, cujo mecanismo de autenticação é independente do Azure AD.
1. O aplicativo Web requer um código de autorização para adquirir um token de acesso, portanto, ele emite uma solicitação por meio do navegador para o ponto de extremidade de autorização do AD do Azure, fornecendo a ID do aplicativo e o URI de redirecionamento para o aplicativo Web após a autenticação bem-sucedida. O usuário faz login no Azure AD.
1. Se o usuário do aplicativo Web ainda não tiver consentido para permitir que o aplicativo Web chame a API da Web em seu nome, o usuário precisará de consentimento. O aplicativo exibirá as permissões que ele exige e se qualquer uma dessas permissões for de nível administrativo, um usuário normal no diretório não poderá conceder a permissão. Esse consentimento se aplica ao aplicativo de único locatário e multilocatário. No caso de único locatário, um administrador pode executar o consentimento de administrador para consentir em nome de seus usuários. Isso pode ser feito usando o `Grant Permissions` botão na [portal do Azure](https://portal.azure.com). 
1. Depois que o usuário tiver concedido permissão, o aplicativo Web recebe o código de autorização de que precisa para adquirir um token de acesso.
1. Usando o código de autorização emitido pelo AD do Azure, o aplicativo Web envia uma solicitação ao ponto de extremidade do token do Azure AD que inclui o código de autorização, detalhes sobre o aplicativo cliente (ID do aplicativo e URI de redirecionamento) e o recurso desejado (URI de ID do aplicativo para a API da Web).
1. O código de autorização e informações sobre o aplicativo Web e a API da Web são validados pelo Azure AD. Após a validação bem-sucedida, o Azure AD retorna dois tokens: um token de acesso JWT e um token de atualização JWT.
1. Via HTTPS, o aplicativo Web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "portador" no cabeçalho de autorização da solicitação para a API da Web. A API da Web, em seguida, valida o token JWT e, se a validação for bem-sucedida, retorna o recurso desejado.

## <a name="code-samples"></a>Exemplos de código

Consulte os exemplos de código para os cenários de aplicativo Web para API da Web. E volte sempre, pois adicionamos novos exemplos com frequência. [Aplicativo Web para API da Web](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Registro do aplicativo

Para registrar um aplicativo com o ponto de extremidade v 1.0 do Azure AD, consulte [registrar um aplicativo](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Locatário único: para a identidade do aplicativo e casos de identidade de usuário delegado, o aplicativo Web e a API Web devem ser registrados no mesmo diretório no Azure AD. A API da Web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso do aplicativo Web a seus recursos. Se um tipo de identidade de usuário delegado estiver sendo usado, o aplicativo Web precisará selecionar as permissões desejadas no menu suspenso **permissões para outros aplicativos** na portal do Azure. Essa etapa não será necessária se o tipo de identidade de aplicativo estiver sendo usado.
* Multilocatário: primeiro, o aplicativo Web é configurado para indicar as permissões necessárias para que seja funcional. Essa lista de permissões necessárias é mostrada em uma caixa de diálogo quando um usuário ou administrador no diretório de destino dá consentimento para o aplicativo, o que o torna disponível para sua organização. Alguns aplicativos exigem apenas permissões de nível de usuário, que qualquer usuário na organização pode conceder. Outros aplicativos exigem permissões de nível administrativo, que um usuário na organização não pode conceder. Somente um administrador do diretório pode dar consentimento para aplicativos que exigem esse nível de permissões. Quando o usuário ou administrador conceder sua permissão, o aplicativo Web e a API da Web são registrados em seu diretório.

## <a name="token-expiration"></a>Expiração do token

Quando o aplicativo Web usa seu código de autorização para obter um token de acesso JWT, ele também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser usado para autenticar o usuário novamente sem a necessidade de fazer logon novamente. Esse token de atualização é usado para autenticar o usuário, o que resulta em um novo token de acesso e token de atualização.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre outros [Tipos e cenários de aplicativo](app-types.md)
- Saiba mais sobre as [noções básicas de autenticação](v1-authentication-scenarios.md) do Azure AD
