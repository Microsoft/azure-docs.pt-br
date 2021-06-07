---
title: Ponto de extremidade de UserInfo da plataforma de identidade da Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o ponto de extremidade de UserInfo na plataforma de identidade da Microsoft.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3bd0621d607b121f19bc47c717343b8e2e39b04f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98752722"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>Ponto de extremidade de UserInfo da plataforma de identidade da Microsoft

O ponto de extremidade de UserInfo faz parte do [padrão OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC), projetado para retornar declarações sobre o usuário autenticado.  Para a plataforma de identidade da Microsoft, o ponto de extremidade de UserInfo é hospedado em Microsoft Graph ( https://graph.microsoft.com/oidc/userinfo) . 

## <a name="find-the-well-known-configuration-endpoint"></a>Encontrar o ponto de extremidade de configuração. conhecido

Você pode descobrir programaticamente o ponto de extremidade do UserInfo usando o documento de descoberta do OpenID Connect em `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` . Ele está listado no `userinfo_endpoint` campo, e esse padrão pode ser usado em nuvens para ajudar a apontar para o ponto de extremidade correto.  Não recomendamos embutir o ponto de extremidade UserInfo em seu aplicativo – use o documento de descoberta OIDC para encontrar esse ponto de extremidade em tempo de execução.

Como parte da especificação do OpenID Connect, o ponto de extremidade de UserInfo geralmente é chamado automaticamente por [bibliotecas compatíveis com OIDC](https://openid.net/developers/certified/)  para obter informações sobre o usuário.  Sem hospedar tal ponto de extremidade, a plataforma de identidade da Microsoft não seria compatível com padrões e algumas bibliotecas falhariam.  Na [lista de declarações identificadas no padrão OIDC](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims) , geramos as declarações de nome, a declaração de assunto e o email quando disponíveis e consentiram para o.  

## <a name="consider-use-an-id-token-instead"></a>Considere: Use um token de ID em vez disso

As informações disponíveis no token de ID que seu aplicativo pode receber são um superconjunto das informações que ele pode obter do ponto de extremidade de UserInfo.  Como você pode obter um token de ID ao mesmo tempo que obtém um token para chamar o ponto de extremidade de UserInfo, sugerimos que você use esse token de ID para obter informações sobre o usuário em vez de chamar o ponto de extremidade de UserInfo.  O uso do token de ID eliminará uma a duas solicitações de rede do início do aplicativo, reduzindo a latência em seu aplicativo.

Se você precisar de mais detalhes sobre o usuário, deverá chamar a [ `/user` API de Microsoft Graph](/graph/api/user-get) para obter informações como o número do escritório ou o cargo.   Você também pode usar [declarações opcionais](active-directory-optional-claims.md) para incluir informações adicionais do usuário em seus tokens de ID e de acesso.

## <a name="calling-the-userinfo-endpoint"></a>Chamando o ponto de extremidade de UserInfo

O UserInfo é uma API de token de portador OAuth padrão, chamada como qualquer outra API de Microsoft Graph usando o token de acesso recebido ao obter um token para Microsoft Graph. Ele retorna uma resposta JSON que contém declarações sobre o usuário.

### <a name="permissions"></a>Permissões

Use as seguintes [permissões OIDC](v2-permissions-and-consent.md#openid-connect-scopes) para chamar a API UserInfo. `openid` é necessário, e os `profile` `email` escopos e garantem que informações adicionais sejam fornecidas na resposta.

|Tipo de permissão      | Permissões    |
|:--------------------|:---------------------------------------------------------|
|Delegado (conta corporativa ou de estudante) | OpenID (obrigatório), perfil, email |
|Delegado (conta Microsoft pessoal) | OpenID (obrigatório), perfil, email |
|Aplicativo | Não aplicável |

> [!TIP]
> Copie essa URL no navegador para obter um token para o ponto de extremidade de UserInfo, bem como um [token de ID](id-tokens.md) e substitua a ID do cliente e o URI de redirecionamento com o seu próprio. Observe que ele só solicita escopos para os escopos OpenID ou Graph e nada mais.  Isso é necessário, pois você não pode solicitar permissões para dois recursos diferentes na mesma solicitação de token.
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> Você pode usar esse token de acesso na próxima seção.

Assim como ocorre com qualquer outro token de Microsoft Graph, o token que você recebe aqui pode não ser um JWT. Se você tiver entrado em um usuário conta Microsoft, ele será um formato de token criptografado. Isso ocorre porque Microsoft Graph tem um padrão de emissão de token especial. Isso não afeta sua capacidade de usar o token de acesso para chamar o ponto de extremidade de UserInfo.

### <a name="calling-the-api"></a>Chamando a API

A API UserInfo dá suporte a GET e POST, de acordo com a especificação OIDC.

```http
GET or POST /oidc/userinfo HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6Il…
```

### <a name="userinfo-response"></a>Resposta de UserInfo

```jsonc
{
    "sub": "OLu859SGc2Sr9ZsqbkG-QbeLgJlb41KcdiPoLYNpSFA",
    "name": "Mikah Ollenburg", // names all require the “profile” scope.
    "family_name": " Ollenburg",
    "given_name": "Mikah",
    "email": "mikoll@contoso.com" //requires the “email” scope.
}
```

As declarações listadas aqui são todas as declarações que o ponto de extremidade do UserInfo pode retornar.  Esses são os mesmos valores que o aplicativo veria no token de [ID](id-tokens.md) emitido para o aplicativo.  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>Observações e advertências sobre o ponto de extremidade de UserInfo

* Se você quiser chamar esse ponto de extremidade de UserInfo, deverá usar o ponto de extremidade v 2.0.  Se você usar o ponto de extremidade v 1.0, receberá um token para o ponto de extremidade de UserInfo v 1.0, hospedado em login.microsoftonline.com.  É recomendável que todos os aplicativos e bibliotecas compatíveis com o OIDC usem o ponto de extremidade v 2.0 para garantir a compatibilidade.
* A resposta do ponto de extremidade UserInfo não pode ser personalizada.  Se você quiser personalizar declarações, use o mapeamento de [declarações]( active-directory-claims-mapping.md) para editar as informações retornadas nos tokens.
* A resposta do ponto de extremidade UserInfo não pode ser adicionada a.  Se você quiser obter declarações adicionais sobre o usuário, use [declarações opcionais]( active-directory-optional-claims.md) para adicionar novas declarações aos tokens.

## <a name="next-steps"></a>Próximas etapas

* [Examinar o conteúdo dos tokens de ID](id-tokens.md)
* [Personalizar o conteúdo de um token de ID usando declarações opcionais](active-directory-optional-claims.md)
* [Solicitar um token de acesso e um token de ID usando o protocolo OAuth2](v2-protocols-oidc.md)
