---
title: Plug-in de reagir para Application Insights SDK do JavaScript
description: Como instalar e usar o plug-in reajam para Application Insights SDK do JavaScript.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 4c6d8fabbd236a2653fff8168ad73c0b45f09d64
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98027835"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Plug-in de reagir para Application Insights SDK do JavaScript

O plug-in reajam para o SDK do Application Insights JavaScript permite:

- Acompanhamento de alterações de rota
- Estatísticas de uso de componentes reagir

## <a name="getting-started"></a>Introdução

Instale o pacote NPM:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Uso básico

Inicializar uma conexão com Application Insights:

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

Empacote o componente com a função de componente de ordem superior para habilitar a Application Insights nele:

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

// withAITracking takes 4 parameters ( reactPlugin, Component, ComponentName, className) 
// the first two are required and the other two are optional.

export default withAITracking(reactPlugin, MyComponent);
```

## <a name="configuration"></a>Configuração

| Nome    | Padrão | Descrição                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | nulo    | Reagir o histórico do roteador. Para obter mais informações, consulte a [documentação do pacote reagir-router](https://reactrouter.com/web/api/history). Para saber como acessar o objeto de histórico fora dos componentes, consulte as [perguntas frequentes sobre reagir-roteador](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>Acompanhamento de uso de componentes reajam

Para instrumentar vários rastreamentos de uso de componentes reajam, aplique a `withAITracking` função de componente de ordem superior.

Ele medirá o tempo do `ComponentDidMount` evento por meio do `ComponentWillUnmount` evento. No entanto, para tornar isso mais preciso, ele subtrairá a hora em que o usuário esteve ocioso `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Para ver essa métrica no portal do Azure você precisa navegar até o recurso de Application Insights, selecione a guia "métricas" e configure os gráficos vazios para exibir o nome da métrica personalizada "tempo envolvido do componente de reajam (segundos)", selecione agregação (Sum, AVG, etc.) de sua métrica e aplique dividir ser "nome do componente".

![Captura de tela do gráfico que exibe a métrica personalizada "reagir o componente de tempo envolvido (segundos)" dividir por "nome do componente"](./media/javascript-react-plugin/chart.png)

Você também pode executar consultas personalizadas para dividir Application Insights dados para gerar relatórios e visualizações de acordo com seus requisitos. Na portal do Azure navegue até o recurso de Application Insights, selecione "análise" no menu superior da guia Visão geral e execute a consulta.

![Captura de tela dos resultados da consulta de métrica personalizada.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Pode levar até 10 minutos para que novas métricas personalizadas apareçam no portal do Azure.

## <a name="using-react-hooks"></a>Usando ganchos reajam

Os [ganchos reajam](https://reactjs.org/docs/hooks-reference.html) são uma abordagem para o gerenciamento de estado e ciclo de vida em um aplicativo de reagir sem depender de componentes de reagir baseados em classe. O plug-in reajam Application Insights fornece uma série de integrações de ganchos que operam de forma semelhante à abordagem de componente de ordem superior.

### <a name="using-react-context"></a>Usando o contexto reagir

Os ganchos reajam para Application Insights são projetados para usar o [contexto de reagir](https://reactjs.org/docs/context.html) como um aspecto que o contém. Para usar o contexto, inicialize Application Insights como acima e, em seguida, importe o objeto de contexto:

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

Esse provedor de contexto fará Application Insights disponível como um `useContext` gancho dentro de todos os componentes filhos dele.

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    
    appInsights.trackMetric("Component 'MyComponent' is in use");
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

O `useTrackMetric` gancho Replica a funcionalidade do componente de `withAITracking` ordem superior, sem adicionar um componente adicional à estrutura do componente. O gancho usa dois argumentos, primeiro é a Application Insights instância (que pode ser obtida do `useAppInsightsContext` gancho) e um identificador para o componente para acompanhamento (como seu nome).

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

Ele funcionará como o componente de ordem superior, mas responderá a conectar eventos do ciclo de vida, em vez de um ciclo de vida do componente. O gancho precisa ser fornecido explicitamente a eventos de usuário se houver a necessidade de executar em interações específicas.

### `useTrackEvent`

O `useTrackEvent` gancho é usado para rastrear qualquer evento personalizado que um aplicativo possa precisar controlar, como um clique de botão ou outra chamada à API. Ele usa dois argumentos, o primeiro é a Application Insights instância (que pode ser obtida do `useAppInsightsContext` gancho) e um nome para o evento.

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const ProductCart = () => {
    const appInsights = useAppInsightsContext();
    const trackCheckout = useTrackEvent(appInsights, "Checkout");
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated");
    const [cart, setCart] = useState([]);
    
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}
export default MyComponent;
```

Quando o gancho é usado, uma carga de dados pode ser fornecida a ele para adicionar dados adicionais ao evento quando ele é armazenado em Application Insights.

## <a name="react-error-boundaries"></a>Reagir aos limites de erro

Os [limites de erro](https://reactjs.org/docs/error-boundaries.html) fornecem uma maneira de tratar normalmente uma exceção quando ocorre dentro de um aplicativo de reagir e, quando esse erro ocorre, é provável que a exceção precise ser registrada em log. O plug-in reagir para Application Insights fornece um componente de limite de erro que registrará automaticamente o erro quando ocorrer.

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

O `AppInsightsErrorBoundary` requer que dois props sejam passados para ele, a `ReactPlugin` instância criada para o aplicativo e um componente a ser renderizado quando ocorrer um erro. Quando ocorre um erro sem tratamento, `trackException` é chamado com as informações fornecidas ao limite de erro e o `onError` componente é exibido.

## <a name="sample-app"></a>Aplicativo de exemplo

Confira a [demonstração reagir Application insights](https://github.com/Azure-Samples/application-insights-react-demo).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o SDK do JavaScript, consulte a [documentação do SDK do Application insights JavaScript](javascript.md).
- Para saber mais sobre a linguagem de consulta Kusto e consultar dados em Log Analytics, consulte a [visão geral de consulta de log](../../azure-monitor/log-query/log-query-overview.md).
