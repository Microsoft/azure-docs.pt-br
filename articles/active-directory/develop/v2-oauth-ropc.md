---
title: Use plataforma de identidade Microsoft para conectar usuários usando a concessão de credencial (ROPC) de senha de proprietário de recurso | Azure
description: Suporta fluxos de autenticação sem navegador usando a concessão de credencial de senha do proprietário do recurso.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: da111311de7b873be6453862ffcbd56fe546ea7f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482374"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>Plataforma de identidade da Microsoft e a credencial de senha de proprietário de recurso do OAuth 2.0

Dá suporte à plataforma de identidade do Microsoft as [concessão de credencial de senha de proprietário do recurso (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), que permite que um aplicativo para a entrada do usuário manipulando diretamente sua senha. O fluxo ROPC requer um alto grau de exposição de confiança e de usuário e você deve usar esse fluxo apenas quando os fluxos de outros, mais seguros, não podem ser usados.

> [!IMPORTANT]
>
> * O ponto de extremidade de plataforma de identidade do Microsoft só dá suporte a ROPC para locatários do Azure AD, contas não pessoais. Isso significa que você deve usar um terminal específico do locatário (`https://login.microsoftonline.com/{TenantId_or_Name}`) ou o terminal `organizations`.
> * Contas pessoais são convidadas a um locatário Azure AD não é possível usar ROPC.
> * Contas que não têm senhas não podem entrar por meio de ROPC. Para este cenário, recomendamos que você use um fluxo diferente para seu aplicativo em vez disso.
> * Se os usuários precisam usar a autenticação multifator (MFA) para fazer logon no aplicativo, eles serão bloqueados em vez disso.

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama a seguir mostra o fluxo do ROPC.

![Diagrama mostrando o proprietário do recurso de fluxo de credenciais de senha](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Solicitação de autorização

O fluxo ROPC é uma solicitação única &mdash;, envia a identificação do cliente e as credenciais do usuário para o IDP e, em seguida, recebe os tokens em retorno. O cliente deve solicitar o endereço de e-mail (UPN) e a senha do usuário antes de fazer isso. Imediatamente após uma solicitação bem-sucedida, o cliente deve liberar com segurança as credenciais do usuário da memória. Ele nunca deve salvá-los.

> [!TIP]
> Tente executar a solicitação no Postman!
> [![Tente executar essa solicitação no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parâmetro | Condição | DESCRIÇÃO |
| --- | --- | --- |
| `tenant` | Obrigatório | O locatário do diretório no qual você deseja fazer o login. Pode estar no formato de nome amigável ou de GUID. Este parâmetro não pode ser definido como `common` ou `consumers`, mas pode ser definido como `organizations`. |
| `grant_type` | Obrigatório | Deve ser definido como `password`. |
| `username` | Obrigatório | Endereço de email do usuário. |
| `password` | Obrigatório | A senha do usuário. |
| `scope` | Recomendado | Uma lista separada por espaço de [escopos](v2-permissions-and-consent.md) ou permissões que o aplicativo exige. Em um fluxo interativo, o administrador ou o usuário deve concordar com esses escopos antecipadamente. |

### <a name="successful-authentication-response"></a>Resposta de autenticação bem sucedida

O exemplo a seguir mostra uma resposta bem-sucedida de token:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parâmetro | Formatar | DESCRIÇÃO |
| --------- | ------ | ----------- |
| `token_type` | Cadeia de caracteres | Sempre defina como `Bearer`. |
| `scope` | Cadeia de caracteres separadas por espaço | Se um token de acesso foi retornado, esse parâmetro lista os escopos para os quais o token de acesso é válido. |
| `expires_in`| int | Número de segundos para o qual o token de acesso incluído é válido. |
| `access_token`| Cadeia de caracteres opaca | Emitido para os [escopos](v2-permissions-and-consent.md) que foram solicitados. |
| `id_token` | JWT | Emitido quando o parâmetro original `scope` inclui o escopo `openid`. |
| `refresh_token` | Cadeia de caracteres opaca | Emitido quando o parâmetro original `scope` inclui `offline_access`. |

Você pode usar o token de atualização para adquirir novos tokens de acesso e atualizar os tokens usando o mesmo fluxo descrito na documentação do [fluxo de código do OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Resposta de erro

Se o usuário não tiver fornecido o nome de usuário ou a senha corretos, ou se o cliente não tiver recebido o consentimento solicitado, a autenticação falhará.

| Erro | DESCRIÇÃO | Ação do cliente |
|------ | ----------- | -------------|
| `invalid_grant` | A autenticação falhou | As credenciais estavam incorretas ou o cliente não tem consentimento para os escopos solicitados. Se os escopos não forem concedidos, um `consent_required` erro será retornado. Se isso ocorrer, o cliente deve enviar o usuário para um prompt interativo usando uma visualização da Web ou um navegador. |
| `invalid_request` | A solicitação foi mal construída | O tipo de concessão não é suportado na `/common` ou `/consumers` contextos de autenticação.  Use `/organizations` em vez disso. |
| `invalid_client` | O aplicativo está configurado incorretamente | Isso pode acontecer se o `allowPublicClient` propriedade não está definida como true na [manifesto do aplicativo](reference-app-manifest.md). A propriedade `allowPublicClient` é necessária porque a concessão de ROPC não possui um URI de redirecionamento. O Microsoft Azure Active Directory não pode determinar se o aplicativo é um aplicativo cliente público ou um aplicativo cliente confidencial, a menos que a propriedade esteja definida. ROPC tem suporte apenas para aplicativos de cliente público. |

## <a name="learn-more"></a>Saiba mais

* Experimente ROPC por conta própria usando o [aplicativo de console de exemplo](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Para determinar se deve usar o ponto de extremidade v2.0, leia sobre [limitações da plataforma Microsoft identity](active-directory-v2-limitations.md).
