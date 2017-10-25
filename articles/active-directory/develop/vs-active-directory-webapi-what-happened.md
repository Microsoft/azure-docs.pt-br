---
title: "Alterações feitas em um projeto WebApi quando você se conecta ao Azure AD | Microsoft Docs"
description: "Descreve o que acontece ao seu projeto WebApi quando você se conecta ao Azure AD usando o Visual Studio"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 086e5a9622cad681cd282345d97e0c28ee7de2fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu com meu projeto da API Web (serviço conectado do Active Directory do Azure do Visual Studio)
> [!div class="op_single_selector"]
> * [Introdução](vs-active-directory-webapi-getting-started.md)
> * [O que aconteceu](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Referências foram adicionadas
### <a name="nuget-package-references"></a>Referências do pacote NuGet
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

### <a name="net-references"></a>Referências .NET
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

## <a name="code-changes"></a>Alterações de código
### <a name="code-files-were-added-to-your-project"></a>Arquivos de código foram adicionados ao seu projeto
Uma classe de inicialização de autenticação **App_Start/Startup.Auth.cs** foi adicionada ao seu projeto contendo lógica de inicialização para autenticação do Azure AD.

### <a name="startup-code-was-added-to-your-project"></a>O código de inicialização foi adicionado ao seu projeto
Se você já tiver uma classe de Inicialização em seu projeto, o método **Configuração** terá sido atualizado para incluir uma chamada para `ConfigureAuth(app)`. Caso contrário, uma classe Startup foi adicionada ao projeto.

### <a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>Seu arquivo app.config ou web.config possui novos valores de configuração.
As entradas de configuração a seguir foram adicionadas.

```
    <appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

### <a name="an-azure-ad-app-was-created"></a>Um aplicativo do Azure AD foi criado
Um Aplicativo Azure AD foi criado no diretório selecionado no assistente.

[Saiba mais sobre o Active Directory do Azure](https://azure.microsoft.com/services/active-directory/)

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Se eu tiver marcado *desabilitar a autenticação de Contas de Usuários Individuais*, quais alterações adicionais foram feitas ao meu projeto?
Referências ao pacote NuGet foram removidas e arquivos foram removidos e copiados. Dependendo do estado do seu projeto, você terá de remover manualmente referências ou arquivos adicionais ou modificar o código conforme apropriado.

### <a name="nuget-package-references-removed-for-those-present"></a>Referências ao pacotes NuGet removidas (para aquelas presentes)
* `Microsoft.AspNet.Identity.Core`
* `Microsoft.AspNet.Identity.EntityFramework`
* `Microsoft.AspNet.Identity.Owin`

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Arquivos de código copiados e removidos (para aqueles presentes)
Cada um dos seguintes arquivos foi copiado e removido do projeto. Arquivos de backup estão localizados em uma pasta “Backup” na raiz do diretório do projeto.

* `App_Start\IdentityConfig.cs`
* `Controllers\AccountController.cs`
* `Controllers\ManageController.cs`
* `Models\IdentityModels.cs`
* `Providers\ApplicationOAuthProvider.cs`

### <a name="code-files-backed-up-for-those-present"></a>Arquivos de código copiados (para aqueles presentes)
Cada um dos seguintes arquivos foi copiado antes de ser substituído. Arquivos de backup estão localizados em uma pasta “Backup” na raiz do diretório do projeto.

* `Startup.cs`
* `App_Start\Startup.Auth.cs`

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Se eu tiver marcado *Ler dados do diretório*, quais alterações adicionais foram feitas ao meu projeto?
### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Foram feitas alterações adicionais em sua app.config ou web.config
As outras entradas de configuração a seguir foram adicionadas.

```
    <appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

### <a name="your-azure-active-directory-app-was-updated"></a>Seu aplicativo Active Directory do Azure foi atualizado
Seu aplicativo Azure Active Directory foi atualizado para incluir a permissão *Ler dados do diretório* e uma chave adicional foi criada e, em seguida, utilizada como *ida:Password* no arquivo `web.config`.

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre o Active Directory do Azure](https://azure.microsoft.com/services/active-directory/)

