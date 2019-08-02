---
title: Alterações feitas em um projeto WebAPI quando você se conecta ao Azure AD
description: Descreve o que acontece ao seu projeto WebApi quando você se conecta ao Azure AD usando o Visual Studio
services: active-directory
author: ghogen
manager: douge
ms.service: active-directory
ms.subservice: develop
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19a36ca4b194d8031b4a263a092ecb52be74cdd0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324284"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu com meu projeto WebAPI (serviço conectado do Visual Studio do Azure Active Directory)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-webapi-getting-started.md)
> - [O que aconteceu](vs-active-directory-webapi-what-happened.md)

Este artigo identifica as alterações exatas feitas nos projetos ASP.NET do WebAPI, Aplicativo ASP.NET de Página única e API ASP.NET do Azure ao adicionar o [serviço conectado do Azure Active Directory usando o Visual Studio](vs-active-directory-add-connected-service.md). Também se aplica aos projetos ASP.NET de Serviço Móvel do Azure no Visual Studio 2015.

Para obter informações sobre como trabalhar com o serviço conectado, consulte [Introdução](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Referências adicionadas

Afeta as referências *.NET do arquivo de projeto) e `packages.config` (referências de NuGet).

| Tipo | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

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
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Somente Visual Studio 2015) |
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

- Adicionadas as seguintes entradas de configuração:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Somente Visual Studio 2017: Também adicionada a seguinte entrada no `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Adicionados os elementos `<dependentAssembly>` sob o nó `<runtime><assemblyBinding>` para `System.IdentityModel.Tokens.Jwt`.

- Se você tiver selecionado a opção **Ler dados do diretório**, adicionado a seguinte entrada de configuração em `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Adições e alterações de código

- Adicionado o `[Authorize]` atributo para `Controllers/ValueController.cs` e outros controladores existentes.

- Adicionado uma classe de inicialização de autenticação, `App_Start/Startup.Auth.cs`, que contém a lógica de inicialização para a autenticação do Azure AD, ou modificado de acordo. Se você tiver selecionado a opção **Ler dados do diretório**, esse arquivo também contém código para receber um código de OAuth e trocá-lo por um token de acesso.

- (Visual Studio 2015 com ASP.NET 4 somente do aplicativo) Removido `App_Start/IdentityConfig.cs` e adicionado `Controllers/AccountController.cs`, `Models/IdentityModel.cs`, e `Providers/ApplicationAuthProvider.cs`.

- Adicionado `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) ou `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), que contém informações que o Visual Studio usa para acompanhar a adição do serviço conectado.

### <a name="file-backup-visual-studio-2015"></a>Backup de arquivo (Visual Studio 2015)

Ao adicionar o serviço conectado, o Visual Studio 2015 faz backup dos arquivos alterados e removidos. Todos os arquivos afetados são salvos na pasta `Backup/AzureAD`. O Visual Studio 2017 não cria backups.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Alterações no Azure

- Criado um Aplicativo do Azure AD no domínio que você selecionou ao adicionar o serviço conectado.
- Atualizado o aplicativo para incluir a permissão **Ler dados do diretório** se essa opção foi selecionada.

[Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Próximas etapas

- [Cenários de autenticação do Azure Active Directory](authentication-scenarios.md)
- [Adicionar entrada com a Microsoft para um aplicativo Web ASP.NET](quickstart-v1-aspnet-webapp.md)
