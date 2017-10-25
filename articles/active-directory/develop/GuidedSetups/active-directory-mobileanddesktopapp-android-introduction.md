---
title: "Introdução ao Android no Azure AD v2 – Introdução | Microsoft Docs"
description: Como um aplicativo do Android pode obter um token de acesso e chamar a API do Microsoft Graph ou APIs que exigem tokens de acesso por meio do ponto de extremidade do Azure Active Directory v2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: d933781713456f73aa76db557fdf35672dfb2a29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>Chamar a API do Microsoft Graph em um aplicativo do Android

Este guia demonstra como um aplicativo nativo do Android pode obter um token de acesso e chamar a API do Microsoft Graph ou outras APIs que exigem tokens de acesso por meio do ponto de extremidade do Azure Active Directory v2.

Ao final deste guia, seu aplicativo poderá chamar uma API protegida usando contas pessoais (incluindo outlook.com, live.com e outras), bem como contas corporativas ou de estudante de qualquer empresa ou organização que tem o Azure Active Directory.  

### <a name="how-this-sample-works"></a>Como funciona esta amostra
![Como funciona esta amostra](media/active-directory-mobileanddesktopapp-android-intro/android-intro.png)

A amostra criada por este guia se baseia em um cenário no qual um aplicativo do Android é usado para consultar uma API Web que aceita tokens do ponto de extremidade do Azure Active Directory v2 – nesse caso, a API do Microsoft Graph. Para esse cenário, um token é adicionado às solicitações HTTP por meio do cabeçalho de Autorização. A aquisição e a renovação de tokens são manipuladas pela MSAL (Biblioteca de Autenticação da Microsoft).

### <a name="pre-requisites"></a>Pré-requisitos
* Esta instalação guiada se concentra no Android Studio, mas qualquer outro ambiente de desenvolvimento de aplicativos do Android também é aceitável. 
* O SDK 21 ou mais novo do Android é necessário (o recomendado é o SDK 25).
* O Google Chrome ou um navegador da Web que usa Guias Personalizadas é necessário para esta versão da MSAL (Biblioteca de Autenticação da Microsoft) para Android.

> Observação: o Google Chrome não está incluído no Emulador do Visual Studio para Android. Recomendamos testar esse código em um Emulador com a API 25 ou uma imagem com a API 21 ou mais nova que tem o Google Chrome instalado.


### <a name="how-to-handle-token-acquisition-to-access-a-protected-web-api"></a>Como manipular a aquisição de tokens para acessar uma API Web protegida

Depois que o usuário é autenticado, o aplicativo de exemplo recebe um token que pode ser usado para consultar a API do Microsoft Graph ou uma API Web protegida pelo Microsoft Azure Active Directory v2.

APIs como o Microsoft Graph exigem um token de acesso para permitir o acesso a recursos específicos – por exemplo, ler um perfil de usuário, acessar o calendário do usuário ou enviar um email. O aplicativo pode solicitar um token de acesso que usa a MSAL para acessar esses recursos especificando escopos de API. Esse token de acesso é então adicionado ao cabeçalho de Autorização HTTP de cada chamada feita no recurso protegido. 

A MSAL gerencia o armazenamento em cache e a atualização de tokens de acesso para você, de forma que o aplicativo não precise fazer isso.

### <a name="libraries"></a>Bibliotecas

Este guia usa as seguintes bibliotecas:

|Biblioteca|Descrição|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|MSAL (Biblioteca de Autenticação da Microsoft)|
