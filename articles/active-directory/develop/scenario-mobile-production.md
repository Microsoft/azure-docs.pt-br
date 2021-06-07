---
title: Preparar aplicativos móveis – chamando APIs da Web para produção | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo móvel que chama as APIs Web. (Preparar aplicativos para produção.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 08243fd06de289941d8e6a9197ccb349614af056
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675950"
---
# <a name="prepare-mobile-apps-for-production"></a>Preparar aplicativos móveis para produção

Este artigo fornece detalhes sobre como melhorar a qualidade e a confiabilidade do seu aplicativo móvel antes de movê-lo para produção.

## <a name="handle-errors"></a>Tratar erros

Ao preparar um aplicativo móvel para produção, várias condições de erro podem ocorrer. Os principais casos que você tratará são falhas silenciosas e fallbacks para a interação. Outras condições que você deve considerar incluem situações sem rede, interrupções de serviço, requisitos para o consentimento do administrador e outros casos específicos do cenário.

Para cada tipo de MSAL (biblioteca de autenticação da Microsoft), você pode encontrar o código de exemplo e o conteúdo do wiki que descrevem como lidar com condições de erro:

- [Wiki do MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Wiki do MSAL iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Próximas etapas

Para experimentar exemplos adicionais, consulte [aplicativos cliente públicos móveis e de desktop](sample-v2-code.md#desktop-and-mobile-public-client-apps).