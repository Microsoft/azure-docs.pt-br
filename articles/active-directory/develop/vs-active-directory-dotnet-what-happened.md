---
title: Alterações feitas em um projeto do MVC quando você se conecta ao Azure AD
description: Descreve o que acontece ao seu projeto do MVC quando você se conecta ao AD do Azure usando os serviços conectados do Visual Studio
services: active-directory
ms.service: active-directory
ms.subservice: develop
author: ghogen
manager: douge
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8a2b73276dd6acc99e91df768ebf613b9e70477
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320754"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu com meu projeto do MVC (serviço conectado do Active Directory do Azure do Visual Studio)?

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)

Este artigo identifica as alterações exatas feitas em um projeto ASP.NET MVC ao adicionar o [serviço conectado do Azure Active Directory usando o Visual Studio](vs-active-directory-add-connected-service.md).

Para obter informações sobre como trabalhar com o serviço conectado, consulte [Introdução](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Referências adicionadas

Afeta as referências *.NET do arquivo de projeto) e `packages.config` (referências de NuGet).

| Tipo | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.OpenIdConnect |
| .NET; NuGet | Owin |
| .NET        | System.IdentityModel |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

Referências adicionais se você selecionou a opção **Ler dados do diretório**:

| Tipo | Referência |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (somente Visual Studio 2015) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (somente Visual Studio 2015) |
| .NET; NuGet | System.Spatial |

As seguintes referências são removidas (somente projetos ASP.NET 4, como no Visual Studio 2015):

| Tipo | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Alterações de arquivo de projeto

- Defina a propriedade `IISExpressSSLPort` para um número distinto.
- Defina a propriedade `WebProject_DirectoryAccessLevelKey` para 0, ou 1 se você selecionou a opção **Ler dados do diretório**.
- Defina a propriedade `IISUrl` e `https://localhost:<port>/` onde `<port>` corresponde ao valor `IISExpressSSLPort`.

## <a name="webconfig-or-appconfig-changes"></a>alterações de web.config ou app.config

- Adicionadas as entradas de configuração a seguir:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Adicionados os elementos `<dependentAssembly>` no nó `<runtime><assemblyBinding>` para `System.IdentityModel.Tokens.Jwt` e `Microsoft.IdentityModel.Protocol.Extensions`.

Alterações adicionais se você selecionou a opção **Ler dados do diretório**:

- Adicionada a entrada de configuração a seguir em `<appSettings>`:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Adicionou os seguintes elementos em `<configuration>`; valores para o project.mdf-file e project-catalog-id variam de:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit https://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- Adicionados os elementos `<dependentAssembly>` no nó `<runtime><assemblyBinding>` para `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm`, e `Microsoft.Data.OData`.

## <a name="code-changes-and-additions"></a>Adições e alterações de código

- Adicionado o atributo `[Authorize]` para `Controllers/HomeController.cs` e outros controladores existentes.

- Adicionada uma classe de inicialização de autenticação, `App_Start/Startup.Auth.cs`, que contém a lógica de inicialização para a autenticação do Azure AD. Se você tiver selecionado a opção **Ler dados do diretório**, esse arquivo também contém código para receber um código de OAuth e do trocá-lo por um token de acesso.

- Adicionada uma classe de controlador, `Controllers/AccountController.cs`, que contém os métodos `SignIn` e `SignOut`.

- Adicionada uma exibição parcial, `Views/Shared/_LoginPartial.cshtml`, que contém um link de ação para `SignIn` e `SignOut`.

- Adicionada uma exibição parcial, `Views/Account/SignoutCallback.cshtml`, que contém o HTML para a saída da interface do usuário.

- Atualizado o método `Startup.Configuration` para incluir uma chamada para `ConfigureAuth(app)` se a classe já existir; caso contrário adicionada uma classe `Startup` que inclui chamadas para o método.

- Adicionado `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) ou `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), que contém informações que o Visual Studio usa para acompanhar a adição do serviço conectado.

- Se você selecionou a opção **Ler dados do diretório**, adicionou `Models/ADALTokenCache.cs` e `Models/ApplicationDbContext.cs` para oferecer suporte a cache de token. Também é adicionado um controlador adicional e a exibição para ilustrar acessando informações de perfil de usuário usando os APIs do Azure graph: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml`, e `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Backup de arquivo (Visual Studio 2015)

Ao adicionar o serviço conectado, o Visual Studio 2015 faz backup dos arquivos alterados e removidos. Todos os arquivos afetados são salvos na pasta `Backup/AzureAD`. O Visual Studio 2017 e posterior não cria backups.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Alterações no Azure

- Criado um Aplicativo do Azure AD no domínio que você selecionou ao adicionar o serviço conectado.
- Atualizado o aplicativo para incluir a permissão **Ler dados do diretório** se essa opção foi selecionada.

[Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Próximas etapas

- [Cenários de autenticação do Azure Active Directory](authentication-scenarios.md)
- [Adicionar entrada com a Microsoft para um aplicativo Web ASP.NET](quickstart-v1-aspnet-webapp.md)
