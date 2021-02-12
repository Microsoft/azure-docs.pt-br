---
title: Mover aplicativo de página única para produção
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo de página única (mover para produção)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 9656da8be086724482f129efab323e02b73e117e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954946"
---
# <a name="single-page-application-move-to-production"></a>Aplicativo de página única: mover para produção

Agora que você sabe como adquirir um token para chamar APIs Web, aqui estão algumas coisas a serem consideradas ao mover seu aplicativo para produção.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>Implante seu aplicativo

Confira um [exemplo de implantação](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) para aprender a implantar seus projetos de API da Web e do spa com o armazenamento do Azure e os serviços de Azure app, respectivamente. 

## <a name="code-samples"></a>Exemplos de código

Esses exemplos de código demonstram várias operações importantes para um aplicativo de página única.
- [Spa com um back-end de ASP.net](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi): como obter tokens para sua própria API Web de back-end (ASP.NET Core) usando **MSAL.js**.

- [ API Web doNode.js (AD do Azure](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2): como validar tokens de acesso para sua API Web de back-end (Node.js) usando o **Passport-Azure-ad**.

- [Spa com Azure ad B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp): como usar **MSAL.js** para conectar usuários em um aplicativo que está registrado com **Azure Active Directory B2C** (Azure ad B2C).

- [ API Web doNode.js (Azure ad B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi): como usar o **Passport-Azure-ad** para validar tokens de acesso para aplicativos registrados com **Azure Active Directory B2C** (Azure ad B2C).

## <a name="next-steps"></a>Próximas etapas

- [Tutorial do spa do JavaScript](./tutorial-v2-javascript-spa.md): Aprofunde-se em como conectar usuários e obter um token de acesso para chamar a **API de Microsoft Graph** usando **MSAL.js**.