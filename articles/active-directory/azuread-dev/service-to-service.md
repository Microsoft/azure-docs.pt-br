---
title: Aplicativos serviço a serviço no Azure Active Directory
description: Descreve quais são os aplicativos serviço a serviço e as noções básicas sobre fluxo de protocolo, registro e expiração de token para esse tipo de aplicativo.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 179034533d90dbbb6ca362fc6f72996f32873729
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154756"
---
# <a name="service-to-service-apps"></a>Aplicativos serviço a serviço

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Os aplicativos de serviço a serviço podem ser um aplicativo daemon ou para servidores que precisa obter recursos de uma API Web. Há dois subcenários que se aplicam a esta seção:

- Um daemon que precisa chamar uma API Web, criada no tipo de concessão de credenciais de cliente OAuth 2.0

    Nesse cenário, é importante entender algumas coisas. Primeiro, a interação do usuário não é possível com um aplicativo daemon, que requer que o aplicativo tenha sua própria identidade. Um exemplo de um aplicativo daemon é um trabalho em lotes ou um serviço de sistema operacional sendo executado em segundo plano. Esse tipo de aplicativo solicita um token de acesso usando sua identidade de aplicativo e apresentando sua ID de Aplicativo, credenciais (senha ou certificado) e URI de ID do aplicativo para o Azure AD. Após a autenticação bem-sucedida, o daemon recebe um token de acesso do Azure AD, que é usado para chamar a API da Web.

- Um aplicativo para servidores (por exemplo, uma API Web) que precisa chamar uma API Web, criado na especificação de rascunho em nome do OAuth 2.0

    Nesse cenário, imagine que um usuário tenha se autenticado em um aplicativo nativo e que esse aplicativo nativo precisa chamar uma API Web. O Azure AD emite um token de acesso JWT para chamar a API da Web. Se a API da Web precisar chamar outra API da Web downstream, ela poderá usar o fluxo em nome de para delegar a identidade do usuário e autenticar para a API da Web de segunda camada.

## <a name="diagram"></a>Diagrama

![Aplicativo Daemon ou de servidor para diagrama da API da Web](./media/authentication-scenarios/daemon-server-app-to-web-api.png)

## <a name="protocol-flow"></a>Fluxo do protocolo

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identidade do aplicativo com concessão de credenciais do cliente OAuth 2.0

1. Primeiro, o aplicativo de servidor precisa se autenticar no Azure AD ele mesmo, sem qualquer interação humana, como uma caixa de diálogo de logon interativa. Ele faz uma solicitação para o ponto de extremidade do token do Azure AD, fornecendo a credencial, a ID do aplicativo e o URI da ID do aplicativo.
1. O Azure AD autentica o aplicativo e retorna um token de acesso JWT que é usado para chamar a API da Web.
1. Via HTTPS, o aplicativo Web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "portador" no cabeçalho de autorização da solicitação para a API da Web. A API da Web, em seguida, valida o token JWT e, se a validação for bem-sucedida, retorna o recurso desejado.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identidade de usuário delegado com a especificação de rascunho do OAuth 2.0 On-Behalf-Of

O fluxo discutido abaixo pressupõe que um usuário tenha sido autenticado em outro aplicativo (como um aplicativo nativo) e sua identidade de usuário tiver sido usada para adquirir um token de acesso à API da Web de primeira camada.

1. O aplicativo nativo envia o token de acesso à API da Web de primeira camada.
1. A API da Web de primeira camada envia uma solicitação ao ponto de extremidade do token do Azure AD, fornecendo sua ID de aplicativo e suas credenciais, bem como o token de acesso do usuário. Além disso, a solicitação é enviada com um parâmetro on_behalf_of que indica que a API da Web está solicitando novos tokens para chamar uma API da Web downstream em nome do usuário original.
1. O Azure AD verifica se a API da Web de primeira camada tem permissões para acessar a API da Web de segunda camada e valida a solicitação, retornando um token de acesso JWT e um token de atualização JWT para a API da Web de primeira camada.
1. Via HTTPS, a API da Web de primeira camada chama a API da Web de segunda camada, acrescentando a cadeia de caracteres do token no cabeçalho de autorização na solicitação. A API da Web de primeira camada pode continuar a chamar a API da Web de segunda camada, desde que o token de acesso e os tokens de atualização sejam válidos.

## <a name="code-samples"></a>Exemplos de código

Consulte os exemplos de código para o aplicativo daemon ou de servidor para cenários da API Web: [servidor ou aplicativo daemon para API da Web](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Registro de aplicativo

* Locatário único – Para a identidade do aplicativo e casos de identidade de usuário delegado, o aplicativo daemon ou para servidores deve ser registrado no mesmo diretório no Azure AD. A API da Web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso do daemon ou do servidor a seus recursos. Se um tipo de identidade de usuário delegado estiver sendo usado, o aplicativo de servidor precisará selecionar as permissões desejadas. Na página **permissão de API** para o registro do aplicativo, depois de selecionar **Adicionar uma permissão** e escolher a família de API, escolha **permissões delegadas** e, em seguida, selecione suas permissões. Essa etapa não será necessária se o tipo de identidade de aplicativo estiver sendo usado.
* Multilocatário – Primeiro, o aplicativo daemon ou para servidores é configurado para indicar as permissões necessárias para que seja funcional. Essa lista de permissões necessárias é mostrada em uma caixa de diálogo quando um usuário ou administrador no diretório de destino dá consentimento para o aplicativo, o que o torna disponível para sua organização. Alguns aplicativos exigem apenas permissões de nível de usuário, que qualquer usuário na organização pode conceder. Outros aplicativos exigem permissões de nível administrativo, que um usuário na organização não pode conceder. Somente um administrador do diretório pode dar consentimento para aplicativos que exigem esse nível de permissões. Quando o usuário ou administrador concede permissão, ambas as APIs Web são registradas no diretório delas.

## <a name="token-expiration"></a>Expiração do token

Quando o primeiro aplicativo usa seu código de autorização para obter um token de acesso JWT, ele também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser usado para autenticar o usuário novamente sem solicitar credenciais. Esse token de atualização é usado para autenticar o usuário, o que resulta em um novo token de acesso e token de atualização.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre outros [Tipos e cenários de aplicativo](app-types.md)
- Saiba mais sobre as [noções básicas de autenticação](v1-authentication-scenarios.md) do Azure AD
