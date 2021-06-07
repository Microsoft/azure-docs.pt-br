---
title: Plug-in angular para Application Insights SDK do JavaScript
description: Como instalar e usar o plug-in angular para Application Insights SDK do JavaScript.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: d45d8bed328dc91dfeeabd6ce878074fa1218623
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737011"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Plug-in angular para Application Insights SDK do JavaScript

O plug-in angular para o SDK do Application Insights JavaScript permite:

- Acompanhamento de alterações do roteador

> [!WARNING]
> O plug-in angular não é compatível com ECMAScript 3 (ES3).

## <a name="getting-started"></a>Introdução

Instale o pacote NPM:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Uso básico

Configure uma instância do Application Insights no componente de entrada em seu aplicativo:

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o SDK do JavaScript, consulte a [documentação do SDK do Application insights JavaScript](javascript.md)
- [Plug-in angular no GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
