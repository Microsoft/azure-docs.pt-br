---
title: Tokens de acesso da plataforma de identidade da Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os tokens de acesso emitidos pelos pontos de extremidade do Azure AD v 1.0 e da plataforma de identidade da Microsoft (v 2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: hirsin
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: e1dcd52660ff43a93c6a170912fea5a5847fe9d3
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575747"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Tokens de acesso da plataforma de identidade da Microsoft

Os tokens de acesso permitem que os clientes chamem com segurança APIs da Web protegidas e são usados por APIs da Web para executar autenticação e autorização. De acordo com a especificação OAuth, os tokens de acesso são cadeias de caracteres opacas sem um formato definido – alguns provedores de identidade (IDPs) usam GUIDs, outros usam BLOBs criptografados. A plataforma de identidade da Microsoft usa uma variedade de formatos de token de acesso, dependendo da configuração da API que aceita o token. [APIs personalizadas registradas por desenvolvedores](quickstart-configure-app-expose-web-apis.md) na plataforma de identidade da Microsoft podem escolher entre dois formatos diferentes de tokens Web JSON (JWTs), chamados "v1" e "v2", e APIs desenvolvidas pela Microsoft, como Microsoft Graph ou APIs no Azure, têm formatos de token proprietários adicionais. Esses formatos proprietários podem ser tokens criptografados, JWTs ou tokens especiais semelhantes ao JWT que não serão validados.

Os clientes devem tratar tokens de acesso como cadeias de caracteres opacas porque o conteúdo do token destina-se apenas ao recurso (a API). *Somente* para fins de validação e depuração, os desenvolvedores podem decodificar JWTs usando um site como [JWT.ms](https://jwt.ms). No entanto, lembre-se de que os tokens recebidos para uma API da Microsoft talvez nem sempre sejam um JWT e que você nem sempre possa decodificá-los.

Para obter detalhes sobre o que está dentro do token de acesso, os clientes devem usar os dados de resposta do token retornados com o token de acesso para o cliente. Quando o cliente solicita um token de acesso, a plataforma de identidade da Microsoft também retorna alguns metadados sobre o token de acesso para o consumo do aplicativo. Essas informações incluem a data de expiração do token de acesso e os escopos para os quais ele é válido. Esses dados permitem ao aplicativo realizar o cache inteligente dos tokens de acesso sem precisar analisar o token de acesso em si.

Consulte as seções a seguir para saber como sua API pode validar e usar as declarações dentro de um token de acesso.  

> [!NOTE]
> Toda a documentação nesta página, exceto onde anotada, aplica-se somente a tokens emitidos para APIs que você registrou.  Ele não se aplica a tokens emitidos para APIs de propriedade da Microsoft, nem pode usar esses tokens para validar como a plataforma de identidade da Microsoft emitirá tokens para uma API que você criar.  

## <a name="token-formats-and-ownership"></a>Formatos e propriedade de token

### <a name="v10-and-v20"></a>v 1.0 e v 2.0 

Há duas versões de tokens de acesso disponíveis na plataforma Microsoft Identity: v 1.0 e v 2.0.  Essas versões regem quais declarações estão no token, garantindo que uma API da Web possa controlar de que forma seus tokens se parecem. As APIs da Web têm uma dessas selecionadas como padrão durante o registro-v 1.0 para aplicativos somente AD do Azure e v 2.0 para aplicativos que dão suporte a contas de consumidor.  Isso é controlável por aplicativos que usam a `accessTokenAcceptedVersion` configuração no [manifesto do aplicativo](reference-app-manifest.md#manifest-reference), onde `null` e `1` resulta em tokens v 1.0 e `2` resulta em tokens v 2.0.

### <a name="what-app-is-a-token-for"></a>Qual aplicativo é um token "para"?

Há duas partes envolvidas em uma solicitação de token de acesso: o cliente, quem solicita o token e o recurso (a API) que aceita o token quando a API é chamada. A `aud` declaração em um token indica o recurso para o qual o token se destina (seu *público*). Os clientes usam o token, mas não devem entender nem tentar analisá-lo. Os recursos aceitam o token.  

A plataforma de identidade da Microsoft dá suporte à emissão de qualquer versão de token de qualquer ponto de extremidade de versão. eles não estão relacionados. É por isso que uma configuração de recurso `accessTokenAcceptedVersion` `2` significa que um cliente que chama o ponto de extremidade v 1.0 para obter um token para essa API receberá um token de acesso v 2.0.  Os recursos sempre possuem seus tokens (aqueles com sua `aud` declaração) e são os únicos aplicativos que podem alterar seus detalhes de token. É por isso que alterar as [declarações opcionais](active-directory-optional-claims.md) do token de acesso para seu *cliente* não altera o token de acesso recebido quando um token é solicitado `user.read` , que pertence ao recurso Microsoft Graph.

### <a name="sample-tokens"></a>Tokens de exemplo

Os tokens v1.0 e v2.0 são semelhantes e contêm muitas das mesmas declarações. Um exemplo de cada um é fornecido aqui. No entanto, esses tokens de exemplo não serão [validados](#validating-tokens), pois as chaves foram giradas antes da publicação, e as informações pessoais foram removidas delas.

#### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Exiba esse token da v1.0 em [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

#### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Exiba esse token v2.0 em [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Declarações em tokens de acesso

Os JWTs (Token Web JSON) são divididos em três partes:

* **Cabeçalho** – fornece informações sobre como [validar o token](#validating-tokens), incluindo informações sobre o tipo de token e como ele foi assinado.
* **Conteúdo** – contém todos os dados importantes sobre o usuário ou o aplicativo que está tentando chamar o serviço.
* **Assinatura** – é a matéria-prima usada para validar o token.

Cada parte é separada por um ponto (`.`) e codificada em Base64 separadamente.

As declarações estão presentes somente se existe um valor para preenchê-lo. Seu aplicativo não deve usar uma dependência em uma declaração que esteja presente. Os exemplos incluem `pwd_exp` (nem todos os locatários exigem que as senhas expirem) e `family_name` (os fluxos de[credencial do cliente](v2-oauth2-client-creds-grant-flow.md) estão em nome dos aplicativos que não têm nomes). As declarações usadas para validação do token de acesso sempre estarão presentes.

Algumas declarações são usadas para ajudar o Azure AD a proteger tokens no caso de reutilização. Elas são marcadas como não sendo para consumo público na descrição como "Opacas". Essas declarações podem ou não aparecer em um token, e novas declarações podem ser adicionadas sem aviso prévio.

### <a name="header-claims"></a>Declarações de cabeçalho

|Declaração | Formatar | Descrição |
|--------|--------|-------------|
| `typ` | Cadeia de caracteres – sempre "JWT" | Indica que o token é um JWT.|
| `nonce` | String | Um identificador exclusivo usado para proteger contra ataques de reprodução de token. O recurso pode registrar esse valor para proteger contra reproduções. |
| `alg` | String | Indica o algoritmo que foi usado para assinar o token, por exemplo, "RS256" |
| `kid` | String | Especifica a impressão digital da chave pública que é usada para assinar esse token. Emitido nos tokens de acesso v1.0 e v2.0. |
| `x5t` | String | Funciona da mesma forma (em uso e valor) que `kid`. `x5t` é uma declaração herdada emitida somente em tokens de acesso da v1.0 para fins de compatibilidade. |

### <a name="payload-claims"></a>Declarações de conteúdo

| Declaração | Formatar | Descrição |
|-----|--------|-------------|
| `aud` | Cadeia de caracteres, um URI de ID de aplicativo ou GUID | Identifica o destinatário pretendido do token-seu público-alvo.  Sua API deve validar esse valor e rejeitar o token se o valor não corresponder. Em tokens v 2.0, essa é sempre a ID do cliente da API, enquanto em tokens v 1.0 ele pode ser a ID do cliente ou o URI de recurso usado na solicitação, dependendo de como o cliente solicitou o token.|
| `iss` | Cadeia de caracteres, um URI STS | Identifica o STS (Serviço de Token de Segurança) que constrói e retorna o token e o locatário do Azure AD no qual o usuário foi autenticado. Se o token emitido for um token v2.0 (consulte a declaração `ver`), o URI será finalizado em `/v2.0`. O GUID que indica que o usuário é um consumidor da conta da Microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad`. Seu aplicativo pode usar a parte GUID da declaração para restringir o conjunto de locatários que podem entrar no aplicativo, se aplicável. |
|`idp`| Cadeia de caracteres, normalmente um URI de STS | Registra o provedor de identidade que autenticou a entidade do token. Esse valor é idêntico ao valor da declaração Emissor, a menos que a conta de usuário não esteja no mesmo locatário que o emissor – convidados, por exemplo. Se a declaração não estiver presente, isso significará que o valor de `iss` poderá ser usado.  Para as contas pessoais usadas em um contexto organizacional (por exemplo, uma conta pessoal convidada para um locatário do Azure AD), a declaração `idp`pode ser 'live.com' ou um URI de STS que contém o locatário da conta Microsoft`9188040d-6c67-4c5b-b112-36a304b66dad`. |
| `iat` | int, um carimbo de data/hora UNIX | "Emitido em" indica quando ocorreu a autenticação desse token. |
| `nbf` | int, um carimbo de data/hora UNIX | A declaração "nbf" (não antes) identifica a hora antes da qual o JWT não deve ser aceito para processamento. |
| `exp` | int, um carimbo de data/hora UNIX | A declaração "exp" (hora de expiração) identifica a hora de expiração ou a hora após ela na qual o JWT não deve ser aceito para processamento. É importante observar que um recurso pode rejeitar o token antes dessa hora também, como quando uma alteração na autenticação é necessária ou se uma revogação de token foi detectada. |
| `aio` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure AD para registrar os dados para reutilização de token. Os recursos não devem usar essa declaração. |
| `acr` | Cadeia de caracteres, um "0" ou "1" | Presente apenas em tokens da v1.0. A declaração da "Classe de contexto de autenticação". Um valor "0" indica que a autenticação do usuário final não atendeu aos requisitos da ISO/IEC 29115. |
| `amr` | Matriz de cadeias de caracteres JSON | Presente apenas em tokens da v1.0. Identifica como o assunto do token foi autenticado. Confira [a seção de declarações de amr](#the-amr-claim) para obter mais detalhes. |
| `appid` | Cadeia de caracteres, um GUID | Presente apenas em tokens da v1.0. A ID do aplicativo do cliente que usa o token. O aplicativo pode agir como ele próprio ou em nome de um usuário. A ID do aplicativo normalmente representa um objeto de aplicativo, mas também pode representar um objeto de entidade de serviço no AD do Azure. |
| `azp` | Cadeia de caracteres, um GUID | Somente os presentes em tokens v2.0, uma substituição para `appid`. A ID do aplicativo do cliente que usa o token. O aplicativo pode agir como ele próprio ou em nome de um usuário. A ID do aplicativo normalmente representa um objeto de aplicativo, mas também pode representar um objeto de entidade de serviço no AD do Azure. |
| `appidacr` | "0", "1" ou "2" | Presente apenas em tokens da v1.0. Indica como o cliente foi autenticado. Para um cliente público, o valor é "0". Se a ID do cliente e o segredo do cliente são usados, o valor é "1". Se um certificado do cliente foi usado para autenticação, o valor é "2". |
| `azpacr` | "0", "1" ou "2" | Somente os presentes em tokens v2.0, uma substituição para `appidacr`. Indica como o cliente foi autenticado. Para um cliente público, o valor é "0". Se a ID do cliente e o segredo do cliente são usados, o valor é "1". Se um certificado do cliente foi usado para autenticação, o valor é "2". |
| `preferred_username` | String | O nome de usuário principal que representa o usuário. Ele pode ser um endereço de email, número de telefone ou nome de usuário genérico sem um formato especificado. Seu valor é mutável e pode ser alterado ao longo do tempo. Uma vez que é mutável, esse valor não deve ser usado para tomar decisões de autorização.  Ele pode ser usado para dicas de nome de usuário, no entanto, e na interface do usuário legível como um nome de usuário. O escopo `profile` é necessário para receber essa declaração. Presente somente em tokens v 2.0. |
| `name` | String | Fornece um valor legível por humanos que identifica a entidade do token. Não há garantia de que o valor seja exclusivo. Ele é mutável e foi projetado para ser usado apenas para fins de exibição. O escopo `profile` é necessário para receber essa declaração. |
| `scp` | Cadeia de caracteres, uma lista de escopos separados por espaços | O conjunto de escopos expostos pelo aplicativo para o qual o aplicativo cliente solicitou (e recebeu) o consentimento. O aplicativo deve verificar se os escopos são escopos válidos expostos pelo aplicativo e tomar decisões de autorização de acordo com o valor desses escopos. Incluído apenas para [tokens de usuário](#user-and-application-tokens). |
| `roles` | Matriz de cadeias de caracteres, uma lista de permissões | O conjunto de permissões expostas pelo aplicativo que o aplicativo ou usuário solicitante recebeu permissão para chamar. Em [tokens de aplicativo](#user-and-application-tokens), isso é usado durante o fluxo de credenciais do cliente ([v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) no lugar dos escopos do usuário.  Para [tokens de usuário](#user-and-application-tokens), isso é populado com as funções às quais o usuário foi atribuído no aplicativo de destino. |
| `wids` | Matriz de GUIDs [RoleTemplateID](../roles/permissions-reference.md#role-template-ids) | Denota as funções de todo o locatário atribuídas a esse usuário, da seção de funções presentes na [página funções de administrador](../roles/permissions-reference.md#role-template-ids).  Esta declaração é configurada por aplicativo, por meio da propriedade `groupMembershipClaims` do [manifesto do aplicativo](reference-app-manifest.md).  É necessário configurá-la como "All" or "DirectoryRole".  Pode não estar presente em tokens obtidos por meio do fluxo implícito devido a problemas de comprimento do token. |
| `groups` | Matriz JSON de GUIDs | Fornece IDs de objetos que representam as associações de grupo do assunto. Esses valores são exclusivos (consulte a ID de objeto) e podem ser usados com segurança para gerenciar o acesso, como a imposição da autorização para acessar um recurso. Os grupos incluídos na declaração dos grupos são configurados por aplicativo, por meio da propriedade `groupMembershipClaims` do [manifesto do aplicativo](reference-app-manifest.md). Um valor de NULL excluirá todos os grupos, um valor de "grupo de segurança" incluirá apenas Active Directory associações de grupos de segurança, e um valor de "All" incluirá os grupos de segurança e as listas de distribuição de Microsoft 365. <br><br>Consulte a declaração `hasgroups` abaixo para obter detalhes de como usar a declaração `groups` com a concessão implícita. <br>Para outros fluxos, se o número de grupos a que o usuário pertence ultrapassar determinado limite (150 para SAML, 200 para JWT), uma declaração excedente será adicionada às fontes de declaração que apontam para o ponto de extremidade do Microsoft Graph que contém a lista de grupos do usuário. |
| `hasgroups` | Boolean | Se houver algum, sempre `true`, indicando que o usuário pertence a pelo menos um grupo. Usado no lugar da declaração `groups` de JWTs em fluxos de concessão implícitos se a declaração completa de grupos ultrapassar o fragmento de URI para além dos limites de extensão da URL (atualmente, 6 ou mais grupos). Indica que o cliente deve usar a API do Microsoft Graph para determinar os grupos do usuário (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`). |
| `groups:src1` | Objeto JSON | Para solicitações de token sem limite de tamanho (consulte `hasgroups` acima), mas ainda muito grandes para o token, será incluído um link para a lista completa de grupos do usuário. Para JWTs na forma de declaração distribuída, para SAML como uma nova declaração no lugar da declaração `groups`. <br><br>**Valor de exemplo de JWT**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | String | O item mais importante sobre o qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído nem reutilizado. Pode ser usado para executar verificações de autorização de forma segura, por exemplo, quando o token é usado para acessar um recurso, e pode ser usado como uma chave nas tabelas de banco de dados. Como a entidade está sempre presente nos tokens emitidos pelo Azure AD, é recomendável usar esse valor em um sistema de autorização de uso geral. O assunto é, no entanto, um identificador de paridade - é exclusivo a uma ID de aplicativo específica. Portanto, se um único usuário entra em dois aplicativos diferentes usando duas IDs de cliente diferentes, esses aplicativos receberão dois valores diferentes para a declaração do assunto. Isso pode ou não ser desejável, dependendo dos requisitos de arquitetura e de privacidade. Confira também a declaração de `oid` (que permanece a mesma em aplicativos dentro de um locatário). |
| `oid` | Cadeia de caracteres, um GUID | O identificador imutável de um objeto na plataforma de identidade da Microsoft, nesse caso, uma conta de usuário. Também pode ser usada para realizar verificações de autorização com segurança e como uma chave em tabelas de banco de dados. Essa ID identifica exclusivamente o usuário entre os aplicativos - dois aplicativos diferentes autenticando o mesmo usuário receberão o mesmo valor na declaração `oid`. Portanto, `oid` pode ser usada ao fazer consultas nos serviços online da Microsoft, como o Microsoft Graph. O Microsoft Graph retornará essa ID como a propriedade `id` para uma determinada [conta de usuário](/graph/api/resources/user). Como o `oid` permite que vários aplicativos correlacionem usuários, o escopo `profile` é necessário a fim de receber essa declaração. Observe que, se um único usuário existir em vários locatários, o usuário conterá uma ID de objeto diferentes em cada locatário - são consideradas contas diferentes, mesmo que o usuário faça logon em cada conta com as mesmas credenciais. |
| `tid` | Cadeia de caracteres, um GUID | Representa o locatário do Azure AD do qual o usuário é proveniente. Para contas corporativas e de estudante, o GUID é a ID de locatário imutável da organização à qual o usuário pertence. Para contas pessoais, o valor é `9188040d-6c67-4c5b-b112-36a304b66dad`. O escopo `profile` é necessário para receber essa declaração. |
| `unique_name` | String | Presente apenas em tokens da v1.0. Fornece um valor legível que identifica a entidade do token. Não há garantia de que esse valor seja exclusivo dentro de um locatário e ele deve ser usado apenas para fins de exibição. |
| `uti` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure para revalidar tokens. Os recursos não devem usar essa declaração. |
| `rh` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure para revalidar tokens. Os recursos não devem usar essa declaração. |
| `ver` | Cadeia de caracteres, `1.0` ou `2.0` | Indica a versão do token de acesso. |

**Declaração de excedente de grupos**

Para garantir que o tamanho do token não exceda os limites de tamanho do cabeçalho HTTP, o Azure AD limita o número de IDs de objeto que ele inclui na declaração de grupos. Se um usuário for membro de mais grupos do que o limite excedente (150 para tokens SAML, 200 para tokens JWT e apenas 6 se for emitido por meio do fluxo implícito), o Azure AD não emitirá a declaração de grupos no token. Em vez disso, ele inclui uma declaração excedente no token que indica ao aplicativo consultar a API do Microsoft Graph para recuperar a associação de grupo do usuário.

```JSON
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

Use o `BulkCreateGroups.ps1` fornecidos na pasta [Scripts de Criação de Aplicativo](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) para ajudar a testar cenários excedentes.

#### <a name="v10-basic-claims"></a>Declarações básicas v1.0

As declarações a seguir serão incluídas nos tokens v1.0, se aplicável, mas não nos tokens v2.0 por padrão. Caso você esteja usando a v2.0 e precise de uma dessas declarações, solicite-as usando [declarações opcionais](active-directory-optional-claims.md).

| Declaração | Formatar | Descrição |
|-----|--------|-------------|
| `ipaddr`| String | O endereço IP por meio do qual o usuário se autenticou. |
| `onprem_sid`| Cadeia de caracteres, em [formato SID](/windows/desktop/SecAuthZ/sid-components) | Nos casos em que o usuário tem uma autenticação local, essa declaração fornece o SID. Você pode usar `onprem_sid` para autorização em aplicativos herdados.|
| `pwd_exp`| int, um carimbo de data/hora UNIX | Indica quando a senha do usuário expira. |
| `pwd_url`| String | Uma URL para a qual os usuários podem ser enviados para redefinir suas senhas. |
| `in_corp`| booleano | Indica se o cliente está se conectando da rede corporativa. Se não estiver, a declaração não será incluída. |
| `nickname`| String | Um nome adicional para o usuário, separado do nome ou sobrenome.|
| `family_name` | String | Fornece o sobrenome ou o nome da família do usuário, conforme definido no objeto de usuário. |
| `given_name` | String | Fornece o nome ou nome especificado do usuário, conforme definido no objeto de usuário. |
| `upn` | String | O nome de usuário do usuário. Pode ser um número de telefone, um endereço de email ou uma cadeia de caracteres sem formatação. Deve ser usado apenas para fins de exibição e para fornecer dicas de nome de usuário em cenários de reautenticação. |

#### <a name="the-amr-claim"></a>A declaração `amr`

As identidades da Microsoft podem ser autenticadas em uma variedade de maneiras, que podem ser relevantes para o seu aplicativo. A declaração `amr` é uma matriz que pode conter vários itens, como `["mfa", "rsa", "pwd"]`, para uma autenticação que usou uma senha e o aplicativo Authenticator.

| Valor | Descrição |
|-----|-------------|
| `pwd` | Autenticação de senha, uma senha da Microsoft do usuário ou um segredo do cliente do aplicativo. |
| `rsa` | A autenticação se baseava na prova de uma chave RSA, por exemplo, com o [aplicativo Microsoft Authenticator](https://aka.ms/AA2kvvu). Isso inclui se a autenticação foi realizada por um JWT autoassinado com um certificado X509 de propriedade do serviço. |
| `otp` | Uma senha única usando um email ou uma mensagem de texto. |
| `fed` | Uma declaração de autenticação federada (por exemplo, JWT ou SAML) foi usada. |
| `wia` | Autenticação Integrada do Windows |
| `mfa` | A [autenticação multifator](../authentication/concept-mfa-howitworks.md) foi usada. Quando isso estiver presente, os outros métodos de autenticação também serão incluídos. |
| `ngcmfa` | Equivalente a `mfa`, usado para o provisionamento de alguns tipos de credenciais avançadas. |
| `wiaormfa`| O usuário utilizou o Windows ou uma credencial MFA para se autenticar. |
| `none` | Nenhuma autenticação foi realizada. |

## <a name="validating-tokens"></a>Validando tokens

Nem todos os aplicativos devem validar tokens. Somente em cenários específicos, os aplicativos devem validar um token:

* As [APIs da Web](quickstart-configure-app-expose-web-apis.md) devem validar tokens de acesso enviados a eles por um cliente.  Eles devem aceitar somente tokens que contenham sua `aud` declaração.
* Aplicativos Web confidenciais como ASP.NET Core devem validar tokens de ID enviados a eles por meio do navegador do usuário no fluxo híbrido, antes de permitir o acesso aos dados de um usuário ou estabelecer uma sessão.

Se nenhum dos cenários acima for aplicável, seu aplicativo não se beneficiará da validação do token e poderá apresentar um risco de segurança e confiabilidade se as decisões forem tomadas com base na validade do token.  Clientes públicos como aplicativos nativos ou SPAs não se beneficiam da validação de tokens – o aplicativo se comunica diretamente com o IDP, portanto, a proteção SSL garante que os tokens sejam válidos.

APIs e aplicativos Web só devem validar tokens que tenham uma `aud` declaração que corresponda a seu aplicativo; outros recursos podem ter regras de validação de token personalizadas. Por exemplo, os tokens para Microsoft Graph não serão validados de acordo com essas regras devido ao formato proprietário. Validar e aceitar tokens destinados a outro recurso é um exemplo do problema de [Deputy confuso](https://cwe.mitre.org/data/definitions/441.html) .

Se seu aplicativo precisar validar um id_token ou um access_token de acordo com o acima, seu aplicativo deverá primeiro validar a assinatura e o emissor do token em relação aos valores no documento de descoberta de OpenID. Por exemplo, a versão independente de locatário do documento está localizada em [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration).

As informações a seguir são fornecidas para aqueles que desejam entender o processo subjacente. O middleware do Azure AD tem funcionalidades internas para validar os tokens de acesso e você pode navegar pelas nossas [amostras](sample-v2-code.md) para encontrar uma na linguagem de sua escolha. Há também várias bibliotecas de software livre de terceiros disponíveis para validação de JWT – há pelo menos uma opção para quase todas as plataformas e idiomas. Para obter mais informações sobre as bibliotecas de autenticação do Azure AD e exemplos de código, consulte as [bibliotecas de autenticação](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Validação da assinatura

Um JWT contém três segmentos, que são separados pelo caractere `.` . O primeiro segmento é conhecido como o **cabeçalho**, o segundo como o **corpo** e o terceiro como a **assinatura**. O segmento de assinatura pode ser usado para validar a autenticidade do token, de modo que seu aplicativo possa confiar nele.

Os tokens emitidos pelo Azure AD são assinados usando algoritmos de criptografia assimétrica padrão do setor, como o RS256. O cabeçalho do JWT contém informações sobre o método de criptografia e a chave usados para assinar o token:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

A declaração `alg` indica o algoritmo usado para assinar o token, enquanto a declaração `kid` indica a chave pública privada usada para validar o token.

Em qualquer ponto no tempo, o AD do Azure pode assinar um id_token usando qualquer um de um determinado conjunto de pares de chaves públicas-privadas. O AD do Azure gira o possível conjunto de chaves em intervalos periódicos, de modo que o aplicativo deve ser escrito para tratar essas mudanças de chave automaticamente. Uma frequência razoável para verificar se há atualizações para as chaves públicas usadas pelo Azure AD é a cada 24 horas.

Você pode adquirir os dados de chave de assinatura necessários para validar a assinatura usando o [documento de metadados do OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) localizado em:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Experimente essa [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) em um navegador!

Esses metadados documentam:

* É um objeto JSON que contém várias informações úteis, como o local dos vários pontos de extremidade necessários para realizar a autenticação do OpenID Connect.
* Inclui um `jwks_uri`, que fornece o local do conjunto de chaves públicas usadas para assinar tokens. O JWK (Chave da Web JSON) localizado no `jwks_uri` contém todas as informações de chave pública em uso naquele momento específico.  O formato JWK é descrito em [RFC 7517](https://tools.ietf.org/html/rfc7517).  Seu aplicativo pode usar a declaração `kid` no cabeçalho do JWT para selecionar qual chave pública neste documento foi usada para assinar um token específico. Assim, ele pode realizar a validação da assinatura usando a chave pública correta e o algoritmo indicado.

> [!NOTE]
> É recomendável usar a declaração `kid` para validar o token. Embora os tokens v 1.0 contenham as `x5t` `kid` declarações e, os tokens v 2.0 contêm apenas a `kid` declaração.

Executar a validação da assinatura está fora do escopo deste documento – há muitas bibliotecas de software livre disponíveis para ajudar você a fazer isso, caso seja necessário.  No entanto, a plataforma de identidade da Microsoft tem uma extensão de assinatura de token para os padrões-chaves de assinatura personalizadas.

Se o aplicativo tiver chaves de assinatura personalizadas como resultado do uso do recurso [claims-mapping](active-directory-claims-mapping.md), será necessário acrescentar um parâmetro de consulta `appid` com a ID do aplicativo para obter um `jwks_uri` que aponta para as informações de chave de assinatura do aplicativo, que devem ser usadas para validação. Por exemplo: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contém uma `jwks_uri` de `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

### <a name="claims-based-authorization"></a>Autorização baseada em declarações

A lógica de negócios do aplicativo determinará esta etapa; alguns métodos comuns de autorização são apresentados abaixo.

* Verifique a declaração `scp` ou `roles` para confirmar se todos os escopos presentes correspondem àqueles expostos pela API e permitem que o cliente execute a ação solicitada.
* Verifique se o cliente de chamada pode chamar sua API usando a declaração `appid`.
* Valide o status de autenticação do cliente que fez a chamada usando `appidacr` – ele não deve ser 0 se os clientes públicos não têm permissão para chamar a API.
* Verifique em uma lista das últimas declarações `nonce` para confirmar se o token não está sendo reproduzido.
* Verifique se o `tid` corresponde a um locatário que tem permissão para chamar sua API.
* Use a declaração `amr` para verificar se o usuário realizou a MFA. Isso deve ser imposto por meio do [acesso condicional](../conditional-access/overview.md).
* Se você solicitou as declarações `roles` ou `groups` no token de acesso, verifique se o usuário está no grupo com permissão para executar essa ação.
  * Para os tokens recuperados usando o fluxo implícito, provavelmente, você precisará conferir o [Microsoft Graph](https://developer.microsoft.com/graph/) para esses dados, pois eles costumam ser muito grandes para se ajustarem ao token.

## <a name="user-and-application-tokens"></a>Tokens de usuário e aplicativo

Seu aplicativo pode receber tokens para o usuário (o fluxo geralmente é discutido) ou diretamente de um aplicativo (por meio do [fluxo de credenciais do cliente](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)). Esses tokens somente de aplicativo indicam que essa chamada é proveniente de um aplicativo e não tem nenhum usuário dando suporte a ela. Esses tokens são manipulados basicamente da mesma forma:

* Use `roles` para ver as permissões que foram concedidas ao assunto do token.
* Use `oid` ou `sub` para validar que a entidade de serviço de chamada é a esperada.

Se seu aplicativo precisar distinguir entre tokens de acesso somente de aplicativo e tokens de acesso para usuários, use a `idtyp` [declaração opcional](active-directory-optional-claims.md).  Ao adicionar a `idtyp` declaração ao `accessToken` campo e verificar o valor `app` , você pode detectar tokens de acesso somente de aplicativo.  Tokens de ID e tokens de acesso para usuários não terão a `idtyp` declaração incluída.

## <a name="token-revocation"></a>Revogação de token

Os tokens de atualização podem ser invalidados ou revogados a qualquer momento por motivos diferentes. Eles se encaixam em duas categorias principais: tempos limite e revogações.

### <a name="token-timeouts"></a>Tempos limite de token

Com a [configuração de tempo de vida do token](active-directory-configurable-token-lifetimes.md), é possível alterar o tempo de vida dos tokens de atualização.  É normal e esperado que alguns tokens não sejam usados (por exemplo, o usuário não abre o aplicativo por três meses) e, portanto, expirem.  Os aplicativos encontrarão cenários em que o servidor de logon rejeita um token de atualização devido à sua idade.

* MaxInactiveTime: Se o token de atualização não tiver sido usado no tempo determinado pelo MaxInactiveTime, ele não será válido.
* MaxSessionAge: se MaxAgeSessionMultiFactor ou MaxAgeSessionSingleFactor tiver sido definido como algo diferente do padrão (até revogado), será necessária a reautenticação depois de decorrido o tempo definido em MaxAgeSession*.
* Exemplos:
  * O locatário tem uma MaxInactiveTime de cinco dias e o usuário entrou em férias por uma semana. Então, Azure AD não obteve uma nova solicitação de token do usuário em sete dias. Na próxima vez que o usuário solicitar um novo token, ele descobrirá que o token de atualização foi revogado e deverá inserir suas credenciais novamente.
  * Um aplicativo confidencial tem um MaxAgeSessionSingleFactor de um dia. Se um usuário fizer logon na segunda-feira e na terça-feira (após decorridas 25 horas), ele deverá se autenticar novamente.

### <a name="revocation"></a>Revogação

Os tokens de atualização podem ser revogados pelo servidor devido a uma alteração nas credenciais, ao uso ou à ação do administrador.  Os tokens de atualização se enquadram em duas classes – aqueles emitidos para clientes confidenciais (a coluna mais à direita) e aqueles emitidos para clientes públicos (todas as outras colunas).

| Alterar | Cookie baseado em senha | Token baseado em senha | Cookie não baseado em senha | Token não baseados em senha | Token de cliente confidencial |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| A senha expira | Permanece ativo | Permanece ativo | Permanece ativo | Permanece ativo | Permanece ativo |
| Senha alterada pelo usuário | Revogado | Revogado | Permanece ativo | Permanece ativo | Permanece ativo |
| Usuário faz SSPR | Revogado | Revogado | Permanece ativo | Permanece ativo | Permanece ativo |
| Administrador redefine senha | Revogado | Revogado | Permanece ativo | Permanece ativo | Permanece ativo |
| Usuário revoga seus tokens de atualização [por meio do PowerShell](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revogado | Revogado | Revogado | Revogado | Revogado |
| O administrador revoga todos os tokens de atualização do usuário [por meio do PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revogado | Revogado |Revogado | Revogado | Revogado |
| Logout único ([v1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v2.0](v2-protocols-oidc.md#single-sign-out)) na Web | Revogado | Permanece ativo | Revogado | Permanece ativo | Permanece ativo |

#### <a name="non-password-based"></a>Não baseado em senha

Um logon *não baseado em senha* é aquele em que o usuário não digitou uma senha para obtê-lo. Entre os exemplos de logon não baseado em senha estão:

- Usando seu rosto com o Windows Hello
- Chave FIDO2
- SMS
- Voz
- PIN

Confira [tokens de atualização primária](../devices/concept-primary-refresh-token.md) para obter mais detalhes sobre os tokens de atualização primária.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [`id_tokens` no Azure AD](id-tokens.md).
* Saiba mais sobre permissões e consentimento ([v1.0](../azuread-dev/v1-permissions-consent.md), [v2.0](v2-permissions-and-consent.md)).
