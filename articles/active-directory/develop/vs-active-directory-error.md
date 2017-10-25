---
title: "Como diagnosticar erros com o Assistente de Conexão do Azure Active Directory"
description: "O Assistente de conexão do Active Directory detectou um tipo de autenticação incompatível"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/05/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 4f29f62b2996cae98b02c1ed5fcb59eca09301ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connection-wizard"></a>Diagnosticando erros com o Assistente de Conexão do Azure Active Directory
Ao detectar o código de autenticação anterior, o assistente detectou um tipo de autenticação incompatível.   

## <a name="what-is-being-checked"></a>O que está sendo verificado?
**Observação:** para detectar corretamente o código de autenticação anterior em um projeto, o projeto deve ser compilado.  Se este erro ocorrer e você não tiver o código de autenticação anterior em seu projeto, compile o projeto novamente e repita a operação.

### <a name="project-types"></a>Tipos de projeto
O assistente verifica o tipo de projeto que está sendo desenvolvido para que possa injetar a lógica de autenticação adequada ao projeto.  Se houver um controlador que deriva de `ApiController` no projeto, o projeto será considerado um projeto WebAPI.  Se houver apenas controladores que derivam de `MVC.Controller` no projeto, o projeto será considerado um projeto MVC.  Qualquer outro item não tem suporte do assistente.

### <a name="compatible-authentication-code"></a>Código de autenticação compatível
O assistente também verifica se as configurações de autenticação configuradas anteriormente com o assistente são compatíveis com ele.  Se todas as configurações estiverem presentes, ele será considerado um caso reentrante e o assistente será aberto e exibirá as configurações.  Se apenas algumas das configurações estiverem presentes, ele será considerado um caso de erro.

Em um projeto do MVC, o assistente verifica qualquer uma das configurações a seguir, fruto do uso anterior do assistente:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Além disso, o assistente verifica qualquer uma das configurações a seguir em um projeto de API da Web, fruto do uso anterior do assistente:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

### <a name="incompatible-authentication-code"></a>Código de autenticação incompatível
Por fim, o assistente tenta detectar versões do código de autenticação que foram configuradas com versões anteriores do Visual Studio. Se você recebeu esse erro, significa que seu projeto contém um tipo de autenticação incompatível. O assistente detecta os seguintes tipos de autenticação de versões anteriores do Visual Studio:

* Autenticação do Windows 
* Contas Individuais de Usuário 
* Contas organizacionais 

Para detectar a Autenticação do Windows em um projeto MVC, o assistente procura pelo elemento `authentication` de seu arquivo **web.config** .

<pre>
    &lt;configuration&gt;
        &lt;system.web&gt;
            <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Para detectar a Autenticação do Windows em um projeto da API da Web, o assistente procura pelo elemento `IISExpressWindowsAuthentication` do arquivo **.csproj** de seu projeto:

<pre>
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup>
    &lt;/Project&gt;
</pre>

Para detectar a autenticação de contas de usuário individual, o assistente procura o elemento de pacote de seu arquivo **Packages.config** .

<pre>
    &lt;packages&gt;
        <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
    &lt;/packages&gt;
</pre>

Para detectar uma forma anterior de Autenticação de conta organizacional, o assistente procura o seguinte elemento **web.config**:

<pre>
    &lt;configuration&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Para alterar o tipo de autenticação, remova o tipo de autenticação incompatível e execute o assistente novamente.

Para obter mais informações, consulte [Cenários de autenticação para o Azure AD](active-directory-authentication-scenarios.md).

#<a name="next-steps"></a>Próximas etapas
- [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md)