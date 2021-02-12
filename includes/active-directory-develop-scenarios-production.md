---
title: incluir arquivo
description: incluir arquivo
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 75650d7ff0ac647aeb6dace76c270680b1b89347
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98954945"
---
## <a name="enable-logging"></a>Habilitar o registro em log

Para ajudar em cenários de solução de problemas de falha de depuração e autenticação, a biblioteca de autenticação da Microsoft fornece suporte de log interno. O registro em log é cada biblioteca abordada nos seguintes artigos:

:::row:::
    :::column:::
        - [Registro em log no MSAL.NET](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [Registrar em log em MSAL para Android](../articles/active-directory/develop/msal-logging-android.md)
        - [Como fazer registro em log no MSAL.js](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [Registrar em log em MSAL para iOS/macOS](../articles/active-directory/develop/msal-logging-ios.md)
        - [Registrar em log em MSAL para Java](../articles/active-directory/develop/msal-logging-java.md)
        - [Registrar em log em MSAL para Python](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

Aqui estão algumas sugestões para a coleta de dados:

- Os usuários podem pedir ajuda quando tiverem problemas. Uma prática recomendada é capturar e armazenar logs temporariamente. Forneça um local onde os usuários possam carregar os logs. O MSAL fornece extensões de log para capturar informações detalhadas sobre a autenticação.

- Se a telemetria estiver disponível, habilite-a por meio do MSAL para coletar dados sobre como os usuários entram no seu aplicativo.


## <a name="validate-your-integration"></a>Validar sua integração

Teste sua integração seguindo a [lista de verificação de integração da plataforma de identidade da Microsoft](../articles/active-directory/develop/identity-platform-integration-checklist.md).
