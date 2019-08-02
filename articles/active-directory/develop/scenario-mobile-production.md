---
title: Aplicativo móvel que chama APIs da Web (mover para produção)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo móvel que chama APIs da Web (mover para produção)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67788dd9257a0a4685313dce44c6a3dfb5e514df
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413614"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Aplicativo móvel que chama APIs da Web – mover para produção

Este artigo fornece detalhes sobre como melhorar a qualidade e a confiabilidade do seu aplicativo antes de movê-lo para produção.

## <a name="handling-errors-in-mobile-applications"></a>Tratamento de erros em aplicativos móveis

Várias condições de erro podem ocorrer em seu aplicativo neste ponto. Os principais cenários a serem tratados são falhas silenciosas e fallbacks para a interação. Outras condições que você deve considerar para produção incluem situações sem rede, interrupções de serviço, requisitos de consentimento do administrador e outros casos específicos do cenário.

Cada biblioteca MSAL possui código de exemplo e conteúdo wiki que descreve como lidar com essas condições:

- [Wiki do MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Wiki do MSAL iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Mitigar e investigar problemas

Para diagnosticar problemas em seu aplicativo, ele ajuda a coletar dados. Para obter informações sobre os tipos de dados que você pode coletar, consulte os wikis da plataforma MSAL.

- Os usuários podem pedir ajuda quando encontrarem problemas. Uma prática recomendada é capturar e armazenar logs temporariamente e fornecer um local onde os usuários possam carregá-los. O MSAL fornece extensões de log para capturar informações detalhadas sobre a autenticação.
- Se estiver disponível, habilite a telemetria por meio do MSAL para coletar dados sobre como os usuários estão entrando em seu aplicativo.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

Experimente amostras adicionais disponíveis em [exemplos | Aplicativos cliente públicos de desktop e móveis](sample-v2-code.md#desktop-and-mobile-public-client-apps)
