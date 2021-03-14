---
title: Tokens de ID da plataforma Microsoft Identity | Azure
titleSuffix: Microsoft identity platform
description: Saiba como usar id_tokens emitidos pelos pontos de extremidade do Azure AD v 1.0 e da plataforma Microsoft Identity (v 2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 21e0b800e06b7a5ad0351ff53d26a5e2cd2ba71c
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175381"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Tokens de ID da plataforma Microsoft Identity

`id_tokens` são enviados ao aplicativo cliente como parte de um fluxo do [OpenID Connect](v2-protocols-oidc.md) (OIDC). Eles podem ser enviados junto ou em vez de um token de acesso e são usados pelo cliente para autenticar o usuário.

## <a name="using-the-id_token"></a>Usando o id_token

Os tokens de ID devem ser usados para validar que um usuário é quem alega ser e obter informações úteis adicionais sobre eles – ele não deve ser usado para autorização no lugar de um [token de acesso](access-tokens.md). As declarações fornecidas por ele podem ser usadas para UX dentro de seu aplicativo, como [chaves em um banco de dados](#using-claims-to-reliably-identify-a-user-subject-and-object-id)e para fornecer acesso ao aplicativo cliente.  

## <a name="claims-in-an-id_token"></a>Declarações em um id_token

`id_tokens` são [JWTs](https://tools.ietf.org/html/rfc7519) (tokens Web JSON), o que significa que consistem em um cabeçalho, uma carga e uma parte da assinatura. Você pode usar o cabeçalho e a assinatura para verificar a autenticidade do token, enquanto o conteúdo tem informações sobre o usuário solicitado pelo cliente. Exceto quando indicado, todas as declarações JWT listadas aqui aparecem nos tokens v 1.0 e v 2.0.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Exiba esse token de exemplo de v1.0 em [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Exiba esse token de exemplo de v2.0 em [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Declarações de cabeçalho

|Declaração | Formatar | Descrição |
|-----|--------|-------------|
|`typ` | Cadeia de caracteres – sempre "JWT" | Indica que o token é um token JWT.|
|`alg` | String | Indica o algoritmo que foi usado para assinar o token. Exemplo: "RS256" |
|`kid` | String | Impressão digital para a chave pública usada para verificar esse token. Emitido em `id_tokens` v1.0 e v2.0. |
|`x5t` | String | O mesmo (em uso e valor) que `kid`. No entanto, essa é uma declaração herdada emitida somente em `id_tokens` v1.0 para fins de compatibilidade. |

### <a name="payload-claims"></a>Declarações de conteúdo

Essa lista mostra as declarações JWT que estão na maioria id_tokens por padrão (exceto quando indicado).  No entanto, seu aplicativo pode usar [declarações opcionais](active-directory-optional-claims.md) para solicitar declarações JWT adicionais no id_token.  Elas podem variar da `groups` declaração para informações sobre o nome do usuário.

|Declaração | Formatar | Descrição |
|-----|--------|-------------|
|`aud` |  Cadeia de caracteres, um URI da ID do Aplicativo | Identifica o destinatário pretendido do token. Em `id_tokens`, o público-alvo é a ID de Aplicativo de seu aplicativo, atribuída a ele no portal do Azure. O aplicativo deve validar esse valor e rejeitar o token, caso o valor não seja correspondente. |
|`iss` |  Cadeia de caracteres, um URI STS | Identifica o STS (Serviço de Token de Segurança) que constrói e retorna o token e o locatário do Azure AD no qual o usuário foi autenticado. Se o token tiver sido emitido pelo ponto de extremidade v2.0, o URI terminará com `/v2.0`.  O GUID que indica que o usuário é um consumidor da conta da Microsoft é `9188040d-6c67-4c5b-b112-36a304b66dad`. O aplicativo deve usar a parte do GUID da declaração para restringir o conjunto de locatários que podem entrar no aplicativo, se aplicável. |
|`iat` |  int, um carimbo de data/hora UNIX | "Emitido em" indica quando ocorreu a autenticação desse token.  |
|`idp`|Cadeia de caracteres, normalmente um URI de STS | Registra o provedor de identidade que autenticou a entidade do token. Esse valor é idêntico ao valor da declaração Emissor, a menos que a conta de usuário não esteja no mesmo locatário que o emissor – convidados, por exemplo. Se a declaração não estiver presente, isso significará que o valor de `iss` poderá ser usado.  Para as contas pessoais usadas em um contexto organizacional (por exemplo, uma conta pessoal convidada para um locatário do Azure AD), a declaração `idp`pode ser 'live.com' ou um URI de STS que contém o locatário da conta Microsoft`9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, um carimbo de data/hora UNIX | A declaração "nbf" (não antes) identifica a hora antes da qual o JWT NÃO DEVE ser aceito para processamento.|
|`exp` |  int, um carimbo de data/hora UNIX | A declaração "exp" (hora de expiração) identifica a hora de expiração ou a hora após ela na qual o JWT NÃO DEVE ser aceito para processamento.  É importante observar que um recurso pode rejeitar o token antes dessa hora também, se, por exemplo, uma alteração na autenticação for necessária ou se uma revogação de token tiver sido detectada. |
| `c_hash`| String |O hash de código é incluído em tokens de ID apenas quando eles são emitidos com um código de autorização OAuth 2.0. Ele pode ser usado para validar a autenticidade de um código de autorização. Para obter detalhes sobre como realizar essa validação, confira a [Especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| String |O hash de token de acesso é incluído em tokens de ID somente quando o token de ID é emitido do `/authorize` ponto de extremidade com um token de acesso OAuth 2,0. Ele pode ser usado para validar a autenticidade de um token de acesso. Para obter detalhes sobre como realizar essa validação, confira a [Especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken). Isso não é retornado nos tokens de ID do `/token` ponto de extremidade. |
|`aio` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure AD para registrar os dados para reutilização de token. Deve ser ignorado.|
|`preferred_username` | String | O nome de usuário principal que representa o usuário. Ele pode ser um endereço de email, número de telefone ou nome de usuário genérico sem um formato especificado. Seu valor é mutável e pode ser alterado ao longo do tempo. Uma vez que é mutável, esse valor não deve ser usado para tomar decisões de autorização. O `profile` escopo é necessário para receber essa declaração.|
|`email` | String | O `email` declaração está presente por padrão para contas de convidados que possuem um endereço de email.  O aplicativo pode solicitar a declaração de email para usuários gerenciados (aquelas do mesmo locatário do recurso) usando o `email` [declaração opcional](active-directory-optional-claims.md).  No ponto de extremidade v 2.0, seu aplicativo também pode solicitar o `email` escopo da OpenID Connect - você não precisa solicitar a declaração opcional e o escopo para obter a declaração.  A declaração de email só dá suporte a emails endereçável de informações de perfil do usuário. |
|`name` | String | A declaração `name` fornece um valor legível por humanos que identifica o assunto do token. O valor não é garantido como exclusivo, é mutável e foi projetado para ser usado somente para fins de exibição. O `profile` escopo é necessário para receber essa declaração. |
|`nonce`| String | O nonce corresponde ao parâmetro incluído na solicitação original /authorize para o IDP. Se esses itens não corresponderem, seu aplicativo deverá rejeitar o token. |
|`oid` | Cadeia de caracteres, um GUID | O identificador imutável de um objeto do sistema de identidade da Microsoft, nesse caso, uma conta de usuário. Essa ID identifica exclusivamente o usuário entre os aplicativos - dois aplicativos diferentes autenticando o mesmo usuário receberão o mesmo valor na declaração `oid`. O Microsoft Graph retornará essa ID como a propriedade `id` para uma determinada conta de usuário. Como o `oid` permite que vários aplicativos correlacionem os usuários, o `profile` escopo é necessário para receber essa declaração. Observe que, se um único usuário existir em vários locatários, o usuário conterá uma ID de objeto diferente em cada locatário. eles são considerados contas diferentes, mesmo que o usuário faça logon em cada conta com as mesmas credenciais. A `oid` declaração é um GUID e não pode ser reutilizada. |
|`roles`| Matriz de cadeia de caracteres | O conjunto de funções que foram atribuídas ao usuário que está fazendo logon. |
|`rh` | Cadeia de caracteres opaca |Uma declaração interna usada pelo Azure para revalidar tokens. Deve ser ignorado. |
|`sub` | String | O item mais importante sobre o qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído nem reutilizado. O assunto é um identificador de paridade e é exclusivo a uma ID de aplicativo específica. Se um único usuário entrar em dois aplicativos diferentes usando duas IDs de cliente diferentes, esses aplicativos receberão dois valores diferentes para a declaração de assunto. Isso pode ou não ser desejado dependendo dos requisitos de arquitetura e privacidade. |
|`tid` | Cadeia de caracteres, um GUID | Um GUID que representa o locatário do Azure AD do qual o usuário é proveniente. Para contas corporativas e de estudante, o GUID é a ID de locatário imutável da organização à qual o usuário pertence. Para contas pessoais, o valor é `9188040d-6c67-4c5b-b112-36a304b66dad`. O `profile` escopo é necessário para receber essa declaração. |
|`unique_name` | String | Fornece um valor legível que identifica a entidade do token. Esse valor é exclusivo em qualquer momento determinado, mas como emails e outros identificadores podem ser reutilizados, esse valor pode reaparecer em outras contas e, portanto, deve ser usado somente para fins de exibição. Emitido somente no `id_tokens` v1.0. |
|`uti` | Cadeia de caracteres opaca | Uma declaração interna usada pelo Azure para revalidar tokens. Deve ser ignorado. |
|`ver` | Cadeia de caracteres, 1.0 ou 2.0 | Indica a versão do id_token. |
|`hasgroups`|Boolean|Se estiver presente, sempre true, denotando que o usuário está em pelo menos um grupo. Usado no lugar da declaração de grupos para JWTs em fluxos de concessão implícitos se a declaração de grupos completos estender o fragmento de URI além dos limites de comprimento da URL (atualmente, 6 ou mais grupos). Indica que o cliente deve usar a API do Microsoft Graph para determinar os grupos do usuário (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`).|
|`groups:src1`|Objeto JSON | Para solicitações de token sem limite de tamanho (consulte `hasgroups` acima), mas ainda muito grandes para o token, será incluído um link para a lista completa de grupos do usuário. Para JWTs na forma de declaração distribuída, para SAML como uma nova declaração no lugar da declaração `groups`. <br><br>**Valor de exemplo de JWT**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> Para obter mais informações, consulte [grupos excedentes de declaração](#groups-overage-claim).|

> [!NOTE]
> Os id_token v 1.0 e v 2.0 têm diferenças na quantidade de informações que serão executadas, conforme mostrado nos exemplos acima. A versão é baseada no ponto de extremidade de onde ele foi solicitado. Embora os aplicativos existentes provavelmente usem o ponto de extremidade do Azure AD, os novos aplicativos devem usar a "plataforma de identidade da Microsoft".
>
> - v 1.0: pontos de extremidade do Azure AD: `https://login.microsoftonline.com/common/oauth2/authorize`
> - v 2.0: pontos de extremidade da plataforma Microsoft Identity: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Usando declarações para identificar de forma confiável um usuário (entidade e ID de objeto)

Ao identificar um usuário (digamos, procurá-los em um banco de dados ou decidir quais permissões eles têm), é essencial usar as informações que permanecerão constantes e exclusivas ao longo do tempo. Os aplicativos herdados às vezes usam campos como o endereço de email, um número de telefone ou o UPN.  Todos eles podem mudar ao longo do tempo e também podem ser reutilizados com o passar do tempo, quando um funcionário altera seu nome, ou um funcionário recebe um endereço de email que corresponde ao de um funcionário anterior, que não apresenta mais tempo). Portanto, é **essencial** que seu aplicativo não use dados legíveis para identificar um usuário que possa ser lido por pessoas geralmente significa que alguém irá lê-lo e deseja alterá-lo. Em vez disso, use as declarações fornecidas pelo padrão OIDC ou as declarações de extensão fornecidas pela Microsoft- `sub` as `oid` declarações e.

Para armazenar corretamente as informações por usuário, use `sub` ou `oid` sozinha (que são exclusivas como GUIDs), com `tid` uso para roteamento ou fragmentação, se necessário.  Se você precisar compartilhar dados entre serviços, `oid` + `tid` é melhor que todos os aplicativos obtenham o mesmo `oid` e as `tid` declarações para um determinado usuário.  A `sub` declaração na plataforma Microsoft Identity é "emparelhada" – ela é exclusiva com base em uma combinação do destinatário, do locatário e do usuário do token.  Assim, dois aplicativos que solicitam tokens de ID para um determinado usuário receberão `sub` declarações diferentes, mas as mesmas `oid` declarações para esse usuário.

>[!NOTE]
> Não use a `idp` declaração para armazenar informações sobre um usuário em uma tentativa de correlacionar usuários entre locatários.  Ele não funcionará, pois as `oid` `sub` declarações e para um usuário mudam entre locatários, por design, para garantir que os aplicativos não possam rastrear os usuários entre locatários.  
>
> Os cenários de convidado, onde um usuário é hospedado em um locatário e são autenticados em outro, devem tratar o usuário como se fossem um usuário totalmente novo para o serviço.  Seus documentos e privilégios no locatário da Contoso não devem ser aplicados no locatário da Fabrikam. Isso é importante para evitar vazamento acidental de dados entre locatários.

### <a name="groups-overage-claim"></a>Declaração de excedente de grupos
Para garantir que o tamanho do token não exceda os limites de tamanho do cabeçalho HTTP, o Azure AD limita o número de IDs de objeto que ele inclui na `groups` declaração. Se um usuário for membro de mais grupos do que o limite excedente (150 para tokens SAML, 200 para tokens JWT), o Azure AD não emitirá a declaração de grupos no token. Em vez disso, ele inclui uma declaração excedente no token que indica ao aplicativo consultar a API do Microsoft Graph para recuperar a associação de grupo do usuário.

```json
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

## <a name="validating-an-id_token"></a>Validação de um id_token

A validação de um `id_token` é semelhante à primeira etapa da [validação de um token de acesso](access-tokens.md#validating-tokens) -o cliente pode validar que o emissor correto enviou o token e que ele não foi adulterado. Como `id_tokens` o sempre é um token JWT, existem muitas bibliotecas para validar esses tokens – recomendamos que você use um deles em vez de fazer isso por conta própria.  Observe que somente clientes confidenciais (aqueles com um segredo) devem validar tokens de ID.  Aplicativos públicos (o código executado inteiramente em um dispositivo ou rede que você não controla, por exemplo, o navegador de um usuário ou sua rede doméstica) não se beneficiam da validação do token de ID, uma vez que um usuário mal-intencionado pode interceptar e editar as chaves usadas para validação do token.

Para validar o token manualmente, consulte os detalhes das etapas em [validar um token de acesso](access-tokens.md#validating-tokens). Depois de validar a assinatura no token, as seguintes declarações de JWT devem ser validadas na id_token (elas também podem ser feitas pela biblioteca de validação de token):

* Carimbos de data/hora: os carimbos de data/hora `iat`, `nbf`, e `exp` devem estar todos antes ou depois da hora atual, conforme apropriado.
* Público-alvo: a declaração `aud` deve corresponder à ID do aplicativo do seu aplicativo.
* Nonce: a declaração `nonce` no conteúdo deve corresponder ao parâmetro nonce passado para o ponto de extremidade /authorize durante a solicitação inicial.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [tokens de acesso](access-tokens.md)
* Personalize as declarações de JWT em seu id_token usando [declarações opcionais](active-directory-optional-claims.md).
