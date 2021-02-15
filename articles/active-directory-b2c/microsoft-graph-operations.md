---
title: Gerenciar recursos com Microsoft Graph
titleSuffix: Azure AD B2C
description: Como gerenciar recursos em um locatário Azure AD B2C chamando a API de Microsoft Graph e usando uma identidade de aplicativo para automatizar o processo.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a7e9e523d3aae7cf1444c048c023ca1d85fde41f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98952216"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Gerenciar Azure AD B2C com Microsoft Graph

Microsoft Graph permite que você gerencie recursos em seu diretório Azure AD B2C. As operações de API de Microsoft Graph a seguir têm suporte para o gerenciamento de recursos de Azure AD B2C, incluindo usuários, provedores de identidade, fluxos de usuário, políticas personalizadas e chaves de política. Cada link nas seções a seguir visa a página correspondente dentro do Microsoft Graph referência de API para essa operação. 

## <a name="prerequisites"></a>Pré-requisitos

Para usar o MS API do Graph e interagir com recursos em seu locatário do Azure AD B2C, você precisa de um registro de aplicativo que conceda as permissões para fazer isso. Siga as etapas no artigo [gerenciar Azure ad B2C com Microsoft Graph](microsoft-graph-get-started.md) para criar um registro de aplicativo que seu aplicativo de gerenciamento pode usar. 

## <a name="user-management"></a>Gerenciamento de usuários

- [Listar usuários](/graph/api/user-list)
- [Criar um usuário do consumidor](/graph/api/user-post-users)
- [Obter um usuário](/graph/api/user-get)
- [Atualizar um usuário](/graph/api/user-update)
- [Excluir um usuário](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>Gerenciamento de número de telefone de usuário (beta)

Um número de telefone que pode ser usado por um usuário para entrar usando [chamadas de voz ou SMS](identity-provider-local.md#phone-sign-in-preview), ou [autenticação multifator](multi-factor-authentication.md). Para obter mais informações, consulte [API de métodos de autenticação do Azure ad](/graph/api/resources/phoneauthenticationmethod).

- [Adicionar](/graph/api/authentication-post-phonemethods)
- [Lista](/graph/api/authentication-list-phonemethods)
- [Obter](/graph/api/phoneauthenticationmethod-get)
- [Atualizar](/graph/api/phoneauthenticationmethod-update)
- [Delete (excluir)](/graph/api/phoneauthenticationmethod-delete)

Observe que a operação de [lista](/graph/api/authentication-list-phonemethods) retorna apenas números de telefone habilitados. O número de telefone a seguir deve ser habilitado para uso com as operações de lista. 

![Habilitar entrada pelo telefone](./media/microsoft-graph-operations/enable-phone-sign-in.png)

## <a name="self-service-password-reset-email-address-beta"></a>Endereço de email de redefinição de senha de autoatendimento (beta)

Um endereço de email que pode ser usado por uma [conta de entrada de nome de usuário](identity-provider-local.md#username-sign-in) para redefinir a senha. Para obter mais informações, consulte [API de métodos de autenticação do Azure ad](/graph/api/resources/emailauthenticationmethod).

- [Adicionar](/graph/api/emailauthenticationmethod-post)
- [Lista](/graph/api/emailauthenticationmethod-list)
- [Obter](/graph/api/emailauthenticationmethod-get)
- [Atualizar](/graph/api/emailauthenticationmethod-update)
- [Delete (excluir)](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>Provedores de identidade

Gerencie os [provedores de identidade](add-identity-provider.md) disponíveis para seus fluxos de usuário em seu locatário Azure ad B2C.

- [Listar provedores de identidade registrados no locatário do Azure AD B2C](/graph/api/identityprovider-list)
- [Criar um provedor de identidade](/graph/api/identityprovider-post-identityproviders)
- [Obter um provedor de identidade](/graph/api/identityprovider-get)
- [Atualizar provedor de identidade](/graph/api/identityprovider-update)
- [Excluir um provedor de identidade](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Fluxo de usuário

Configure políticas predefinidas para inscrição, entrada, inscrição combinada e entrada, redefinição de senha e atualização de perfil.

- [Listar fluxos de usuário](/graph/api/identitycontainer-list-b2cuserflows)
- [Criar um fluxo de usuário](/graph/api/identitycontainer-post-b2cuserflows)
- [Obter um fluxo de usuário](/graph/api/b2cidentityuserflow-get)
- [Excluir um fluxo de usuário](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>Métodos de autenticação de fluxo de usuário (beta)

Escolha um mecanismo para permitir que os usuários se registrem por meio de contas locais. As contas locais são as contas em que o Azure AD faz a asserção de identidade. Para obter mais informações, consulte [tipo de recurso b2cAuthenticationMethodsPolicy](/graph/api/resources/b2cauthenticationmethodspolicy).

- [Obter](/graph/api/b2cauthenticationmethodspolicy-get)
- [Atualizar](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>Políticas personalizadas

As operações a seguir permitem que você gerencie suas políticas de estrutura de confiança Azure AD B2C, conhecidas como [políticas personalizadas](custom-policy-overview.md).

- [Listar todas as políticas de estrutura de confiança configuradas em um locatário](/graph/api/trustframework-list-trustframeworkpolicies)
- [Criar política de estrutura confiável](/graph/api/trustframework-post-trustframeworkpolicy)
- [Ler propriedades de uma política de estrutura de confiança existente](/graph/api/trustframeworkpolicy-get)
- [Atualize ou crie a política de estrutura confiável.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Excluir uma política de estrutura de confiança existente](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Chaves de política

A estrutura de experiência de identidade armazena os segredos referenciados em uma política personalizada para estabelecer a confiança entre os componentes. Esses segredos podem ser chaves/valores simétricos ou assimétricos. No portal do Azure, essas entidades são mostradas como **chaves de política**.

O recurso de nível superior para chaves de política na API de Microsoft Graph é o conjunto de [chaves de estrutura confiável](/graph/api/resources/trustframeworkkeyset). Cada conjunto de **chaves** contém pelo menos uma **chave**. Para criar uma chave, primeiro crie um conjunto de chaves vazio e, em seguida, gere uma chave no conjunto de chaves. Você pode criar um segredo manual, carregar um certificado ou uma chave PKCS12. A chave pode ser um segredo gerado, uma cadeia de caracteres (como o segredo do aplicativo do Facebook) ou um certificado que você carrega. Se um conjunto de chaves tiver várias chaves, somente uma das chaves estará ativa.

### <a name="trust-framework-policy-keyset"></a>Conjunto de chaves de política de estrutura confiável

- [Listar os conjuntos de keyframework de confiança](/graph/api/trustframework-list-keysets)
- [Criar um conjunto de conjuntos de estrutura de confiança](/graph/api/trustframework-post-keysets)
- [Obter um conjunto de chaves](/graph/api/trustframeworkkeyset-get)
- [Atualizar conjuntos de keyframework de confiança](/graph/api/trustframeworkkeyset-update)
- [Excluir um conjunto de conjuntos de estrutura de confiança](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Chave de política de estrutura confiável

- [Obter a chave atualmente ativa no conjunto de chaves](/graph/api/trustframeworkkeyset-getactivekey)
- [Gerar uma chave no conjunto de chaves](/graph/api/trustframeworkkeyset-generatekey)
- [Carregar um segredo baseado em cadeia de caracteres](/graph/api/trustframeworkkeyset-uploadsecret)
- [Carregar um certificado X. 509](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Carregar um certificado de formato PKCS12](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplicativos

- [Listar aplicativos](/graph/api/application-list)
- [Criar um aplicativo](/graph/api/resources/application)
- [Atualizar o aplicativo](/graph/api/application-update)
- [Criar servicePrincipalName](/graph/api/resources/serviceprincipal)
- [Criar concessão de oauth2Permission](/graph/api/resources/oauth2permissiongrant)
- [Excluir aplicativo](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Propriedades de extensão do aplicativo

- [Listar Propriedades de extensão](/graph/api/application-list-extensionproperty)

O Azure AD B2C fornece um diretório que pode conter 100 atributos personalizados por usuário. Para fluxos de usuário, essas propriedades de extensão são [gerenciadas usando o portal do Azure](user-flow-custom-attributes.md). Para políticas personalizadas, Azure AD B2C cria a propriedade para você, na primeira vez em que a política grava um valor na propriedade de extensão.

## <a name="audit-logs"></a>Logs de auditoria

- [Listar logs de auditoria](/graph/api/directoryaudit-list)

Para obter mais informações sobre como acessar Azure AD B2C logs de auditoria, consulte [Acessando Azure ad B2C logs de auditoria](view-audit-logs.md).

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Exemplo de código: como gerenciar programaticamente contas de usuário

Este exemplo de código é um aplicativo de console .NET Core que usa o [SDK Microsoft Graph](/graph/sdks/sdks-overview) para interagir com Microsoft Graph API. Seu código demonstra como chamar a API para gerenciar programaticamente os usuários em um locatário Azure AD B2C.
Você pode [baixar o arquivo morto de exemplo](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [procurar o repositório](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) no GitHub ou clonar o repositório:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Depois de obter o exemplo de código, configure-o para o seu ambiente e, em seguida, compile o projeto:

1. Abra o projeto no [Visual Studio](https://visualstudio.microsoft.com) ou [Visual Studio Code](https://code.visualstudio.com).
1. Abra `src/appsettings.json`.
1. Na `appSettings` seção, substitua `your-b2c-tenant` pelo nome do seu locatário e `Application (client) ID` `Client secret` com os valores para o registro do aplicativo de gerenciamento. Para obter mais informações, consulte [registrar um aplicativo Microsoft Graph](microsoft-graph-get-started.md).
1. Abra uma janela de console no seu clone local do repositório, alterne para o `src` diretório e, em seguida, compile o projeto:

    ```console
    cd src
    dotnet build
    ```
    
1. Execute o aplicativo com o comando `dotnet`:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

O aplicativo exibe uma lista de comandos que você pode executar. Por exemplo, obter todos os usuários, obter um único usuário, excluir um usuário, atualizar a senha de um usuário e importação em massa.

### <a name="code-discussion"></a>Discussão de código

O código de exemplo usa o [SDK do Microsoft Graph](/graph/sdks/sdks-overview), que foi projetado para simplificar a criação de aplicativos de alta qualidade, eficientes e resilientes que acessam Microsoft Graph.

Qualquer solicitação para a API de Microsoft Graph requer um token de acesso para autenticação. A solução utiliza o pacote NuGet [Microsoft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) que fornece um wrapper baseado em cenário de autenticação da MSAL (biblioteca de autenticação da Microsoft) para uso com o SDK do Microsoft Graph.

O `RunAsync` método no arquivo _Program.cs_ :

1. Lê as configurações do aplicativo do _appsettings.jsno_ arquivo
1. Inicializa o provedor de autenticação usando o fluxo de [concessão de credenciais de cliente do OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) . Com o fluxo de concessão de credenciais de cliente, o aplicativo é capaz de obter um token de acesso para chamar a API de Microsoft Graph.
1. Configura o cliente do serviço de Microsoft Graph com o provedor de autenticação:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

O *GraphServiceClient* inicializado é usado em _UserService.cs_ para executar as operações de gerenciamento de usuário. Por exemplo, obter uma lista das contas de usuário no locatário:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Faça chamadas à API usando os SDKs de Microsoft Graph](/graph/sdks/create-requests) inclui informações sobre como ler e gravar informações de Microsoft Graph, usar `$select` para controlar as propriedades retornadas, fornecer parâmetros de consulta personalizados e usar os `$filter` parâmetros de `$orderBy` consulta e.

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
