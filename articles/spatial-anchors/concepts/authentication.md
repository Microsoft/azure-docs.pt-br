---
title: Autenticação e autorização
description: Saiba mais sobre as várias maneiras pelas quais um aplicativo ou serviço pode se autenticar para âncoras espaciais do Azure e os níveis de controle que você precisa para portar o acesso a âncoras espaciais.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 01065f9ac26599d26d6e2a6979eae1e559a82854
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97722956"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticação e autorização para Âncoras Espaciais do Azure

Neste artigo, você aprenderá as várias maneiras de autenticar as âncoras espaciais do Azure de seu aplicativo ou serviço Web. Você também aprenderá sobre as maneiras como você pode usar o Azure RBAC (controle de acesso baseado em função) no Azure Active Directory (Azure AD) para controlar o acesso às suas contas de âncoras espaciais.

## <a name="overview"></a>Visão geral

![Diagrama que mostra uma visão geral da autenticação para âncoras espaciais do Azure.](./media/spatial-anchors-authentication-overview.png)

Para acessar uma determinada conta de Âncoras Espaciais do Azure, os clientes precisam primeiro obter um token de acesso do STS (serviço de token de segurança) da Realidade Misturada do Azure. Tokens obtidos do STS têm um tempo de vida de 24 horas. Elas contêm informações que os serviços de âncoras espaciais usam para tomar decisões de autorização na conta e garantir que apenas entidades de segurança autorizadas possam acessar a conta.

Tokens de acesso podem ser obtidos no Exchange para chaves de conta ou tokens emitidos pelo Azure AD.

As chaves de conta permitem que você comece rapidamente a usar o serviço âncoras espaciais do Azure. Mas antes de implantar seu aplicativo para produção, recomendamos que você atualize seu aplicativo para usar a autenticação do Azure AD.

Você pode obter tokens de autenticação do Azure AD de duas maneiras:

- Se você estiver criando um aplicativo empresarial e sua empresa estiver usando o Azure AD como seu sistema de identidade, você poderá usar a autenticação do Azure AD baseada no usuário em seu aplicativo. Em seguida, conceda acesso às suas contas de âncoras espaciais usando seus grupos de segurança existentes do Azure AD. Você também pode conceder acesso diretamente aos usuários em sua organização.
- Caso contrário, recomendamos que você obtenha tokens do Azure AD de um serviço Web que dê suporte ao seu aplicativo. Recomendamos esse método para aplicativos de produção porque ele permite que você evite inserir as credenciais para acessar as âncoras espaciais do Azure no aplicativo cliente.

## <a name="account-keys"></a>Chaves de conta

A maneira mais fácil de começar é usar chaves de conta para acesso à sua conta de âncoras espaciais do Azure. Você pode obter suas chaves de conta no portal do Azure. Vá para sua conta e selecione a guia **chaves** :

![Captura de tela que mostra a guia chaves com o botão Copiar para a chave primária realçada.](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Há duas chaves disponíveis. Ambos são válidos simultaneamente para acesso à conta de âncoras espaciais. É recomendável que você atualize regularmente a chave usada para acessar a conta. Ter duas chaves válidas separadas permite essas atualizações sem tempo de inatividade. Você só precisa atualizar a chave primária e a chave secundária como alternativa.

O SDK tem suporte interno para autenticação por meio de chaves de conta. Você só precisa definir a `AccountKey` propriedade em seu `cloudSession` objeto:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Depois de definir essa propriedade, o SDK manipulará a troca da chave de conta para um token de acesso e o cache de tokens necessário para seu aplicativo.

> [!WARNING]
> Recomendamos que você use chaves de conta para a integração rápida, mas somente durante o desenvolvimento/criação de protótipos. Não recomendamos que você envie seu aplicativo para produção com uma chave de conta incorporada. Em vez disso, use as abordagens de autenticação do Azure AD baseadas no usuário ou no serviço descritas a seguir.

## <a name="azure-ad-user-authentication"></a>Autenticação de usuário do Azure AD

Para aplicativos direcionados Azure Active Directory usuários, recomendamos que você use um token do Azure AD para o usuário. Você pode obter esse token usando o [MSAL](../../active-directory/develop/msal-overview.md). Siga as etapas no guia de [início rápido ao registrar um aplicativo](../../active-directory/develop/quickstart-register-app.md), que inclui:

**No portal do Azure**
1.    Registre seu aplicativo no Azure AD como um aplicativo nativo. Como parte do registro, você precisará determinar se seu aplicativo deve ser multilocatário. Você também precisará fornecer as URLs de redirecionamento permitidas para seu aplicativo.
1.  Vá para a guia **permissões de API** .
2.  Selecione **Adicionar uma permissão**.
    1.  Selecione **provedor de recursos de realidade misturada** na guia **APIs que minha organização usa** .
    2.  Selecione **Permissões delegadas**.
    3.  Selecione **mixedreality. Signe** em **mixedreality**.
    4.  Escolha **Adicionar permissões**.
3.  Selecione **conceder consentimento do administrador**.

2. Conceda ao seu aplicativo ou usuários acesso ao seu recurso:
   1.    Vá para o recurso âncoras espaciais no portal do Azure.
   2.    Vá para a guia **controle de acesso (iam)** .
   3.    Selecione **Adicionar atribuição de função**.
   1.    [Selecione uma função](#azure-role-based-access-control).
   2.    Na caixa **selecionar** , insira os nomes dos usuários, grupos e/ou aplicativos aos quais você deseja atribuir acesso.
   3.    Clique em **Salvar**.

**Em seu código**
1.    Certifique-se de usar a ID do aplicativo e o URI de redirecionamento do seu próprio aplicativo do Azure AD para os parâmetros **ID do cliente** e **RedirectUri** em MSAL.
2.    Defina as informações do locatário:
        1.    Se seu aplicativo der suporte **apenas à minha organização**, substitua esse valor pela sua **ID de locatário** ou nome do **locatário**. Por exemplo, contoso.microsoft.com.
        2.    Se seu aplicativo oferecer suporte a **contas em qualquer diretório organizacional**, substitua esse valor pelas **organizações**.
        3.    Se seu aplicativo der suporte a **todos os conta Microsoft usuários**, substitua esse valor por **comum**.
3.    Em sua solicitação de token, defina o **escopo** como **" `https://sts.<account-domain>//.default` "**, em que `<account-domain>` é substituído pelo **domínio da conta** para sua conta de âncoras espaciais do Azure. Um exemplo de escopo para uma conta de âncoras espaciais do Azure no domínio da conta leste dos EUA 2 é **" `https://sts.mixedreality.azure.com//.default` "**. Esse escopo indicará ao Azure AD que seu aplicativo está solicitando um token para o STS (serviço de token de segurança) da realidade misturada.

Depois de concluir essas etapas, seu aplicativo deverá ser capaz de obter do MSAL um token do Azure AD. Você pode definir esse token do Azure AD como o `authenticationToken` em seu objeto de configuração de sessão de nuvem:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Autenticação de serviço do Azure AD

Para implantar aplicativos que usam âncoras espaciais do Azure para produção, recomendamos que você use um serviço de back-end que atenderá às solicitações de autenticação do Broker. Aqui está uma visão geral do processo:

![Diagrama que fornece uma visão geral da autenticação para âncoras espaciais do Azure.](./media/spatial-anchors-aad-authentication.png)

Aqui, supõe-se que seu aplicativo usa seu próprio mecanismo para autenticar em seu serviço de back-end. (Por exemplo, um conta Microsoft, PlayFab, Facebook, uma ID do Google ou um nome de usuário e senha personalizados.)  Depois que os usuários são autenticados no serviço de back-end, esse serviço pode recuperar um token do Azure AD, trocar o ti por um token de acesso para âncoras espaciais do Azure e retorná-lo de volta para o aplicativo cliente.

O token de acesso do AD do Azure é recuperado por meio do [MSAL](../../active-directory/develop/msal-overview.md). Siga as etapas no [início rápido registrar um aplicativo](../../active-directory/develop/quickstart-register-app.md), que incluem:

**No portal do Azure**
1.    Registrar seu aplicativo no Azure AD:
        1.    Na portal do Azure, selecione **Azure Active Directory** e, em seguida, selecione **registros de aplicativo**.
        2.    Selecione **Novo registro**.
        3.    Insira o nome do seu aplicativo, selecione **aplicativo Web/API** como o tipo de aplicativo e insira a URL de autenticação para seu serviço. Selecione **Criar**.
2.    No aplicativo, selecione **configurações** e, em seguida, selecione a guia **certificados e segredos** . Crie um novo segredo do cliente, selecione uma duração e, em seguida, selecione **Adicionar**. Lembre-se de salvar o valor secreto. Você precisará incluí-lo no código do serviço Web.
3.    Conceda ao seu aplicativo e/ou usuários acesso ao seu recurso:
        1.    Vá para o recurso âncoras espaciais no portal do Azure.
        2.    Vá para a guia **controle de acesso (iam)** .
        3.    Selecione **Adicionar atribuição de função**.
        4.    [Selecione uma função](#azure-role-based-access-control).
        5.    Na caixa **selecionar** , insira o nome ou os nomes dos aplicativos aos quais você deseja atribuir acesso. Se você quiser que os usuários do aplicativo tenham funções diferentes em relação à conta de âncoras espaciais, registre vários aplicativos no Azure AD e atribua uma função separada a cada um. Em seguida, implemente sua lógica de autorização para usar a função certa para seus usuários.

              > [!NOTE]
              > No painel **Adicionar atribuição de função** , em **atribuir acesso a**, selecione **usuário, grupo ou entidade de serviço do Azure ad**.

        6.    Clique em **Salvar**.

**Em seu código**

>[!NOTE]
> Você pode usar o [exemplo de serviço](https://github.com/Azure/azure-spatial-anchors-samples/tree/master/Sharing/SharingServiceSample) que está disponível como parte das [âncoras espaciais de exemplo de aplicativos](https://github.com/Azure/azure-spatial-anchors-samples).

1.    Certifique-se de usar a ID do aplicativo, o segredo do aplicativo e o URI de redirecionamento do seu próprio aplicativo do Azure AD como a **ID do cliente**, o **segredo** e os parâmetros **RedirectUri** em MSAL.
2.    Defina a ID de locatário para sua própria ID de locatário do Azure AD no parâmetro **Authority** em MSAL.
3.    Em sua solicitação de token, defina o **escopo** como **" `https://sts.<account-domain>//.default` "**, em que `<account-domain>` é substituído pelo **domínio da conta** para sua conta de âncoras espaciais do Azure. Um exemplo de escopo para uma conta de âncoras espaciais do Azure no domínio da conta leste dos EUA 2 é **" `https://sts.mixedreality.azure.com//.default` "**.

Depois de concluir essas etapas, o serviço de back-end pode recuperar um token do Azure AD. Em seguida, ele pode alterá-lo para um token MR que ele retornará ao cliente. Usar um token do Azure AD para recuperar um token MR é feito por meio de uma chamada REST. Aqui está um exemplo de chamada:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

O cabeçalho de autorização é formatado da seguinte maneira: `Bearer <Azure_AD_token>`

A resposta contém o token MR em texto sem formatação.

Esse token MR é retornado para o cliente. Seu aplicativo cliente pode defini-lo como seu token de acesso na configuração da sessão de nuvem:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Controle de acesso baseado em função do Azure

Para ajudá-lo a controlar o nível de acesso concedido a aplicativos, serviços ou usuários do Azure AD do seu serviço, você pode atribuir essas funções preexistentes conforme necessário em relação às suas contas de âncoras espaciais do Azure:

- **Proprietário da conta de âncoras espaciais**. Os aplicativos ou usuários que têm essa função podem criar âncoras espaciais, consultá-los e excluí-los. Quando você se autentica em sua conta usando chaves de conta, a função de proprietário da conta de âncoras espaciais é atribuída à entidade de segurança autenticada.
- **Colaborador de conta de âncoras espaciais**. Os aplicativos ou usuários que têm essa função podem criar âncoras espaciais e consultá-los, mas não podem excluí-los.
- **Leitor de conta de âncoras espaciais**. Os aplicativos ou usuários que têm essa função só podem consultar âncoras espaciais. Eles não podem criar novos, excluir os existentes ou atualizar os metadados neles. Essa função é normalmente usada para aplicativos em que alguns usuários organizadam o ambiente, mas outros podem apenas recuperar âncoras previamente colocadas no ambiente.

## <a name="next-steps"></a>Próximas etapas

Crie seu primeiro aplicativo com âncoras espaciais do Azure:

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
