---
title: Mover API Web chamando APIs da Web para produção | Azure
titleSuffix: Microsoft identity platform
description: Saiba como mover uma API Web que chama APIs da Web para produção.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0ab5baef925b7c8589dd7852b6ff8058d67ba745
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675865"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Uma API Web que chama APIs da Web: mover para produção

Depois de adquirir um token para chamar APIs da Web, aqui estão algumas coisas a serem consideradas ao mover seu aplicativo para produção.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você conhece as noções básicas de como chamar APIs Web de sua própria API Web, você pode estar interessado no tutorial a seguir, que descreve o código usado para criar uma API Web protegida que chama APIs da Web.

| Amostra | Plataforma | Descrição |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-webAPI-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) capítulo 1 | API Web do ASP.NET Core, área de trabalho (WPF) | ASP.NET Core chamadas de API Web Microsoft Graph, que você chama de um aplicativo WPF usando a plataforma de identidade da Microsoft. |
