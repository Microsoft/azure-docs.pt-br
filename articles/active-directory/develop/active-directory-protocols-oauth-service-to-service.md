---
title: "Serviço do Azure AD para Autenticação de Serviço usando OAuth 2.0 | Microsoft Docs"
description: "Este artigo descreve como usar mensagens HTTP para implementar a autenticação de serviço para serviço usando o fluxo de concessão de credenciais de cliente OAuth 2.0."
services: active-directory
documentationcenter: .net
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: cc30a54cd56c0cb03a67f86e4552398baa764e58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# Serviço de chamadas de serviço usando as credenciais do cliente (segredo compartilhado ou certificados)
O Fluxo de Concessão de Credenciais do Cliente do OAuth 2.0 permite que um serviço Web (um *cliente confidencial*) use suas próprias credenciais, em vez de representar um usuário, para se autenticar ao chamar outro serviço Web. Nesse cenário, o cliente é geralmente um serviço Web de camada intermediária, um serviço daemon ou um Site. Para um nível mais alto de segurança, o AD do Azure também permite que o serviço de chamada usar um certificado (em vez de um segredo compartilhado) como uma credencial.

## Fluxo de concessão de credenciais do cliente
O diagrama a seguir explica o funcionamento do fluxo de concessão de credenciais do cliente no Azure AD (Azure Active Directory).

![Fluxo de concessão de credenciais de cliente do OAuth2.0](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. O aplicativo cliente se autentica no ponto de extremidade de emissão de token do Azure AD e solicita um token de acesso.
2. O ponto de extremidade de emissão de token do Azure AD emite o token de acesso.
3. O token de acesso é usado para a autenticação no recurso protegido.
4. Os dados do recurso protegido são retornados ao aplicativo Web.

## Registrar os serviços no Azure AD
Registre o serviço de chamada e o serviço de recebimento no Azure AD (Azure Active Directory). Para obter instruções detalhadas, consulte [Integrando aplicativos com o Azure Active Directory](active-directory-integrating-applications.md).

## Solicitar um token de acesso
Para solicitar um token de acesso, use um HTTP POST para o ponto de extremidade do Azure AD específico do locatário.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## Solicitação de token de acesso de serviço para serviço
Há dois casos, dependendo se o aplicativo cliente escolhe a ser protegida por um segredo compartilhado ou um certificado.

### Na primeira ocorrência: solicitação de token de acesso com um segredo compartilhado
Ao usar um segredo compartilhado, uma solicitação de token de acesso de serviço para serviço contém estes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório |Especifica o tipo de concessão solicitada. Em um fluxo de Concessão de Credenciais de Cliente, o valor deve ser **client_credentials**. |
| client_id |obrigatório |Especifica a ID de cliente do Azure AD do serviço Web de chamada. Para localizar a ID do cliente do aplicativo de chamada, no [portal do Azure](https://portal.azure.com), clique em  **Active Directory**, troque o diretório, clique no aplicativo. A client_id é a *ID do Aplicativo* |
| client_secret |obrigatório |Insira uma chave registrada para a chamada daemon ou o serviço de aplicativo web no AD do Azure. Para criar uma chave no portal do Azure, clique em **do Active Directory**, alternar o diretório, clique no aplicativo, clique em **configurações**, clique em **chaves**, e adicionar uma chave.|
| recurso |obrigatório |Insira o URI da ID de Aplicativo do serviço Web de recebimento. Para localizar o URI de ID do aplicativo, no portal do Azure, clique em **do Active Directory**, alternar o diretório, clique no aplicativo de serviço e, em seguida, clique em **configurações** e **propriedades** |

#### Exemplo
O HTTP POST a seguir solicita um token de acesso para o serviço Web https://service.contoso.com/. O `client_id` identifica o serviço Web que solicita o token de acesso.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### Segundo caso: solicitação de token de acesso com um certificado
Uma solicitação de token de acesso de serviço para serviço com certificado contém estes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório |Especifica o tipo de resposta solicitada. Em um fluxo de Concessão de Credenciais de Cliente, o valor deve ser **client_credentials**. |
| client_id |obrigatório |Especifica a ID de cliente do Azure AD do serviço Web de chamada. Para localizar a ID do cliente do aplicativo de chamada, no [portal do Azure](https://portal.azure.com), clique em  **Active Directory**, troque o diretório, clique no aplicativo. A client_id é a *ID do Aplicativo* |
| client_assertion_type |obrigatório |O valor deve ser `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |obrigatório | Uma asserção (um Token Web JSON) que você precisa para criar e assinar com o certificado registrado como credenciais do seu aplicativo. Leia mais sobre [credenciais de certificado](active-directory-certificate-credentials.md) para saber como registrar seu certificado e saber sobre o formato da asserção.|
| recurso | obrigatório |Insira o URI da ID de Aplicativo do serviço Web de recebimento. Para localizar o URI da ID do Aplicativo, no portal do Azure, clique em **Active Directory**, clique no diretório, clique no aplicativo e em **Configurar**. |

Observe que os parâmetros são praticamente os mesmos como no caso da solicitação pelo segredo compartilhado, exceto pelo fato de o parâmetro client_secret ser substituído por dois parâmetros: um client_assertion_type e uma client_assertion.

#### Exemplo
O HTTP POST a seguir solicita um token de acesso para o serviço Web https://service.contoso.com/ com um certificado. O `client_id` identifica o serviço Web que solicita o token de acesso.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### Resposta de token de acesso de serviço para serviço

Uma resposta bem-sucedida contém uma resposta JSON do OAuth 2.0 com os seguintes parâmetros:

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token de acesso solicitado. O serviço Web de chamada pode usar esse token para se autenticar no serviço Web de recebimento. |
| token_type |Indica o valor do tipo de token. O único tipo com suporte do Azure AD é **Portador**. Para saber mais sobre os tokens de portador, confira [Estrutura de autorização do OAuth 2.0: uso do token de portador (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Por quanto tempo o token de acesso é válido (em segundos). |
| expires_on |A hora de expiração do token de acesso. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. Esse valor é usado para determinar o tempo de vida de tokens em cache. |
| not_before |A hora na qual o token de acesso se torna útil. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de validade do token.|
| recurso |O URI da ID de aplicativo do serviço Web de recebimento. |

#### Exemplo de resposta
O exemplo a seguir mostra uma resposta bem-sucedida a uma solicitação de um token de acesso para um serviço Web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## Consulte também
* [OAuth 2.0 no Azure AD](active-directory-protocols-oauth-code.md)
* [Exemplo em C# da chamada de serviços com um segredo compartilhado](https://github.com/Azure-Samples/active-directory-dotnet-daemon) e [Exemplo em C# da chamada de serviços com um certificado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
