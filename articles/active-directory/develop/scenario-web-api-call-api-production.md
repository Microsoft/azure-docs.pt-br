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
ms.openlocfilehash: 370bedf04dc61e2a637f735580cd4df14061264a
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753339"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Uma API Web que chama APIs da Web: mover para produção

Depois de adquirir um token para chamar APIs Web, você pode mover seu aplicativo para produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Saiba mais

Agora que você conhece as noções básicas de como chamar APIs Web de sua própria API Web, você pode estar interessado no tutorial a seguir, que descreve o código usado para criar uma API Web protegida que chama APIs da Web.

| Amostra | Plataforma | Descrição |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-webAPI-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) capítulo 1 | API Web do ASP.NET Core, área de trabalho (WPF) | ASP.NET Core chamadas de API Web Microsoft Graph, que você chama de um aplicativo WPF usando a plataforma de identidade da Microsoft. |
