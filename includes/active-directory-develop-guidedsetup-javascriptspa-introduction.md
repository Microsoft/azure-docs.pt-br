---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8c2dc41fde9387da291b6e4a6c8a6220ae62b514
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172527"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Conectar usuários e chamar a API do Microsoft Graph de um SPA (aplicativo de página única) JavaScript

Este guia demonstra como um SPA (aplicativo de página única) JavaScript pode conectar contas pessoais, corporativas e de estudante, obter um token de acesso e chamar a API do Microsoft Graph ou outras APIs que exigem tokens de acesso do ponto de extremidade da plataforma de identidade da Microsoft.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona

![Mostra como funciona o aplicativo de exemplo gerado por este tutorial](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Mais informações

O aplicativo de exemplo criado por este guia permite que um SPA JavaScript consulte a API do Microsoft Graph ou uma API Web que aceita tokens do ponto de extremidade da plataforma de identidade da Microsoft. Para esse cenário, depois que um usuário se conecta, um token de acesso é adicionado às solicitações HTTP por meio do cabeçalho de autorização. A aquisição e a renovação de tokens são manipuladas pela MSAL (Biblioteca de Autenticação da Microsoft).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotecas

Este guia usa a seguinte biblioteca:

|Biblioteca|DESCRIÇÃO|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Versão prévia da Biblioteca de Autenticação da Microsoft para JavaScript|

> [!NOTE]
> *msal.js* tem como destino o *ponto de extremidade da plataforma de identidade da Microsoft*, que permite que contas pessoais, corporativas e de estudante entrem e adquiram tokens. O *ponto de extremidade da plataforma de identidade da Microsoft* tem [algumas limitações](../articles/active-directory/develop/active-directory-v2-limitations.md).
> Para entender as diferenças entre os pontos de extremidade v1.0 e v2.0 leia a [guia de comparação de ponto de extremidade](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
