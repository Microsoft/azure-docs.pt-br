---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 0196d39f5b131bc54e00412beb7fdf10b7352336
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172105"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Propriedades de AuthenticationResult no MSAL.NET

Os métodos para adquirir tokens de retornam um `AuthenticationResult` (ou, para os métodos assíncronos, um `Task<AuthenticationResult>`.

No MSAL.NET, `AuthenticationResult` expõe:

- `AccessToken` para a API da Web para acessar os recursos. Esse parâmetro é uma cadeia de caracteres, geralmente codificado de base64 JWT, mas o cliente nunca deve ser dentro do token de acesso. Não há garantias que o formato permanecerá estável e ele pode ser criptografado para o recurso. A gravação de código que depende do conteúdo do token de acesso no cliente é uma das maiores origens de erros e quebras de lógica do cliente. Consulte também [tokens de acesso](../articles/active-directory/develop/access-tokens.md)
- `IdToken` para o usuário (esse parâmetro é um JWT codificada). Consulte [Tokens de ID](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` informa a data/hora quando o token expira
- `TenantId` contém o locatário no qual o usuário foi encontrado. Para usuários convidados (cenários de B2B do Azure AD), a ID do locatário é o locatário de convidado, o locatário não exclusivo.
Quando o token é entregue para um usuário, `AuthenticationResult` também contém informações sobre este usuário. Para fluxos de cliente confidencial em que os tokens sejam solicitados com nenhum usuário (para o aplicativo), essas informações de usuário são nulas.
- O `Scopes` para o qual o token foi emitido.
- A Id exclusiva para o usuário.

### <a name="iaccount"></a>IAccount

MSAL.NET define a noção de conta (por meio de `IAccount` interface). Essa alteração da falha fornece a semântica correta: o fato de que o mesmo usuário pode ter várias contas, em diferentes diretórios do Azure AD. Também MSAL.NET fornece melhores informações no caso de cenários de convidado, como informações de conta inicial são fornecidas.
O diagrama a seguir mostra a estrutura do `IAccount` interface:

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

O `AccountId` classe identifica uma conta em um locatário específico. Ele tem as seguintes propriedades:

| Propriedade | DESCRIÇÃO |
|----------|-------------|
| `TenantId` | Uma representação de cadeia de caracteres para um GUID, que é a ID do locatário onde reside a conta. |
| `ObjectId` | Uma representação de cadeia de caracteres para um GUID, que é a ID do usuário que é proprietária da conta no locatário. |
| `Identifier` | Identificador exclusivo para a conta. `Identifier` é a concatenação de `ObjectId` e `TenantId` separados por uma vírgula e são não codificado na base64. |

O `IAccount` interface representa informações sobre uma única conta. O mesmo usuário pode estar presente em locatários diferentes, ou seja, um usuário pode ter várias contas. Seus membros são:

| Propriedade | DESCRIÇÃO |
|----------|-------------|
| `Username` | Uma cadeia de caracteres que contém o valor que pode ser exibido no formato UPN (UserPrincipalName), por exemplo, john.doe@contoso.com. Essa cadeia de caracteres pode ser nula, enquanto o HomeAccountId e HomeAccountId.Identifier não serão nula. Essa propriedade substitui a `DisplayableId` propriedade de `IUser` nas versões anteriores do MSAL.NET. |
| `Environment` | Uma cadeia de caracteres que contém o provedor de identidade da conta, por exemplo, `login.microsoftonline.com`. Essa propriedade substitui a `IdentityProvider` propriedade de `IUser`, exceto que `IdentityProvider` também tinha informações sobre o locatário (além do ambiente de nuvem), enquanto que aqui o valor é somente o host. |
| `HomeAccountId` | AccountId da conta inicial do usuário. Essa propriedade identifica exclusivamente o usuário em locatários do AD do Azure. |

### <a name="using-the-token-to-call-a-protected-api"></a>Usando o token para chamar uma API protegida

Uma vez a `AuthenticationResult` tenha sido devolvido pela MSAL (em `result`), você precisará adicioná-lo para o cabeçalho de autorização HTTP, antes de fazer a chamada para acessar a API Web protegida.

```CSharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```