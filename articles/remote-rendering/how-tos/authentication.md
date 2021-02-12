---
title: Autenticação
description: Explica como funciona a autenticação
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 35fd78a9d55dc684045fdb4b83691c1613801421
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724860"
---
# <a name="configure-authentication"></a>Configurar autenticação

A renderização remota do Azure usa o mesmo mecanismo de autenticação que o [asa (âncoras espaciais) do Azure](../../spatial-anchors/concepts/authentication.md?tabs=csharp). Os clientes precisam definir *uma* das seguintes opções para chamar as APIs REST com êxito:

* **AccountKey**: pode ser obtido na guia "Keys" para a conta de renderização remota no portal do Azure. As chaves de conta são recomendadas apenas para desenvolvimento/protótipo.
    ![ID da Conta](./media/azure-account-primary-key.png)

* **AccountDomain**: pode ser obtido na guia "visão geral" para a conta de renderização remota no portal do Azure.
    ![Domínio da conta](./media/azure-account-domain.png)

* **AuthenticationToken**: é um token do Azure AD, que pode ser obtido usando a [biblioteca MSAL](../../active-directory/develop/msal-overview.md). Há vários fluxos diferentes disponíveis para aceitar credenciais de usuário e usar essas credenciais para obter um token de acesso.

* **MRAccessToken**: é um token Mr, que pode ser obtido do serviço de token de segurança (STS) do Azure Mixed Reality. Recuperado do `https://sts.<accountDomain>` ponto de extremidade usando uma chamada REST semelhante à seguinte:

    ```rest
    GET https://sts.southcentralus.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.southcentralus.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    Onde o cabeçalho de autorização é formatado da seguinte maneira: `Bearer <Azure_AD_token>` ou `Bearer <accoundId>:<accountKey>` . O primeiro é preferível à segurança. O token retornado por essa chamada REST é o token de acesso MR.

## <a name="authentication-for-deployed-applications"></a>Autenticação para aplicativos implantados

As chaves de conta são recomendadas para criação rápida de protótipos, somente durante o desenvolvimento. É recomendável não enviar seu aplicativo para produção usando uma chave de conta inserida nele. A abordagem recomendada é usar uma abordagem de autenticação do Azure AD baseada no usuário ou no serviço.

 A autenticação do Azure AD é descrita na seção de [autenticação de usuário do Azure ad](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication) do serviço do [asa (âncora espacial) do Azure](../../spatial-anchors/index.yml) .

 Para obter mais informações, consulte o [tutorial: protegendo a renderização remota do Azure e o armazenamento de modelos-autenticação de Azure Active Directory](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="azure-role-based-access-control"></a>Controle de acesso baseado em função do Azure

Para ajudar a controlar o nível de acesso concedido ao seu serviço, use as seguintes funções ao conceder acesso baseado em função:

* **Administrador de renderização remota**: fornece ao usuário recursos de conversão, gerenciamento de sessão, renderização e diagnóstico para a renderização remota do Azure.
* **Cliente de renderização remota**: fornece ao usuário recursos de gerenciamento de sessão, renderização e diagnóstico para a renderização remota do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta](create-an-account.md)
* [Usar as APIs de front-end do Azure para autenticação](frontend-apis.md)
* [Scripts de exemplo do PowerShell](../samples/powershell-example-scripts.md)