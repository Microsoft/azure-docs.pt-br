---
title: Entrar com concessão de credenciais de senha de proprietário do recurso | Azure
titleSuffix: Microsoft identity platform
description: Ofereça suporte a fluxos de autenticação sem navegador usando a concessão de ROPC (credenciais de senha de proprietário do recurso).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: bf469b79fa532978e904a54f32c80280706ee7cb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174573"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Plataforma de identidade da Microsoft e concessão de credenciais de senha de proprietário do recurso do OAuth 2.0

A plataforma de identidade da Microsoft dá suporte à [concessão de ROPC (credenciais de senha de proprietário de recurso) do OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.3), que permite que um aplicativo Conecte o usuário manipulando sua senha diretamente.  Este artigo descreve como programar diretamente no protocolo do seu aplicativo.  Quando possível, recomendamos que você use as MSAL (bibliotecas de autenticação da Microsoft) com suporte para [adquirir tokens e chamar APIs Web seguras](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Confira também os [aplicativos de exemplo que usam MSAL](sample-v2-code.md).

> [!WARNING]
> A Microsoft recomenda que você _não_ use o fluxo ROPC. Na maioria dos cenários, alternativas mais seguras estão disponíveis e são recomendadas. Esse fluxo requer um alto grau de confiança no aplicativo e carrega riscos que não estão presentes em outros fluxos. Você deve usar esse fluxo apenas quando outros fluxos mais seguros não puderem ser usados.

> [!IMPORTANT]
>
> * A plataforma Microsoft Identity só dá suporte a ROPC para locatários do Azure AD, não contas pessoais. Isso significa que você deve usar um terminal específico do locatário (`https://login.microsoftonline.com/{TenantId_or_Name}`) ou o terminal `organizations`.
> * Contas pessoais são convidadas a um locatário Azure AD não é possível usar ROPC.
> * Contas que não têm senhas não podem entrar por meio de ROPC. Para este cenário, recomendamos que você use um fluxo diferente para seu aplicativo em vez disso.
> * Se os usuários precisarem usar a [MFA (autenticação multifator)](../authentication/concept-mfa-howitworks.md) para fazer logon no aplicativo, eles serão bloqueados em vez disso.
> * Não há suporte para ROPC em cenários de [federação de identidade híbrida](../hybrid/whatis-fed.md) (por exemplo, Azure AD e ADFS usados para autenticar contas locais). Se os usuários forem redirecionados de página inteira para um provedor de identidade local, o Azure AD não poderá testar o nome de usuário e a senha nesse provedor de identidade. [No entanto, há suporte para a autenticação de passagem](../hybrid/how-to-connect-pta.md) com ROPC.
> * Uma exceção a um cenário de Federação de identidade híbrida seria o seguinte: a política de descoberta de realm inicial com AllowCloudPasswordValidation definido como TRUE permitirá que o fluxo de ROPC funcione para usuários federados quando a senha local for sincronizada com a nuvem. Para obter mais informações, consulte [habilitar a autenticação direta de ROPC de usuários federados para aplicativos herdados](../manage-apps/configure-authentication-for-federated-users-portal.md#enable-direct-ropc-authentication-of-federated-users-for-legacy-applications).

## <a name="protocol-diagram"></a>Diagrama do protocolo

O diagrama a seguir mostra o fluxo do ROPC.

![Diagrama mostrando o fluxo de credenciais de senha do proprietário do recurso](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Solicitação de autorização

O fluxo ROPC é uma solicitação única: envia a identificação do cliente e as credenciais do usuário para o IDP e, em seguida, recebe os tokens em retorno. O cliente deve solicitar o endereço de e-mail (UPN) e a senha do usuário antes de fazer isso. Imediatamente após uma solicitação bem-sucedida, o cliente deve liberar com segurança as credenciais do usuário da memória. Ele nunca deve salvá-los.

> [!TIP]
> Tente executar a solicitação no Postman!
> [![Tente executar a solicitação no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```HTTP
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | O locatário do diretório no qual você deseja fazer o login. Pode estar no formato de nome amigável ou de GUID. Este parâmetro não pode ser definido como `common` ou `consumers`, mas pode ser definido como `organizations`. |
| `client_id` | Obrigatório | A ID do aplicativo (cliente) que a página [Portal do Azure - Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuiu ao seu aplicativo. |
| `grant_type` | Obrigatório | Deve ser definido como `password`. |
| `username` | Obrigatório | Endereço de email do usuário. |
| `password` | Obrigatório | A senha do usuário. |
| `scope` | Recomendadas | Uma lista separada por espaço de [escopos](v2-permissions-and-consent.md) ou permissões que o aplicativo exige. Em um fluxo interativo, o administrador ou o usuário deve consentir com esses escopos com antecedência. |
| `client_secret`| Às vezes necessário | Se o seu aplicativo for um cliente público, o `client_secret` ou o `client_assertion` não poderão ser incluídos.  Se o aplicativo for um cliente confidencial, ele deverá ser incluído. |
| `client_assertion` | Às vezes necessário | Uma forma diferente de `client_secret`, gerada usando um certificado.  Confira [credenciais de certificado](active-directory-certificate-credentials.md) para obter mais detalhes. |

### <a name="successful-authentication-response"></a>Resposta de autenticação bem sucedida

O exemplo abaixo mostra uma resposta de token bem-sucedida:

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

| Parâmetro | Formatar | Descrição |
| --------- | ------ | ----------- |
| `token_type` | String | Sempre defina como `Bearer`. |
| `scope` | Cadeia de caracteres separadas por espaço | Se um token de acesso foi retornado, esse parâmetro lista os escopos para os quais o token de acesso é válido. |
| `expires_in`| INT | Número de segundos para o qual o token de acesso incluído é válido. |
| `access_token`| Cadeia de caracteres opaca | Emitido para os [escopos](v2-permissions-and-consent.md) que foram solicitados. |
| `id_token` | JWT | Emitido quando o parâmetro original `scope` inclui o escopo `openid`. |
| `refresh_token` | Cadeia de caracteres opaca | Emitido quando o parâmetro original `scope` inclui `offline_access`. |

Você pode usar o token de atualização para adquirir novos tokens de acesso e atualizar os tokens usando o mesmo fluxo descrito na documentação do [fluxo de código do OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Resposta de erro

Se o usuário não tiver fornecido o nome de usuário ou a senha corretos, ou se o cliente não tiver recebido o consentimento solicitado, a autenticação falhará.

| Erro | Descrição | Ação do cliente |
|------ | ----------- | -------------|
| `invalid_grant` | A autenticação falhou | As credenciais estavam incorretas ou o cliente não tem consentimento para os escopos solicitados. Se os escopos não forem concedidos, um erro `consent_required` será retornado. Se isso ocorrer, o cliente deve enviar o usuário para um prompt interativo usando uma visualização da Web ou um navegador. |
| `invalid_request` | A solicitação foi mal construída | O tipo de concessão não tem suporte nos contextos de autenticação `/common` ou `/consumers`.  Em vez disso, use `/organizations` ou uma ID de locatário. |

## <a name="learn-more"></a>Saiba mais

Para obter um exemplo de como usar o ROPC, consulte o exemplo de código do [aplicativo de console do .NET Core](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) no github.
