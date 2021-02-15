---
title: Compartilhar exibições personalizadas com URLs parametrizadas-Azure Time Series Insights | Microsoft Docs
description: Saiba como criar URLs parametrizadas para compartilhar facilmente exibições personalizadas do Explorer no Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 9bf857a66643b1e95ea2559601761a7217babad4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665320"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Compartilhar uma exibição personalizada usando uma URL parametrizada

Para compartilhar uma exibição personalizada no Azure Time Series Insights Explorer, você pode criar programaticamente uma URL com parâmetros da exibição personalizada.

O Azure Time Series Insights Explorer dá suporte a parâmetros de consulta de URL para especificar exibições na experiência diretamente da URL. Por exemplo, usando apenas a URL, você pode especificar um ambiente de destino, um predicado de pesquisa e um período de tempo desejado. Quando um usuário seleciona a URL personalizada, a interface fornece um link diretamente para esse ativo no portal de Azure Time Series Insights. Aplicam-se políticas de acesso a dados.

> [!TIP]
>
> * Veja a [demonstração de Azure Time Series insights](https://insights.timeseries.azure.com/samples)gratuita.
> * Leia a documentação do [Azure Time Series insights Explorer](./time-series-insights-explorer.md) que acompanha.

## <a name="environment-id"></a>ID do Ambiente

O parâmetro `environmentId=<guid>` especifica a ID do ambiente de destino. É um componente do FQDN de acesso a dados, e você pode encontrá-lo no canto superior direito da visão geral do ambiente na portal do Azure. É tudo o que precede `env.timeseries.azure.com` .

Um parâmetro de ID do ambiente de exemplo é `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Hora

Você pode especificar valores de tempo relativos ou absolutos com uma URL parametrizada.

### <a name="absolute-time-values"></a>Valores de tempo absolutos

Para valores de tempo absolutos, use os parâmetros `from=<integer>` e `to=<integer>`.

* `from=<integer>` é um valor em milissegundos de JavaScript da hora de início para o alcance de pesquisa.
* `to=<integer>` é um valor em milissegundos de JavaScript da hora de término para o alcance de pesquisa.

> [!TIP]
> Para traduzir datas facilmente em milissegundos de JavaScript, experimente a [época & o conversor de carimbo de data/hora UNIX](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Valores de tempo relativos

Para um valor de tempo relativo, use `relativeMillis=<value>` , em que o *valor* está em milissegundos de JavaScript do carimbo de data/hora mais recente recebido da API.

Por exemplo, `&relativeMillis=3600000` exibe os 60 minutos mais recentes dos dados.

Os valores aceitos correspondem ao menu de **tempo rápido** do Azure Time Series insights Explorer e incluem:

* `1800000` (Últimos 30 minutos)
* `3600000` (Último 60 min)
* `10800000` (Últimas 3 horas)
* `21600000` (Últimas 6 horas)
* `43200000` (Últimas 12 horas)
* `86400000` (Últimas 24 horas)
* `604800000` (Últimos 7 dias)
* `2592000000` (Últimas 30 horas)

### <a name="optional-parameters"></a>Parâmetros opcionais

O `timeSeriesDefinitions=<collection of term objects>` parâmetro especifica os termos de predicado que aparecerão em uma exibição de Azure Time Series insights:

| Parâmetro | Item de URL | Descrição |
| --- | --- | --- |
| **name** | `\<string>` | O nome do *termo*. |
| **splitBy** | `\<string>` | O nome da coluna para *dividido por*. |
| **measureName** | `\<string>` | O nome da coluna de *medida*. |
| **predicado** | `\<string>` | O cláusula *where* para filtragem do lado do servidor. |
| **Uso** | `true` | Um parâmetro opcional que especifica o uso da soma para sua medida. |

> [!NOTE]
> Se `Events` for a medida de **usos** selecionada, a contagem será selecionada por padrão.  
> Se `Events` não estiver selecionado, a média será selecionada por padrão. |

* O `multiChartStack=<true/false>` par chave-valor habilita o empilhamento no gráfico.
* O `multiChartSameScale=<true/false>` par chave-valor habilita a mesma escala do eixo Y entre os termos dentro de um parâmetro opcional.  
* O `timeBucketUnit=<Unit>&timeBucketSize=<integer>` permite que você ajuste o controle deslizante intervalo para fornecer uma exibição mais granular ou mais suave do gráfico.  
* O `timezoneOffset=<integer>` parâmetro permite que você defina o fuso horário do gráfico a ser exibido como um deslocamento para UTC.

| Par (es) | Descrição |
| --- | --- |
| `multiChartStack=false` | `true` é habilitado por padrão, portanto passe `false` para Stack. |
| `multiChartStack=false&multiChartSameScale=true` | O empilhamento deve estar habilitado para usar a mesma escala de eixo Y entre os termos.  `false`Por padrão, `true` a passagem habilita essa funcionalidade. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Unidades = `days` , `hours` , `minutes` , `seconds` , `milliseconds` .  Sempre colocar em maiuscula a unidade. </br> Defina o número de unidades passando o inteiro desejado para **timeBucketSize**.  |
| `timezoneOffset=-<integer>` | O inteiro é sempre em milissegundos. |

> [!NOTE]
> os valores de **timeBucketUnit** podem ser suavizados para até 7 dias.
> os valores de **timezoneOffset** não são UTC nem horário local.

### <a name="examples"></a>Exemplos

Para adicionar definições de série temporal a um ambiente de Azure Time Series Insights como um parâmetro de URL, acrescente:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Use as definições de série temporal de exemplo para:

* A ID do ambiente
* Os últimos 60 minutos de dados
* Os termos (**F1PressureID**, **F2TempStation**e **F3VibrationPL**) que compõem os parâmetros opcionais

Você pode construir a seguinte URL com parâmetros para uma exibição:

```URL
https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![URL com parâmetros do Azure Time Series Insights Explorer](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Consulte o Gerenciador ao vivo [usando o exemplo de URL](https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[%7B%22name%22:%22F1PressureId%22,%22splitBy%22:%22Id%22,%22measureName%22:%22Pressure%22,%22predicate%22:%22%27Factory1%27%22%7D,%7B%22name%22:%22F2TempStation%22,%22splitBy%22:%22Station%22,%22measureName%22:%22Temperature%22,%22predicate%22:%22%27Factory2%27%22%7D,%7B%22name%22:%22F3VibrationPL%22,%22splitBy%22:%22ProductionLine%22,%22measureName%22:%22Vibration%22,%22predicate%22:%22%27Factory3%27%22%7D]
) acima.

A URL acima descreve e exibe a exibição parametrizada do Azure Time Series Insights Explorer.

* Os predicados com parâmetros.

  [![Predicados com parâmetros Azure Time Series Insights Explorer.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* A exibição de gráfico completo compartilhada.

  [![A exibição de gráfico completo compartilhada.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [consultar dados usando C#](time-series-insights-query-data-csharp.md).

* Saiba mais sobre o [Azure Time Series insights Explorer](./time-series-insights-explorer.md).
