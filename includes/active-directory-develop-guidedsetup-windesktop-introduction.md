---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 301abe95b245603e5414eef84ce74cdc8de01d19
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509808"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Chamar a API do Microsoft Graph em um aplicativo da Área de Trabalho do Windows

Este guia demonstra como um aplicativo .NET da Área de Trabalho do Windows (XAML) nativo usa um token de acesso para chamar a API do Microsoft Graph. O aplicativo também pode acessar outras APIs que exigem tokens de acesso de uma plataforma de identidade da Microsoft para o ponto de extremidade v2.0 do desenvolvedor. Essa plataforma era chamada de Azure AD.

Depois de concluir o guia, seu aplicativo poderá chamar uma API protegida que usa contas pessoais (incluindo o outlook.com, live.com e outros). O aplicativo também usará contas corporativas e de estudante de qualquer empresa ou organização que usa o Azure Active Directory.  

> [!NOTE]
> O guia exige o Visual Studio 2015 Atualização 3, Visual Studio 2017 ou o Visual Studio 2019. Não tem nenhuma dessas versões? [Baixe gratuitamente o Visual Studio 2019](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona

![Mostra como funciona o aplicativo de exemplo gerado por este tutorial](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

O aplicativo de exemplo que você cria com este guia habilita um aplicativo da Área de Trabalho do Windows que consulta a API do Microsoft Graph ou uma API Web que aceita tokens de um ponto de extremidade da plataforma de identidade da Microsoft. Para esse cenário, você adiciona um token às solicitações HTTP por meio do cabeçalho de Autorização. A MSAL (Biblioteca de Autenticação da Microsoft) lida com a aquisição e a renovação de tokens.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Manipulando a aquisição de token para acessar APIs Web protegidas

Após a autenticação do usuário, o aplicativo de exemplo receberá um token que pode ser usado para consultar a API do Microsoft Graph ou uma API Web protegida pela plataforma de identidade da Microsoft para desenvolvedores.

APIs, como o Microsoft Graph, exigem um token para permitir o acesso a recursos específicos. Por exemplo, um token é necessário para ler o perfil ou acessar o calendário de um usuário, ou enviar um email. O aplicativo pode solicitar um token de acesso usando a MSAL para acessar esses recursos especificando escopos de API. Esse token de acesso é então adicionado ao cabeçalho de Autorização HTTP de cada chamada feita no recurso protegido.

A MSAL gerencia o armazenamento em cache e a atualização de tokens de acesso para você, de forma que o aplicativo não precise fazer isso.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia usa os seguintes pacotes NuGet:

|Biblioteca|DESCRIÇÃO|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de Autenticação da Microsoft (MSAL.NET)|
