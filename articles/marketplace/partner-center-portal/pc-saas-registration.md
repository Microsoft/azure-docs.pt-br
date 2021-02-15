---
title: Registrar um aplicativo SaaS-Azure Marketplace
description: Saiba como usar o portal do Azure para registrar um aplicativo SaaS e receber um token de segurança de Azure Active Directory.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 39a0830806d2d9c7358d175cae703e9c81c45b02
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130010"
---
# <a name="register-a-saas-application"></a>Registrar um aplicativo SaaS

Este artigo explica como registrar um aplicativo SaaS usando o Microsoft [portal do Azure](https://portal.azure.com/) e como obter o token de acesso do Publicador (Azure Active Directory token de acesso). O Publicador usará esse token para autenticar o aplicativo SaaS chamando as APIs de preenchimento de SaaS.  As APIs de preenchimento usam as credenciais do cliente OAuth 2,0 para conceder fluxo em pontos de extremidade Azure Active Directory (v 1.0) para fazer uma solicitação de token de acesso de serviço a serviço.

O Azure Marketplace não impõe nenhuma restrição no método de autenticação que seu serviço SaaS usa para os usuários finais. O fluxo abaixo é necessário apenas para autenticar o serviço SaaS no Azure Marketplace.

Para obter mais informações sobre o Azure AD (Active Directory), consulte [o que é autenticação](../../active-directory/develop/authentication-vs-authorization.md)?

## <a name="register-an-azure-ad-secured-app"></a>Registrar um aplicativo protegido pelo Azure AD

Para que você possa usar os recursos do AD do Azure em qualquer aplicativo desejado, primeiramente é preciso registrá-lo em um locatário do AD do Azure. Esse processo de registro envolve fornecer ao Azure AD alguns detalhes sobre seu aplicativo. Para registrar um novo aplicativo usando o portal do Azure, realize as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Se sua conta fornecer acesso a mais de um, clique em sua conta no canto superior direito e defina a sessão do portal para o locatário do Azure AD desejado.
3. No painel de navegação esquerdo, clique no serviço **Azure Active Directory** , clique em **Registros do aplicativo** e clique em **Novo registro de aplicativo** .

    ![Registros de Aplicativo do AD SaaS](./media/saas-offer-app-registration-v1.png)

4. Na página Criar, insira as informações de registro do aplicativo:
    -   **Nome** : insira um nome significativo de aplicativo
    -   **Tipo de aplicativo** :  
        
        Selecione **aplicativo Web/API** para [aplicativos cliente](../../active-directory/develop/developer-glossary.md#client-application)) e [aplicativos de recurso/API](../../active-directory/develop/developer-glossary.md#resource-server)que estão instalados em um servidor seguro. Essa configuração é usada para [clientes Web](../../active-directory/develop/developer-glossary.md#web-client)confidenciais OAuth) e [clientes baseados em agente de usuário](../../active-directory/develop/developer-glossary.md#user-agent-based-client)público).
        O mesmo aplicativo também pode expor um cliente e o recurso/API.

        Para obter exemplos específicos de aplicativos Web, Confira as configurações guiadas de início rápido que estão disponíveis [na seção Introdução](../../active-directory/develop/quickstart-create-new-tenant.md) do [Guia de desenvolvedores do Azure ad](../../active-directory/develop/index.yml).

5. Quando terminar, clique em **registrar** .  O Azure AD atribui uma ID de *aplicativo* exclusiva ao novo aplicativo. É recomendável registrar um aplicativo que acesse a API somente e como um único locatário.

6. Para criar o segredo do cliente, navegue até **certificados & página segredos** e clique em **+ novo segredo do cliente** .  Certifique-se de copiar o valor secreto para usá-lo em seu código.

A **ID do aplicativo do Azure ad** está associada à sua ID do editor, portanto, certifique-se de que a mesma *ID do aplicativo* seja usada em todas as suas ofertas.

>[!Note]
>Se um Publicador tiver duas contas diferentes no Partner Center, duas IDs de aplicativo do Azure AD diferentes deverão ser usadas.  Cada conta de parceiro no Partner Center deve usar uma ID de aplicativo do Azure AD exclusiva para todas as ofertas de SaaS que são publicadas por meio dessa conta.

## <a name="how-to-get-the-publishers-authorization-token"></a>Como obter o token de autorização do editor

Depois de registrar seu aplicativo, você pode solicitar programaticamente o token de autorização do editor (token de acesso do AD do Azure, usando o ponto de extremidade do Azure AD v1). O Publicador deve usar esse token ao chamar as várias APIs de preenchimento de SaaS. Esse token só é válido por uma hora. 

Para obter mais informações sobre esses tokens, veja [Tokens de acesso do Azure Active Directory](../../active-directory/develop/access-tokens.md).  Observe que, no fluxo, abaixo do token do ponto de extremidade v1 é usado.

### <a name="get-the-token-with-an-http-post"></a>Obter o token com um HTTP POST

#### <a name="http-method"></a>Método HTTP

Postar<br>

##### <a name="request-url"></a>*URL de Solicitação* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*Parâmetro URI*

|  Nome do parâmetro    |  Obrigatório         |  Descrição |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  True      |  ID do locatário do aplicativo do AAD registrado. |

##### <a name="request-header"></a>*Cabeçalho da solicitação*

|  Nome do cabeçalho       |  Obrigatório         |  Descrição |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  True      |  Tipo de conteúdo associado à solicitação. O valor padrão é `application/x-www-form-urlencoded`. |

##### <a name="request-body"></a>*Corpo da solicitação*

|  Nome da propriedade     |  Obrigatório         |  Descrição |
|  ---------------   |  ---------------  | ------------ |
|  `grant_type`      |  True      |  Tipo de concessão. Use `"client_credentials"`. |
|  `client_id`       |  True      |  Identificador do cliente/aplicativo associado ao Aplicativo Azure AD. |
|  `client_secret`   |  True      |  Segredo associado ao aplicativo do Azure AD. |
|  `resource`        |  True      |  Recurso de destino para o qual o token é solicitado. Use `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` porque a API SaaS do Marketplace sempre é o recurso de destino nesse caso. |

##### <a name="response"></a>*Resposta*

|  Nome     |  Tipo         |  Descrição |
|  ------   |  ---------------  | ------------ |
|  200 OK   |  TokenResponse    |  Solicitação bem-sucedida. |

##### <a name="tokenresponse"></a>*TokenResponse*

Exemplo de resposta:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

O `"access_token"` valor do campo na resposta é o `<access_token>` que você passará como parâmetro de autorização ao chamar todas as APIs de medição de SaaS e do Marketplace.

## <a name="next-steps"></a>Próximas etapas

Seu aplicativo protegido pelo Azure AD agora pode usar a [API de preenchimento de SaaS versão 2](./pc-saas-fulfillment-api-v2.md).