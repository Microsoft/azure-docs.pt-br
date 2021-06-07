---
title: Configurar aplicativo de página única | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo de página única (configuração de código do aplicativo)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: fe73832ec5eaee62a2dc2d397c12f82334e2efd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010688"
---
# <a name="single-page-application-code-configuration"></a>Aplicativo de página única: configuração de código

Saiba como configurar o código para seu aplicativo de página única (SPA).

## <a name="microsoft-libraries-supporting-single-page-apps"></a>Bibliotecas da Microsoft que dão suporte a aplicativos de página única 

As seguintes bibliotecas da Microsoft dão suporte a aplicativos de página única:

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="application-code-configuration"></a>Configuração de código do aplicativo

Em uma biblioteca MSAL, as informações de registro do aplicativo são passadas como configuração durante a inicialização da biblioteca.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Para obter mais informações sobre as opções configuráveis, consulte [inicializando aplicativo com MSAL.js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [entrar e](scenario-spa-sign-in.md)sair...
