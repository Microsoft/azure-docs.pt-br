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
ms.openlocfilehash: 5cbb576a7fcfb2daf492a149130aa7c99fe10ac5
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753609"
---
# <a name="single-page-application-code-configuration"></a>Aplicativo de página única: configuração de código

Saiba como configurar o código para seu aplicativo de página única (SPA).

## <a name="msal-libraries-for-spas-and-supported-authentication-flows"></a>Bibliotecas de MSAL para SPAs e fluxos de autenticação com suporte

A plataforma de identidade da Microsoft fornece a seguinte biblioteca de autenticação da Microsoft para JavaScript (MSAL.js) para dar suporte ao fluxo implícito e ao fluxo de código de autorização com o PKCE usando práticas de segurança recomendadas do setor:

| Biblioteca MSAL | Fluxo | Descrição |
|--------------|------|-------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (2. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) | Fluxo de código de autorização (PKCE) | Biblioteca JavaScript simples para uso em qualquer aplicativo Web do lado do cliente criado por meio de estruturas JavaScript ou SPA, como angular, Vue.js e React.js. |
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (1. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) | Fluxo implícito | Biblioteca JavaScript simples para uso em qualquer aplicativo Web do lado do cliente criado por meio de estruturas JavaScript ou SPA, como angular, Vue.js e React.js. |
| ![MSAL angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Fluxo implícito | Wrapper da biblioteca de MSAL.js básica para simplificar o uso em aplicativos de página única que são criados por meio da estrutura angular. |

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
