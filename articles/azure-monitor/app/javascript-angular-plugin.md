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
ms.openlocfilehash: 152ba4b1c8a4e09db0bce759f5b67f577ec5d584
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843735"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Plug-in angular para Application Insights SDK do JavaScript

O plug-in angular para o SDK do Application Insights JavaScript permite:

- Acompanhamento de alterações do roteador
- Estatísticas de uso de componentes angulares

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
import { Component, OnInit } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin, AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
    private appInsights;
    constructor(
        private router: Router,
        private angularPluginService: AngularPluginService 
    ){
        var angularPlugin = new AngularPlugin();
        this.angularPluginService.init(angularPlugin, this.router);
        this.appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
    }

    ngOnInit() {
        this.appInsights.loadAppInsights();
    }
}

```

Para usar o `trackMetric` método para rastrear o uso de componentes angulares, adicione `AngularPluginService` como um provedor na lista de provedores no `app.module.ts` arquivo.

```js
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
    ...
  providers: [ AngularPluginService ],
})
export class AppModule { }
```

Para acompanhar o tempo de vida de um componente, chame `trackMetric` no `ngOnDestroy` método desse componente. Quando o componente é destruído, ele disparará um `trackMetric` evento que envia a hora em que o usuário permaneceu na página e o nome do componente.

```js
import { Component, OnDestroy, HostListener } from '@angular/core';
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnDestroy {

  constructor(private angularPluginService: AngularPluginService) {}

  @HostListener('window:beforeunload')
  ngOnDestroy() {
    this.angularPluginService.trackMetric();
  }
}
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o SDK do JavaScript, consulte a [documentação do SDK do Application insights JavaScript](javascript.md)
- [Plug-in angular no GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)