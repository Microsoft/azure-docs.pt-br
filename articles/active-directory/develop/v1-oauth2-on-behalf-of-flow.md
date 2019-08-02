---
title: Especificação de rascunho em nome de do OAuth 2.0 de autenticação serviço a serviço do Azure AD | Microsoft Docs
description: Este artigo descreve como usar mensagens HTTP para implementar a autenticação de serviço a serviço usando o fluxo On-Behalf-Of do OAuth2.0.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7381a0dfb8f780900d8c2c8ba0637dcd232bdb9f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380898"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Chamadas de serviço a serviço que usam a identidade do usuário delegado no fluxo On-Behalf-Of

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

O fluxo OBO (On-Behalf-Of) do OAuth 2.0 permite que um aplicativo que invoca um serviço ou API Web passe a autenticação do usuário para outro serviço ou API Web. O fluxo OBO propaga as permissões e a identidade do usuário delegado por meio da cadeia de solicitações. Para o serviço de camada intermediária fazer solicitações autenticadas para o serviço downstream, ele precisa obter um token de acesso do Azure AD (Azure Active Directory) em nome do usuário.

> [!IMPORTANT]
> Desde maio de 2018, não é possível usar um `id_token` no fluxo On-Behalf-Of.  SPAs (aplicativos de página única) devem passar um token de acesso para um cliente confidencial de camada intermediária para executar fluxos OBO. Para obter mais detalhes sobre quais clientes podem realizar chamadas On-Behalf-Of, confira as [limitações](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>Diagrama do fluxo em nome de

O fluxo OBO é iniciado após o usuário ter sido autenticado em um aplicativo que usa o [fluxo de concessão de código de autorização OAuth 2.0](v1-protocols-oauth-code.md). Nesse ponto, o aplicativo envia um token de acesso (token A) para a API Web da camada intermediária (API A) que contém as declarações do usuário e o consentimento para acessar a API A. Em seguida, a API A faz uma solicitação autenticada para a API Web downstream (API B).

Essas etapas compõem o fluxo On-Behalf-Of: ![Mostra as etapas no fluxo em nome de do OAuth 2.0](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. O aplicativo cliente faz uma solicitação para API A com o token A.
1. A API A se autentica no ponto de extremidade de emissão de token do Azure AD e solicita um token para acessar a API B.
1. O ponto de extremidade de emissão de token do Azure AD valida as credenciais da API com o token A e emite o token de acesso para a API B (token B).
1. A solicitação para a API B contém o token B no cabeçalho de autorização.
1. A API B retorna dados do recurso protegido.

>[!NOTE]
>A declaração de público-alvo de um token de acesso usado para solicitar um token para um serviço downstream deve ser a ID do serviço que está fazendo a solicitação OBO. O token também deve ser assinado com a chave de assinatura global do Azure Active Directory (que é o padrão para aplicativos registrados por meio do portal de **Registros de aplicativo**).

## <a name="register-the-application-and-service-in-azure-ad"></a>Registrar o aplicativo e o serviço no Azure AD

Registre o aplicativo de camada intermediária e o aplicativo cliente no Azure AD.

### <a name="register-the-middle-tier-service"></a>Registrar o serviço de camada intermediária

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Na barra superior, selecione sua conta e examine a lista **Diretório** para selecionar um locatário do Active Directory para seu aplicativo.
1. Selecione **Mais Serviços** no painel esquerdo e escolha **Azure Active Directory**.
1. Selecione **registros de aplicativo** e, em seguida, **novo registro**.
1. Insira um nome amigável para o aplicativo e selecione o tipo de aplicativo.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
1. Defina o URI de redirecionamento para a URL base.
1. Selecione **Registrar** para criar o aplicativo.
1. Gere um segredo do cliente antes de sair do portal do Azure.
1. Na portal do Azure, escolha seu aplicativo e selecione **certificados & segredos**.
1. Selecione **novo segredo do cliente** e adicione um segredo com uma duração de um ano ou dois anos.
1. Quando você salvar essa página, a portal do Azure exibirá o valor secreto. Copie e salve o valor secreto em um local seguro.

> [!IMPORTANT]
> Você precisa do segredo para definir as configurações do aplicativo em sua implementação. Esse valor secreto não é exibido novamente e não é recuperável por nenhum outro meio. Registre-o assim que ele ficar visível no portal do Azure.

### <a name="register-the-client-application"></a>Registrar o aplicativo cliente

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Na barra superior, selecione sua conta e examine a lista **Diretório** para selecionar um locatário do Active Directory para seu aplicativo.
1. Selecione **Mais Serviços** no painel esquerdo e escolha **Azure Active Directory**.
1. Selecione **registros de aplicativo** e, em seguida, **novo registro**.
1. Insira um nome amigável para o aplicativo e selecione o tipo de aplicativo.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
1. Defina o URI de redirecionamento para a URL base.
1. Selecione **Registrar** para criar o aplicativo.
1. Configurar permissões para seu aplicativo. Em **permissões de API**, selecione **Adicionar uma permissão** e, em seguida, **minhas APIs**.
1. Digite o nome do serviço de camada intermediária no campo de texto.
1. Escolha **selecionar permissões** e, em seguida, selecione **nome do serviço de \<acesso >** .

### <a name="configure-known-client-applications"></a>Configurar aplicativos cliente conhecidos

Nesse cenário, o serviço de camada intermediária precisa obter o consentimento do usuário para acessar a API downstream sem interação do usuário. A opção de permitir acesso à API downstream deve ser apresentada antecipadamente como parte da etapa de consentimento durante a autenticação.

Siga as etapas abaixo para associar explicitamente o registro do aplicativo cliente no Azure AD ao registro do serviço de camada intermediária. Esta operação mescla o consentimento exigido pelo cliente e pela camada intermediária em uma única caixa de diálogo.

1. Vá até o registro do serviço de camada intermediária e selecione **Manifesto** para abrir o editor de manifesto.
1. Localize a propriedade de matriz `knownClientApplications` e, em seguida, adicione a ID do Cliente do aplicativo cliente como um elemento.
1. Salve o manifesto selecionando **Salvar**.

## <a name="service-to-service-access-token-request"></a>Solicitação de token de acesso de serviço para serviço

Para solicitar um token de acesso, use um HTTP POST para o ponto de extremidade do Azure AD específico do locatário com os parâmetros a seguir:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

O aplicativo cliente é protegido por um segredo compartilhado ou por um certificado.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: solicitação de token de acesso com um segredo compartilhado

Ao usar um segredo compartilhado, uma solicitação de token de acesso de serviço para serviço contém estes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório | O tipo da solicitação de token. Uma solicitação OBO usa um JWT (Token Web JSON), de modo que o valor deve ser **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| assertion |obrigatório | O valor do token de acesso usado na solicitação. |
| client_id |obrigatório | A ID do aplicativo atribuída ao serviço de chamada durante o registro com o Azure AD. Para localizar a ID do aplicativo no portal do Azure, selecione **Active Directory**, escolha o diretório e selecione o nome do aplicativo. |
| client_secret |obrigatório | A chave registrada para o serviço de chamada no Azure AD. Esse valor deve ter sido observado no momento do registro. |
| resource |obrigatório | O URI da ID do aplicativo do serviço de recebimento (recurso protegido). Para localizar o URI da ID do aplicativo no portal do Azure, selecione **Active Directory** e escolha o diretório. Selecione o nome do aplicativo, escolha **Todas as configurações** e, em seguida, selecione **Propriedades**. |
| requested_token_use |obrigatório | Especifica como a solicitação deve ser processada. No fluxo em nome de, o valor deve ser **on_behalf_of**. |
| scope |obrigatório | Lista de escopos separados por espaço para a solicitação de token. Para OpenID Connect, a **openid** do escopo deve ser especificada.|

#### <a name="example"></a>Exemplo

O HTTP POST a seguir solicita um token de acesso para a API Web https://graph.windows.net. O `client_id` identifica o serviço que solicita o token de acesso.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: solicitação de token de acesso com um certificado

Uma solicitação de token de acesso de serviço para serviço com certificado contém estes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório | O tipo da solicitação de token. Uma solicitação OBO usa um token de acesso JWT, de modo que o valor deve ser **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| assertion |obrigatório | O valor do token usado na solicitação. |
| client_id |obrigatório | A ID do aplicativo atribuída ao serviço de chamada durante o registro com o Azure AD. Para localizar a ID do aplicativo no portal do Azure, selecione **Active Directory**, escolha o diretório e selecione o nome do aplicativo. |
| client_assertion_type |obrigatório |O valor deve ser `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |obrigatório | Um Token Web JSON que você cria e assina com o certificado registrado como credenciais de seu aplicativo. Confira [credenciais de certificado](active-directory-certificate-credentials.md) para saber mais sobre o formato da declaração e sobre como registrar seu certificado.|
| resource |obrigatório | O URI da ID do aplicativo do serviço de recebimento (recurso protegido). Para localizar o URI da ID do aplicativo no portal do Azure, selecione **Active Directory** e escolha o diretório. Selecione o nome do aplicativo, escolha **Todas as configurações** e, em seguida, selecione **Propriedades**. |
| requested_token_use |obrigatório | Especifica como a solicitação deve ser processada. No fluxo em nome de, o valor deve ser **on_behalf_of**. |
| scope |obrigatório | Lista de escopos separados por espaço para a solicitação de token. Para OpenID Connect, a **openid** do escopo deve ser especificada.|

Esses parâmetros são quase iguais aos da solicitação do segredo compartilhado, exceto pelo fato de que `client_secret parameter` é substituído por dois parâmetros: `client_assertion_type` e `client_assertion`.

#### <a name="example"></a>Exemplo

O HTTP POST a seguir solicita um token de acesso para a API Web https://graph.windows.net com um certificado. O `client_id` identifica o serviço que solicita o token de acesso.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Resposta do token de acesso de serviço a serviço

Uma resposta bem-sucedida é uma resposta JSON do OAuth 2.0 com os parâmetros a seguir:

| Parâmetro | Descrição |
| --- | --- |
| token_type |Indica o valor do tipo de token. O único tipo com suporte do Azure AD é **Portador**. Para saber mais sobre os tokens de portador, confira [Estrutura de Autorização do OAuth 2.0: Uso do Token de Portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |O escopo do acesso concedido no token. |
| expires_in |O período de tempo pelo qual o token de acesso é válido (em segundos). |
| expires_on |A hora de expiração do token de acesso. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. Esse valor é usado para determinar o tempo de vida de tokens em cache. |
| resource |O URI da ID do aplicativo do serviço de recebimento (recurso protegido). |
| access_token |O token de acesso solicitado. O serviço de chamada pode usar esse token para se autenticar no serviço de recebimento. |
| id_token |O token de ID solicitado. O serviço de chamada pode usar esse token para verificar a identidade do usuário e iniciar uma sessão com o usuário. |
| refresh_token |O token de atualização para o token de acesso solicitado. O serviço de chamada poderá usar esse token para solicitar outro token de acesso depois que o token de acesso atual expirar. |

### <a name="success-response-example"></a>Exemplo de resposta de êxito

O exemplo a seguir mostra uma resposta bem-sucedida a uma solicitação para um token de acesso para a API Web https://graph.windows.net.

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Exemplo de resposta de erro

O ponto de extremidade de token do Azure AD retorna uma resposta de erro quando tenta adquirir um token de acesso para uma API downstream que é definida com uma política de acesso condicional (por exemplo, autenticação multifator). O serviço de camada intermediária deve enfileirar esse erro para o aplicativo cliente para que o aplicativo cliente possa fornecer a interação do usuário para atender à política de acesso condicional.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Usar o token de acesso para acessar o recurso protegido

O serviço de camada intermediária pode usar o token de acesso obtido para fazer solicitações autenticadas para a API Web downstream definindo o token no cabeçalho `Authorization`.

### <a name="example"></a>Exemplo

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Declarações SAML obtidas com um fluxo OBO do OAuth 2.0

Alguns serviços Web baseados em OAuth precisam acessar outras APIs de serviços Web que aceitam declarações SAML em fluxos não interativos. O Azure Active Directory pode fornecer uma declaração SAML em resposta a um fluxo On-Behalf-Of que usa um serviço Web baseado em SAML como recurso de destino.

>[!NOTE]
>Essa é uma extensão não padrão para o fluxo On-Behalf-Of do OAuth 2.0 que permite que um aplicativo baseado em OAuth2 acesse pontos de extremidade da API do serviço Web que consomem tokens SAML.

> [!TIP]
> Quando chama um serviço Web protegido por SAML de um aplicativo Web de front-end, você pode simplesmente chamar a API e iniciar um fluxo de autenticação interativa normal, com a sessão existente do usuário. Você só precisa usar um fluxo OBO quando uma chamada de serviço a serviço exigir um token SAML para fornecer o contexto de usuário.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Obter um token SAML usando uma solicitação OBO com um segredo compartilhado

Uma solicitação de serviço a serviço para obter uma declaração SAML contém os seguintes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório | O tipo da solicitação de token. Para uma solicitação que usa um JWT, o valor deve ser **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| assertion |obrigatório | O valor do token de acesso usado na solicitação.|
| client_id |obrigatório | A ID do aplicativo atribuída ao serviço de chamada durante o registro com o Azure AD. Para localizar a ID do aplicativo no portal do Azure, selecione **Active Directory**, escolha o diretório e selecione o nome do aplicativo. |
| client_secret |obrigatório | A chave registrada para o serviço de chamada no Azure AD. Esse valor deve ter sido observado no momento do registro. |
| resource |obrigatório | O URI da ID do aplicativo do serviço de recebimento (recurso protegido). Esse é o recurso que será a Audiência do token SAML. Para localizar o URI da ID do aplicativo no portal do Azure, selecione **Active Directory** e escolha o diretório. Selecione o nome do aplicativo, escolha **Todas as configurações** e, em seguida, selecione **Propriedades**. |
| requested_token_use |obrigatório | Especifica como a solicitação deve ser processada. No fluxo em nome de, o valor deve ser **on_behalf_of**. |
| requested_token_type | obrigatório | Especifica o tipo de token solicitado. O valor pode ser **urn:ietf:params:oauth:token-type:saml2** ou **urn:ietf:params:oauth:token-type:saml1**, dependendo dos requisitos do recurso acessado. |

A resposta contém um token SAML codificado em Base64url e UTF8.

- **SubjectConfirmationData para uma declaração SAML é originado de uma chamada OBO**: se o aplicativo de destino exigir um valor de destinatário em **SubjectConfirmationData**, o valor deverá ser uma URL de Resposta sem curinga na configuração do aplicativo de recurso.
- **O nó SubjectConfirmationData**: o nó não pode conter um atributo **InResponseTo**, pois não faz parte de uma resposta SAML. O aplicativo que recebe o token SAML precisa ser capaz de aceitar a declaração SAML sem um atributo **InResponseTo**.

- **Consent**: o consentimento deve ser concedido para receber um token SAML contendo dados de usuário em um fluxo OAuth. Para obter informações sobre permissões e sobre como obter consentimento do administrador, confira [Permissões e consentimento no ponto de extremidade v1.0 do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent).

### <a name="response-with-saml-assertion"></a>Resposta com declaração SAML

| Parâmetro | Descrição |
| --- | --- |
| token_type |Indica o valor do tipo de token. O único tipo com suporte do Azure AD é **Portador**. Para saber mais sobre os tokens de portador, confira [Estrutura de Autorização do OAuth 2.0: Uso do Token de Portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |O escopo do acesso concedido no token. |
| expires_in |O período de tempo pelo qual o token de acesso é válido (em segundos). |
| expires_on |A hora de expiração do token de acesso. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. Esse valor é usado para determinar o tempo de vida de tokens em cache. |
| resource |O URI da ID do aplicativo do serviço de recebimento (recurso protegido). |
| access_token |O parâmetro que retorna a declaração SAML. |
| refresh_token |O token de atualização. O serviço de chamada poderá usar esse token para solicitar outro token de acesso depois que a declaração SAML atual expirar. |

- token_type: Portador
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- resource: `https://api.contoso.com`
- access_token: \<Declaração SAML\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Atualizar token\>

## <a name="client-limitations"></a>Limitações do cliente

Clientes públicos com URLs de resposta curinga não podem usar um `id_token` para fluxos OBO. No entanto, um cliente confidencial ainda poderá resgatar tokens de **acesso** obtidos por meio do fluxo de concessão implícita mesmo se o cliente público tiver um URI de redirecionamento curinga registrado.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o protocolo OAuth 2.0 e outra maneira de executar autenticação de serviço a serviço usando as credenciais do cliente:

* [Autenticação de serviço a serviço usando a concessão de credenciais de cliente OAuth 2.0 no Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 no Azure AD](v1-protocols-oauth-code.md)
