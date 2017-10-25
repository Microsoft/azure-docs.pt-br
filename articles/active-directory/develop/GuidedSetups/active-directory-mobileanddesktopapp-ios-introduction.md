---
title: "Introdução ao iOS no Azure AD v2 – Introdução | Microsoft Docs"
description: Como aplicativos iOS (Swift) podem chamar uma API que exige tokens de acesso pelo ponto de extremidade do Azure Active Directory v2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 948693c8501ecc46a1508e5ea085846d0910783e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-an-ios-app"></a>Chamar a API do Microsoft Graph em um aplicativo iOS

Este guia demonstra como um aplicativo iOS nativo (Swift) pode obter um token de acesso e chamar a API do Microsoft Graph ou outras APIs que exigem tokens de acesso por meio do ponto de extremidade do Azure Active Directory v2.

Ao final deste guia, seu aplicativo poderá chamar uma API protegida usando contas pessoais (incluindo outlook.com, live.com e outras), bem como contas corporativas ou de estudante de qualquer empresa ou organização que tem o Azure Active Directory.

> ### <a name="pre-requisites"></a>Pré-requisitos
> - O XCode 8.x é necessário para este guia. Você pode baixar o XCode [aqui](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL de download do XCode").
> - [Carthage](https://github.com/Carthage/Carthage) para gerenciamento de pacotes

### <a name="how-this-guide-works"></a>Como funciona este guia

![Como funciona este guia](media/active-directory-mobileanddesktopapp-ios-introduction/iosintro.png)

O aplicativo de exemplo criado por este guia permite que um aplicativo iOS consulte a API do Microsoft Graph ou uma API Web que aceita tokens do ponto de extremidade do Azure Active Directory v2. Para esse cenário, um token é adicionado às solicitações HTTP por meio do cabeçalho de Autorização. A aquisição e a renovação de tokens são manipuladas pela MSAL (Biblioteca de Autenticação da Microsoft).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Manipulando a aquisição de token para acessar APIs Web protegidas

Depois que o usuário é autenticado, o aplicativo de exemplo recebe um token que pode ser usado para consultar a API do Microsoft Graph ou uma API Web protegida pelo Microsoft Azure Active Directory v2.

APIs como o Microsoft Graph exigem um token de acesso para permitir o acesso a recursos específicos – por exemplo, ler um perfil de usuário, acessar o calendário do usuário ou enviar um email. O aplicativo pode solicitar um token de acesso que usa a MSAL especificando escopos de API. Esse token de acesso é então adicionado ao cabeçalho de Autorização HTTP de cada chamada feita no recurso protegido.

A MSAL gerencia o armazenamento em cache e a atualização de tokens de acesso para você, de forma que o aplicativo não precise fazer isso.


### <a name="nuget-packages"></a>Pacotes NuGet

Este guia usa os seguintes pacotes NuGet:

|Biblioteca|Descrição|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Versão prévia da Biblioteca de Autenticação da Microsoft para iOS|

