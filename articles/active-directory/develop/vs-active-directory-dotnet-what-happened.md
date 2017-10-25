---
title: "Alterações feitas em um projeto de MVC quando você se conecta ao Azure AD | Microsoft Docs"
description: "Descreve o que acontece ao seu projeto do MVC quando você se conecta ao AD do Azure usando os serviços conectados do Visual Studio"
services: active-directory
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 095411a7fc854f4dce11921adb0f57c5389a8e13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu com meu projeto do MVC (serviço conectado do Active Directory do Azure do Visual Studio)?
> [!div class="op_single_selector"]
> * [Introdução](vs-active-directory-dotnet-getting-started.md)
> * [O que aconteceu](vs-active-directory-dotnet-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Referências foram adicionadas
### <a name="nuget-package-references"></a>Referências do pacote NuGet
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>Referências .NET
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel**
* **System.IdentityModel.Tokens.Jwt**
* **System.Runtime.Serialization**

## <a name="code-has-been-added"></a>Um código foi adicionado
### <a name="code-files-were-added-to-your-project"></a>Arquivos de código foram adicionados ao seu projeto
Uma classe de inicialização de autenticação **App_Start/Startup.Auth.cs** foi adicionada ao seu projeto contendo lógica de inicialização para autenticação do Azure AD. Além disso, uma classe de controlador, Controllers/AccountController.cs, foi adicionada, contendo os métodos **SignIn()** e **SignOut()**. Por fim, uma exibição parcial, **Views/Shared/_LoginPartial.cshtml**, foi adicionada, contendo um link de ação para SignIn/SignOut.

### <a name="startup-code-was-added-to-your-project"></a>O código de inicialização foi adicionado ao seu projeto
Se você já tiver uma classe Startup em seu projeto, o método **Configuration** terá sido atualizado para incluir uma chamada para **ConfigureAuth(app)**. Caso contrário, uma classe Startup foi adicionada ao projeto.

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>Seu app.config ou web.config tem novos valores de configuração
As entradas de configuração a seguir foram adicionadas.

    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>Foi criado um aplicativo do Azure Active Directory (AD)
Um Aplicativo Azure AD foi criado no diretório selecionado no assistente.

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Se eu marquei *Desabilitar autenticação de Contas de usuários individuais*, quais alterações adicionais foram feitas ao meu projeto?
Referências ao pacote NuGet foram removidas e arquivos foram removidos e copiados. Dependendo do estado do seu projeto, você terá de remover manualmente referências ou arquivos adicionais ou modificar o código conforme apropriado.

### <a name="nuget-package-references-removed-for-those-present"></a>Referências ao pacotes NuGet removidas (para aquelas presentes)
* **Microsoft.AspNet.Identity.Core**
* **Microsoft.AspNet.Identity.EntityFramework**
* **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Arquivos de código copiados e removidos (para aqueles presentes)
Cada um dos seguintes arquivos foi copiado e removido do projeto. Arquivos de backup estão localizados em uma pasta “Backup” na raiz do diretório do projeto.

* **App_Start\IdentityConfig.cs**
* **Controllers\ManageController.cs**
* **Models\IdentityModels.cs**
* **Models\ManageViewModels.cs**

### <a name="code-files-backed-up-for-those-present"></a>Arquivos de código copiados (para aqueles presentes)
Cada um dos seguintes arquivos foi copiado antes de ser substituído. Arquivos de backup estão localizados em uma pasta “Backup” na raiz do diretório do projeto.

* **Startup.cs**
* **App_Start\Startup.Auth.cs**
* **Controllers\AccountController.cs**
* **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Se eu tiver marcado *Ler dados do diretório*, quais alterações adicionais foram feitas ao meu projeto?
Referências extras foram adicionadas.

### <a name="additional-nuget-package-references"></a>Referências adicionais do pacote do NuGet
* **EntityFramework**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **System.Spatial**

### <a name="additional-net-references"></a>Referências adicionais do .NET
* **EntityFramework**
* **EntityFramework.SqlServer**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
* **System.Spatial**

### <a name="additional-code-files-were-added-to-your-project"></a>Arquivos de código adicional foram adicionados ao seu projeto
Dois arquivos foram adicionados para dar suporte ao caching de token: **Models\ADALTokenCache.cs** e **Models\ApplicationDbContext.cs**.  Um controlador e uma exibição extras foram adicionados para ilustrar o acesso às informações de perfil do usuário usando as APIs de gráfico do Azure.  Esses arquivos são **Controllers\UserProfileController.cs** e**Views\UserProfile\Index.cshtml**.

### <a name="additional-startup-code-was-added-to-your-project"></a>Um código de inicialização extra foi adicionado ao seu projeto
No arquivo **startup.auth.cs**, um novo objeto **OpenIdConnectAuthenticationNotifications** foi adicionado ao membro **Notifications** de **OpenIdConnectAuthenticationOptions**.  Isso serve para habilitar o recebimento do código do OAuth e a troca dele por um token de acesso.

### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Foram feitas alterações adicionais em sua app.config ou web.config
As outras entradas de configuração a seguir foram adicionadas.

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

As seções de configuração e a cadeia de conexão a seguir foram adicionadas.

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
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


### <a name="your-azure-active-directory-app-was-updated"></a>Seu aplicativo Active Directory do Azure foi atualizado
Seu Aplicativo do Azure Active Directory foi atualizado para incluir a permissão *Ler dados do diretório* e uma chave adicional foi criada, que foi então usada como o *ida:ClientSecret* no arquivo **web.config**.

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre o Active Directory do Azure](https://azure.microsoft.com/services/active-directory/)

