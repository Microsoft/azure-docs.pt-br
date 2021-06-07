---
title: Fluxo de código do dispositivo OAuth 2,0 | Azure
titleSuffix: Microsoft identity platform
description: Conectar usuários sem um navegador. Crie fluxos de autenticação incorporados e sem navegador usando a concessão de autorização do dispositivo.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 8c757f3e067aeac5d8145ca47b2eac145daba574
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88272443"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Plataforma de identidade da Microsoft e o fluxo de concessão de autorização de dispositivo OAuth 2,0

A plataforma de identidade da Microsoft dá suporte à [concessão de autorização de dispositivo](https://tools.ietf.org/html/rfc8628), que permite aos usuários entrar em dispositivos com restrição de entrada, como uma TV inteligente, um dispositivo IOT ou uma impressora.  Para habilitar esse fluxo, o dispositivo faz o usuário acessar uma página da Web em seu navegador em outro dispositivo para entrar.  Depois que o usuário entra, o dispositivo é capaz de obter tokens de acesso e atualizar tokens conforme necessário.

Este artigo descreve como programar diretamente no protocolo do seu aplicativo.  Quando possível, recomendamos que você use as MSAL (bibliotecas de autenticação da Microsoft) com suporte para [adquirir tokens e chamar APIs Web seguras](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Confira também os [aplicativos de exemplo que usam MSAL](sample-v2-code.md).

## <a name="protocol-diagram"></a>Diagrama do protocolo

Todo o fluxo de código do dispositivo é semelhante ao diagrama a seguir. Descrevemos cada uma das etapas mais adiante neste artigo.

![Fluxo de código do dispositivo](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Solicitação de autorização do dispositivo

O cliente deve primeiro verificar o servidor de autenticação para obter um código de usuário e de dispositivo usado para iniciar a autenticação. O cliente coleta essa solicitação do ponto de extremidade `/devicecode`. Nessa solicitação, o cliente também deve incluir as permissões que precisa adquirir do usuário. A partir do momento em que essa solicitação é enviada, o usuário tem apenas 15 minutos para entrar (o valor normal para `expires_in`), portanto, somente faça essa solicitação quando o usuário indicar que está pronto para entrar.

> [!TIP]
> Tente executar a solicitação no Postman!
> [![Tente executar a solicitação no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Obrigatório | Pode ser/Common,/consumers ou/Organizations.  Ele também pode ser o locatário de diretório do qual você deseja solicitar permissão no formato GUID ou nome amigável.  |
| `client_id` | Obrigatório | A **ID do Aplicativo (cliente)** que a experiência [Portal do Microsoft Azure - Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribui ao seu aplicativo. |
| `scope` | Obrigatório | Uma lista separada por espaços de [escopos](v2-permissions-and-consent.md) para os quais você deseja o consentimento do usuário.  |

### <a name="device-authorization-response"></a>Resposta de autorização do dispositivo

Uma resposta bem-sucedida será um objeto JSON que contém as informações necessárias para permitir que o usuário entre.

| Parâmetro | Formatar | Descrição |
| ---              | --- | --- |
|`device_code`     | String | Uma cadeia de caracteres longa usada para verificar a sessão entre o cliente e o servidor de autorização. O cliente usa esse parâmetro para solicitar o token de acesso do servidor de autorização. |
|`user_code`       | String | Uma cadeia de caracteres curta mostrada para o usuário que é usada para identificar a sessão em um dispositivo secundário.|
|`verification_uri`| URI | O URI que o usuário deve acessar com o `user_code` para entrar. |
|`expires_in`      | INT | O número de segundos antes que o `device_code` e o `user_code` expirem. |
|`interval`        | INT | O número de segundos que o cliente deve aguardar entre solicitações de sondagem. |
| `message`        | String | Uma cadeia de caracteres legível por humanos com instruções para o usuário. Ela pode ser localizada incluindo um **parâmetro de consulta** na solicitação do formulário `?mkt=xx-XX`, preenchendo o código de cultura do idioma apropriado. |

> [!NOTE]
> O `verification_uri_complete` campo de resposta não está incluído ou tem suporte no momento.  Mencionamos isso porque, se você ler o [padrão](https://tools.ietf.org/html/rfc8628) , verá que `verification_uri_complete` está listado como uma parte opcional do padrão de fluxo de código do dispositivo.

## <a name="authenticating-the-user"></a>Como autenticar o usuário

Depois de receber o `user_code` e `verification_uri` o, o cliente os exibe para o usuário, instruindo-os a entrar usando seu telefone celular ou navegador de PC.

Se o usuário se autenticar com uma conta pessoal (em/Common ou/consumers), será solicitado que você entre novamente para transferir o estado de autenticação para o dispositivo.  Eles também serão solicitados a fornecer consentimento, para garantir que estejam cientes das permissões que estão sendo concedidas.  Isso não se aplica a contas corporativas ou de estudante usadas para autenticação.

Enquanto o usuário está se autenticando no `verification_uri`, o cliente deverá estar sondando o ponto de extremidade `/token` para o token solicitado usando o `device_code`.

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parâmetro | Obrigatório | Descrição|
| -------- | -------- | ---------- |
| `tenant`  | Obrigatório | O mesmo alias de locatário ou locatário usado na solicitação inicial. |
| `grant_type` | Obrigatório | Precisa ser `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Obrigatório | Precisa corresponder à `client_id` usada na solicitação inicial. |
| `device_code`| Obrigatório | O `device_code` retornado na solicitação de autorização de dispositivo.  |

### <a name="expected-errors"></a>Erros esperados

O fluxo de código do dispositivo é um protocolo de sondagem, de modo que o cliente deve esperar receber erros antes de concluir a autenticação do usuário.

| Erro | Descrição | Ação do Cliente |
| ------ | ----------- | -------------|
| `authorization_pending` | O usuário não concluiu a autenticação, mas não cancelou o fluxo. | Repita a solicitação depois de pelo menos `interval` segundos. |
| `authorization_declined` | O usuário final negou a solicitação de autorização.| Interrompa a sondagem e reverta para um estado não autenticado.  |
| `bad_verification_code`| O `device_code` enviado para o `/token` ponto de extremidade não foi reconhecido. | Verifique se o cliente está enviando o `device_code` correto na solicitação. |
| `expired_token` | Pelo menos `expires_in` segundos foram decorridos e a autenticação não é mais possível com este `device_code`. | Pare a sondagem e a reversão para um estado não autenticado. |

### <a name="successful-authentication-response"></a>Resposta de autenticação bem-sucedida

Uma resposta de token bem-sucedida será assim:

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
| `token_type` | String| Sempre "Portador". |
| `scope` | Cadeia de caracteres separadas por espaço | Se um token de acesso for retornado, isso listará os escopos para os quais o token de acesso é válido. |
| `expires_in`| INT | Número de segundos antes que o token de acesso incluído seja válido. |
| `access_token`| Cadeia de caracteres opaca | Emitido para os [escopos](v2-permissions-and-consent.md) que foram solicitados.  |
| `id_token`   | JWT | Emitido quando o parâmetro original `scope` inclui o escopo `openid`.  |
| `refresh_token` | Cadeia de caracteres opaca | Emitido quando o parâmetro original `scope` inclui `offline_access`.  |

Você pode usar o token de atualização para adquirir novos tokens de acesso e atualizar tokens usando o mesmo fluxo documentado na [documentação do fluxo de código OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).
