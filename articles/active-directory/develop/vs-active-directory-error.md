---
title: Como diagnosticar erros com o serviço conectado do Azure Active Directory
description: O serviço conectado do Active Directory detectou um tipo de autenticação incompatível
services: active-directory
ms.service: active-directory
ms.subservice: develop
author: ghogen
manager: douge
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c54db2ab923a9de5f07f12e0b0202649ef76cb8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326096"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnosticando erros com o Serviço Conectado do Azure Active Directory

Ao detectar o código de autenticação anterior, o servidor conectado do Azure Active Directory detectou um tipo de autenticação incompatível.

Para detectar corretamente o código de autenticação anterior em um projeto, o projeto deve ser compilado.  Se você vir esse erro e não tiver um código de autenticação anterior em seu projeto, recompile e tente novamente.

## <a name="project-types"></a>Tipos de projeto

O serviço conectado verifica o tipo de projeto que está sendo desenvolvido para que possa injetar a lógica de autenticação adequada ao projeto. Se houver qualquer controlador que derive de `ApiController` no projeto, o projeto será considerado um projeto WebAPI. Se houver apenas controladores que derivam de `MVC.Controller` no projeto, o projeto será considerado um projeto MVC. O serviço conectado não oferece suporte a qualquer outro tipo de projeto.

## <a name="compatible-authentication-code"></a>Código de autenticação compatível

O serviço conectado também verifica configurações de autenticação configuradas anteriormente ou que são compatíveis com o serviço. Se todas as configurações estiverem presentes, será considerado um caso reentrante e o serviço conectado será aberto para exibir as configurações.  Se apenas algumas das configurações estiverem presentes, ele será considerado um caso de erro.

Em um projeto do MVC, o serviço conectado verifica qualquer uma das configurações a seguir, que são resultado do uso anterior do serviço:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Além disso, o serviço conectado verifica qualquer uma das seguintes configurações em um projeto de API da Web, que resulta do uso anterior do serviço:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Código de autenticação incompatível

Por fim, o serviço conectado tenta detectar versões do código de autenticação que foram configuradas com versões anteriores do Visual Studio. Se você recebeu esse erro, significa que seu projeto contém um tipo de autenticação incompatível. O serviço conectado detecta os seguintes tipos de autenticação de versões anteriores do Visual Studio:

* Autenticação do Windows
* Contas Individuais de Usuário
* Contas organizacionais

Para detectar a Autenticação do Windows em um projeto do MVC, o serviço conectado procura pelo `authentication` elemento em seu `web.config` arquivo.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Para detectar a Autenticação do Windows em um projeto de API da Web, o serviço conectado procura pelo `IISExpressWindowsAuthentication` elemento em seu `.csproj` arquivo do projeto:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Para detectar a autenticação de Contas de Usuário Individuais, o serviço conectado procura o elemento de pacote no seu `packages.config` arquivo.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Para detectar uma forma anterior de autenticação de Conta Organizacional, o serviço conectado procura o seguinte elemento em`web.config`:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Para alterar o tipo de autenticação, remova o tipo de autenticação incompatível e tente adicionar o serviço conectado novamente.

Para obter mais informações, consulte [Cenários de autenticação para o Azure AD](authentication-scenarios.md).
