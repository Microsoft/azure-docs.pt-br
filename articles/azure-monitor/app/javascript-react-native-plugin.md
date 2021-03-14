---
title: Plug-in de reajam nativo para Application Insights SDK do JavaScript
description: Como instalar e usar o plug-in reajam nativo para Application Insights SDK do JavaScript.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 0c122a21fc7149e9943825cafbed77069b7919f3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593586"
---
# <a name="native-react-plugin-for-application-insights-javascript-sdk"></a>Plug-in de reajam nativo para Application Insights SDK do JavaScript

O plug-in reajam nativo para Application Insights SDK do JavaScript coleta informações do dispositivo, por padrão, esse plug-in coleta automaticamente:

- **ID de dispositivo exclusiva** (também conhecida como ID de instalação.)
- **Nome do modelo de dispositivo** (como iPhone X, Samsung Galaxy fold, Huawei p30 pro etc.)
- **Tipo de dispositivo** (por exemplo, monofone, Tablet, etc.)

## <a name="requirements"></a>Requisitos

Você deve estar usando uma versão >= 2.0.0 de `@microsoft/applicationinsights-web` . Este plug-in só funcionará em aplicativos reajam-nativos. Ele não funcionará com [aplicativos que usam a estrutura exposição](https://docs.expo.io/), portanto, ele não funcionará com CREATE Native app reajam.

## <a name="getting-started"></a>Introdução

Instale e vincule o pacote [reagir-Native-Device-info](https://www.npmjs.com/package/react-native-device-info) . Mantenha o `react-native-device-info` pacote atualizado para coletar os nomes de dispositivo mais recentes usando seu aplicativo.

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>Inicializando o plug-in

Para usar esse plug-in, você precisa construir o plug-in e adicioná-lo como um `extension` em sua instância existente do Application insights.

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o SDK do JavaScript, consulte a [documentação do SDK do Application insights JavaScript](javascript.md).
- Para saber mais sobre a linguagem de consulta Kusto e consultar dados em Log Analytics, consulte a [visão geral de consulta de log](../../azure-monitor/logs/log-query-overview.md).
