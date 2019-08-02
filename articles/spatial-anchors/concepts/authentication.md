---
title: Autenticação e autorização para âncoras espacial do Azure | Microsoft Docs
description: Saiba mais sobre as várias maneiras para âncoras espacial do Azure e os níveis de controle que tem como acesso a âncoras espacial do Azure que pode autenticar um aplicativo ou serviço.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: c7ffa432c9311ba9d4ecf4ba82c375e2dad988d0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478537"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticação e autorização para âncoras espacial do Azure

Nesta seção, abordaremos as várias maneiras que você pode autenticar a âncoras espacial do Azure do seu aplicativo ou serviço da web e as maneiras em que você pode usar o controle de acesso baseado em função no diretório do Azure (AD Azure) para controlar o acesso às suas contas âncoras espacial.  

## <a name="overview"></a>Visão geral

![Uma visão geral de autenticação a ser âncoras espacial do Azure](./media/spatial-anchors-authentication-overview.png)

Para acessar uma determinada conta âncoras espacial do Azure, os clientes precisam primeiro obter um token de acesso do Azure misto realidade serviço STS (Security Token). Os tokens obtidos do STS ao vivo para 24 horas e contêm informações para os serviços espaciais âncoras tomar decisões de autorização da conta e certifique-se de que somente entidades autorizadas podem acessá-la. 

Tokens de acesso podem ser obtidos no exchange a partir de qualquer um dos chaves da conta ou os tokens emitidos pelo AD do Azure. 

Chaves de conta permitem que você começar a usar rapidamente usando o serviço âncoras espacial do Azure; No entanto, antes de implantar seu aplicativo para produção, é recomendável que você atualize seu aplicativo para usar a autenticação baseada no AD do Azure. 

Os tokens de autenticação do AD do Azure podem ser obtidos de duas maneiras:

- Se você estiver criando um aplicativo empresarial e sua empresa está usando o Azure AD como seu sistema de identidade, você pode usar o Azure com base no usuário a autenticação do AD em seu aplicativo e conceder acesso a suas contas de âncoras espaciais usando seus grupos de segurança do Azure AD existentes, ou diretamente aos usuários em sua organização. 
- Caso contrário, é recomendável que você obtenha tokens do AD do Azure de um serviço web que dão suporte a seu aplicativo. Usar um serviço web de suporte é o método de autenticação recomendado para aplicativos de produção, pois evita inserindo as credenciais para acessar as âncoras espacial do Azure em seu aplicativo cliente. 

## <a name="account-keys"></a>Chaves da conta

Usando chaves de conta para acessar sua conta âncoras espacial do Azure é a maneira mais simples para começar a usar. Você encontrará as chaves da conta no portal do Azure. Navegue até sua conta e selecione a guia "Chaves".

![Uma visão geral de autenticação a ser âncoras espacial do Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Duas chaves ficam disponíveis, que são ambos simultaneamente válidas para o acesso à conta âncoras espacial. É recomendável que você regularmente atualizar a chave usada para acessar a conta; ter dois separar habilitar chaves válidas essas atualizações sem tempo de inatividade; Você só precisa atualizar, como alternativa, a chave primária e secundária. 

O SDK tem suporte interno para autenticar com chaves de conta; Basta definir a propriedade de AccountKey no seu objeto cloudSession. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrttabcppwinrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

***

Depois disso, o SDK tratará a troca da chave de conta para um token de acesso e o armazenamento em cache necessárias de tokens para seu aplicativo. 

> [!WARNING] 
> Uso de chaves de conta é recomendado para integração rápida, mas durante o desenvolvimento/criação de protótipos apenas. É altamente recomendável não para enviar seu aplicativo para produção usando uma chave de conta inserido nele e em vez disso, usar o Azure baseada em serviço ou usuário autenticação AD se aproxima de Avançar listado.

## <a name="azure-ad-user-authentication"></a>Autenticação de usuário do Azure AD

Para aplicativos destinados a usuários do Active Directory do Azure, a abordagem recomendada é usar um token do AD do Azure para o usuário, que pode ser obtido usando a biblioteca ADAL, conforme descrito na documentação a seguir: [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); você deve seguir as etapas listadas em "Inícios rápidos", que incluem:

1. Configuração no portal do Azure
    1.  Registre seu aplicativo no Azure AD como sendo **aplicativo nativo**. Como parte do registro, você precisará determinar se seu aplicativo deve ser multilocatário ou não e fornecer o redirecionamento de URLs permitidas para o seu aplicativo.  
    2.  Conceda a seu aplicativo ou o acesso a seus recursos: 
        1.  Navegue até seu recurso âncoras espacial no portal do Azure
        2.  Alterne para o **controle de acesso (IAM)** guia
        3.  Ocorrências **Adicionar atribuição de função**
            1.  [Selecione uma função](#role-based-access-control)
            2.  No **selecionar** , insira o nome de usuário (s), grupo (s) e/ou ao qual você deseja atribuir acesso de aplicativo (s). 
            3.  Ocorrências **salvar**.
2. Em seu código:
    1.  Certifique-se de usar o **ID do aplicativo** e **Uri de redirecionamento** do seu próprio aplicativo do Azure AD como o **ID do cliente** e **RedirectUri** parâmetros em ADAL
    2.  Defina as informações de locatário:
        1.  Se seu aplicativo suporta **minha organização só**, substitua esse valor com seu **ID do locatário** ou **nome do locatário** (por exemplo, contoso.microsoft.com)
        2.  Se seu aplicativo suporta **contas em qualquer diretório organizacional**, substitua esse valor com **organizações**
        3.  Se seu aplicativo suporta **usuários de contas da Microsoft todos os**, substitua esse valor com **comuns**
    3.  Na sua solicitação de token, defina as **resource** para "https://sts.mixedreality.azure.com". Esse "recurso" indica ao AD do Azure que seu aplicativo está solicitando um token para o serviço âncoras espacial do Azure.  

Com isso, seu aplicativo deve ser capaz de obter da ADAL um token do AD do Azure; Você pode definir esse token do Azure AD como o **authenticationToken** em seu objeto de configuração de sessão de nuvem. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

***

## <a name="azure-ad-service-authentication"></a>Autenticação de serviço do Azure AD

A opção recomendada para implantar aplicativos, aproveitando as âncoras espacial do Azure para a produção é aproveitar um serviço de back-end que serão as solicitações de autenticação do agente. O esquema geral deve ser conforme descrito neste diagrama:

![Uma visão geral de autenticação a ser âncoras espacial do Azure](./media/spatial-anchors-aad-authentication.png)

Aqui, supõe-se que seu aplicativo usa seu próprio mecanismo (por exemplo: Conta da Microsoft, o PlayFab, Facebook, Google ID, nome de usuário e senha personalizada, etc.) para autenticar seu serviço back-end. Depois que os usuários são autenticados para seu serviço de back-end, que o serviço pode recuperar um token do AD do Azure, trocá-lo por um token de acesso para âncoras espacial do Azure e retorná-la ao aplicativo cliente.

O token de acesso do AD do Azure é recuperado usando a biblioteca ADAL, conforme descrito na documentação a seguir: [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); você deve seguir as etapas listadas em "Inícios rápidos", que incluem:

1.  Configuração no portal do Azure:
    1.  Registre seu aplicativo no Azure AD:
        1.  No portal do Azure, navegue até **Azure Active Directory**e selecione **registros do aplicativo**
        2.  Selecione **novo registro de aplicativo**
        3.  Insira o nome do seu aplicativo, selecione **aplicativo Web / API** como o tipo de aplicativo e digite a URL de autenticação para seu serviço. Em seguida, pressione **criar**.
        4.  No aplicativo, pressione **as configurações**, em seguida, selecione o **chaves** guia. Insira o nome da sua chave, selecione uma duração e pressione **salvar**. Certifique-se de salvar o valor da chave que é exibido no momento, pois você precisará incluí-lo no código do seu serviço web.
    2.  Conceda a seu aplicativo e/ou os usuários o acesso ao recurso de:
        1.  Navegue até seu recurso âncoras espacial no portal do Azure
        2.  Alterne para o **controle de acesso (IAM)** guia
        3.  Ocorrências **Adicionar atribuição de função**
        1.  [Selecione uma função](#role-based-access-control)
        2.  No **selecionar** de campo, insira o nome do aplicativo (s) criado por você e à qual você deseja atribuir acesso. Se você quiser que os usuários do seu aplicativo têm funções diferentes em relação à conta âncoras espacial, você deve registrar vários aplicativos no Azure AD e atribuir a cada um uma função separada. Em seguida, implemente a lógica de autorização para usar a função correta para seus usuários.  
    3.  Ocorrências **salvar**.
2.  Em seu código (Observação: você pode usar o serviço de exemplo incluído no GitHub):
    1.  Certifique-se de usar a ID do aplicativo, o segredo do aplicativo e Uri de redirecionamento do seu próprio aplicativo do Azure AD como o ID do cliente, segredo e parâmetros de RedirectUri em ADAL
    2.  Defina a ID de locatário para sua própria ID de locatário AAAzure adicionar no parâmetro autoridade em ADAL
    3.  Na sua solicitação de token, defina as **resource** para "https://sts.mixedreality.azure.com" 

Com isso, seu serviço de back-end pode recuperar um token do AD do Azure. Em seguida, ele pode trocá-lo por um token MR que ela será retornada ao cliente. Usar um token do AD do Azure para recuperar um token MR é feito por meio de uma chamada REST. Aqui está uma chamada de exemplo:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Em que o cabeçalho de autorização é formatado da seguinte maneira: `Bearer <accoundId>:<accountKey>`

E a resposta contém o token MR em texto sem formatação.
 
Esse token MR, em seguida, é retornado ao cliente. Seu aplicativo cliente pode, em seguida, defini-lo como seu token de acesso na configuração de sessão de nuvem.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

***

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

Para ajudar a controlar o nível de acesso concedido a aplicativos, serviços ou usuários do AD do Azure do seu serviço, as seguintes funções foram criadas para a atribuição de conforme necessário nas suas contas do âncoras espacial do Azure:

- **Proprietário da conta âncoras espacial**: aplicativos ou usuários que tenham essa função são capazes de criar âncoras espaciais, consultá-las e excluí-los. Quando você autenticar-se à sua conta usando as chaves de conta, o **espacial proprietário da conta âncoras** função é atribuída à entidade de segurança autenticada. 
- **Colaborador da conta âncoras espacial**: aplicativos ou usuários que tenham essa função podem criar âncoras espaciais, a consulta para eles, mas não é possível excluí-los. 
- **Leitor de conta âncoras espacial**: aplicativos ou usuários que tenham essa função só são capazes de consultar âncoras espaciais, mas não é possível criar novos, excluir os existentes ou atualizar metadados em âncoras espaciais. Isso normalmente é usado para aplicativos em que alguns usuários coletem o ambiente, enquanto outros só podem recuperar âncoras colocadas anteriormente nesse ambiente.

## <a name="next-steps"></a>Próximas etapas

Crie seu primeiro aplicativo com âncoras espacial do Azure.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
