---
title: Compartilhar exibições personalizadas do Azure Time Series Insights com URLs parametrizadas | Microsoft Docs
description: Este artigo descreve como desenvolver URLs parametrizadas no Azure Time Series Insights para que uma exibição do cliente possa ser facilmente compartilhada.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: df60429a8b3d6fbdc504a7605d1502b4e084d386
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165312"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Compartilhar uma exibição personalizada usando uma URL parametrizada

Para compartilhar uma exibição personalizada no Explorer do Time Series Insights, você pode criar programaticamente uma URL parametrizada da exibição personalizada.

O Explorer do Time Series Insights dá suporte a parâmetros de consulta de URL para especificar os modos de exibição na experiência diretamente da URL. Por exemplo, usando apenas a URL, você pode especificar um ambiente de destino, um predicado de pesquisa e um período de tempo desejado. Quando um usuário seleciona o URL personalizado, a interface fornece um link diretamente para esse ativo no portal do Time Series Insights. Aplicam-se políticas de acesso a dados.

> [!TIP]
> * Exibir a versão gratuita [Time Series Insights de demonstração](https://insights.timeseries.azure.com/samples).
> * Ler que o acompanha [Explorer do Time Series Insights](./time-series-insights-explorer.md) documentação.

## <a name="environment-id"></a>ID do Ambiente

O parâmetro `environmentId=<guid>` especifica a ID do ambiente de destino. Ele é um componente do FQDN de acesso a dados, e você pode encontrá-lo no canto superior direito da visão geral do ambiente no portal do Azure. É tudo o que precede `env.timeseries.azure.com`.

Um parâmetro de ID do ambiente de exemplo é `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Hora

Você pode especificar valores de tempo relativos ou absolutos com uma URL parametrizada.

### <a name="absolute-time-values"></a>Valores de tempo absolutos

Para valores de tempo absolutos, use os parâmetros `from=<integer>` e `to=<integer>`.

* `from=<integer>` é um valor em milissegundos de JavaScript da hora de início para o alcance de pesquisa.
* `to=<integer>` é um valor em milissegundos de JavaScript da hora de término para o alcance de pesquisa.

Para identificar os milissegundos de JavaScript para uma data, veja [Conversor de carimbo de data/hora Epoch & Unix](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Valores de tempo relativos

Para um valor de tempo relativo, use `relativeMillis=<value>`, onde *value* está em milissegundos de JavaScript dos dados mais recentes no back-end.

Por exemplo, `&relativeMillis=3600000` exibe os 60 minutos mais recentes dos dados.

Valores aceitos correspondem ao Gerenciador de Time Series Insights **tempo rápido** menu e incluem:

* `1800000` (Últimos 30 min.)
* `3600000` (Últimos 60 minutos)
* `10800000` (Últimas 3 horas)
* `21600000` (Últimas 6 horas)
* `43200000` (Últimas 12 horas)
* `86400000` (Últimas 24 horas)
* `604800000` (Últimos 7 dias)
* `2592000000` (Últimas 30 horas)

### <a name="optional-parameters"></a>Parâmetros opcionais

O `timeSeriesDefinitions=<collection of term objects>` parâmetro especifica os termos de um modo de exibição do Time Series Insights:

| Parâmetro | Item de URL | DESCRIÇÃO |
| --- | --- | --- |
| **name** | `\<string>` | O nome do *termo*. |
| **splitBy** | `\<string>` | O nome da coluna para *dividido por*. |
| **measureName** | `\<string>` | O nome da coluna de *medida*. |
| **predicate** | `\<string>` | O cláusula *where* para filtragem do lado do servidor. |
| **useSum** | `true` | Um parâmetro opcional que especifica o uso de soma para a medida. </br>  Observe que, se `Events` é a medida selecionada, a contagem será selecionada por padrão.  </br>  Se `Events` não é selecionada, a média será selecionada por padrão. |

* O `multiChartStack=<true/false>` par chave-valor permite o empilhamento no gráfico.
* O `multiChartSameScale=<true/false>` par chave-valor permite que a mesma escala de eixo y entre os termos de um parâmetro opcional.  
* O `timeBucketUnit=<Unit>&timeBucketSize=<integer>` permite que você ajuste o controle deslizante de intervalo para fornecer mais granulares ou suave, agregada mais a exibição do gráfico.  
* O `timezoneOffset=<integer>` parâmetro permite que você defina o fuso horário para o gráfico seja exibido em como um deslocamento em UTC.

| Par (es) | DESCRIÇÃO |
| --- | --- |
| `multiChartStack=false` | `true` é habilitado por padrão, portanto passe `false` empilhar. |
| `multiChartStack=false&multiChartSameScale=true` | O empilhamento deve estar habilitado para usar a mesma escala de eixo Y entre os termos.  Ele tem `false` por padrão, então passar 'true' permite que essa funcionalidade. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Unidades = dias, horas, minutos, segundos e milissegundos.  Sempre colocar em maiuscula a unidade. </br> Defina o número de unidades passando o inteiro desejado ao timeBucketSize.  Observe que é possível suavizar até sete dias.  |
| `timezoneOffset=-<integer>` | O inteiro é sempre em milissegundos. </br> Observe que essa funcionalidade é ligeiramente diferente da habilitada no Gerenciador de Time Series Insights, onde permitimos que você escolha o local (horário do navegador) ou UTC. |

### <a name="examples"></a>Exemplos

Para adicionar as definições de série de tempo em um ambiente do Time Series Insights como um parâmetro de URL, acrescente:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Use as definições de série de tempo de exemplo para:

* A ID do ambiente
* Últimos 60 minutos de dados
* Os termos (F1PressureID, F2TempStation e F3VibrationPL) que incluem os parâmetros opcionais

Você pode construir a URL a seguir com parâmetros para um modo de exibição:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Consulte o Gerenciador de live [usando a URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

A URL acima descreve e cria a exibição do Explorer do Time Series Insights:

[![Termos do Explorador de análise de séries de tempo](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

A exibição completa (incluindo o gráfico):

[![Modo de exibição de gráfico](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [consulta de dados usando C# ](time-series-insights-query-data-csharp.md).

* Saiba mais sobre o [tempo Series Insights Explorer](./time-series-insights-explorer.md).
