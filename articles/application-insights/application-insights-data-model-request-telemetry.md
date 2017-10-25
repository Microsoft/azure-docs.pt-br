---
title: "Modelo de dados do Azure Application Insights Telemetry – telemetria de solicitações | Microsoft Docs"
description: "Modelo de dados do Application Insights para telemetria de solicitações"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: bwren
ms.openlocfilehash: 8e782e45b706cadec66e7404dd9abc2e01dea917
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetria de solicitações: modelo de dados do Application Insights

Um item de telemetria de solicitação (em [Application Insights](app-insights-overview.md)) representa a sequência lógica de execução acionada por uma solicitação externa a seu aplicativo. Toda execução de solicitação é identificada por um `ID` e `url` exclusivos que contêm todos os parâmetros de execução. Você pode agrupar solicitações por `name` lógico e definir a `source` dessa solicitação. A execução de código pode resultar em `success` ou `fail` e tem um determinado `duration`. Execuções com êxito e falha podem ser agrupadas ainda mais pelo `resultCode`. Hora de início para a telemetria de solicitação definida no nível de envelope.

A solicitação de telemetria dá suporte ao modelo de extensibilidade padrão usando `properties` e `measurements` personalizadas.

## <a name="name"></a>Nome

O nome da solicitação representa o caminho de código necessário para processar a solicitação. Valor de baixa cardinalidade para permitir melhor agrupamento de solicitações. Para solicitações HTTP, ele representa o método HTTP e o modelo do caminho de URL como `GET /values/{id}` sem um valor real de `id`.

O SDK Web do Application Insights envia o nome de solicitação "como está" em relação a diferenciar maiúsculas e minúsculas. O agrupamento na interface do usuário diferencia maiúsculas de minúsculas, então `GET /Home/Index` é contado separadamente de `GET /home/INDEX`, embora muitas vezes eles resultem na mesma execução de ação e de controlador. O motivo é que as URLs em geral [diferenciam maiúsculas e minúsculas](http://www.w3.org/TR/WD-html40-970708/htmlweb.html). Talvez você queira ver se todos os `404` aconteceram para as URLs digitadas em maiúsculas. Você pode ler mais sobre a coleção de nomes de solicitação de SDK Web do ASP.NET na [postagem de blog](http://apmtips.com/blog/2015/02/23/request-name-and-url/).

Comprimento máximo: 1.024 caracteres

## <a name="id"></a>ID

Identificador de uma instância de chamada de solicitação. Usada para a correlação entre a solicitação e outros itens de telemetria. A ID deve ser globalmente exclusiva. Para obter mais informações, consulte a página de [correlação](application-insights-correlation.md).

Comprimento máximo: 128 caracteres

## <a name="url"></a>Url

URL de solicitação com todos os parâmetros de cadeia de consulta.

Comprimento máximo: 2.048 caracteres

## <a name="source"></a>Fonte

A origem da solicitação. Os exemplos são a chave de instrumentação do chamador ou o endereço IP do chamador. Para obter mais informações, consulte a página de [correlação](application-insights-correlation.md).

Comprimento máximo: 1.024 caracteres

## <a name="duration"></a>Duração

Duração da solicitação no formato: `DD.HH:MM:SS.MMMMMM`. Deve ser positivo e menor que `1000` dias. Esse campo é obrigatório, já que a telemetria de solicitação representa a operação com o início e fim.

## <a name="response-code"></a>Código de resposta

Resultado de uma execução de solicitação. Código de status HTTP para solicitações HTTP. Pode ser um valor `HRESULT` ou um tipo de exceção para outros tipos de solicitação.

Comprimento máximo: 1.024 caracteres

## <a name="success"></a>Sucesso

Indicação de chamada bem-sucedida ou malsucedida. Esse campo é obrigatório. Quando não definido explicitamente como `false`, a solicitação é considerada bem-sucedida. Defina esse valor como `false` se a operação for interrompida por exceção ou tiver retornado um código de resultado do erro.

Para aplicativos Web, o Application Insights define a solicitação como com falha quando o código de resposta é menor que `400` ou igual a `401`. No entanto, há casos em que esse mapeamento padrão não coincide com a semântica do aplicativo. O código de resposta `404` não pode indicar "nenhum registro", o que pode ser parte do fluxo regular. Ele também pode indicar um link desfeito. Para os links desfeitos, você pode até mesmo implementar lógica mais avançada. Você pode marcar links desfeitos como falhas somente quando esses links estiverem localizados no mesmo site, analisando o referenciador da URL. Ou então, marcá-los como falhas quando acessados por aplicativos móveis da empresa. Da mesma forma, `301` e `302` indicam falha quando acessados do cliente que não dá suporte a redirecionamento.

Conteúdo `206` parcialmente aceito pode indicar uma falha de uma solicitação geral. Por exemplo, o ponto de extremidade do Application Insights recebe um lote de itens de telemetria como uma única solicitação. Ele retorna `206` quando alguns itens no lote não foram processados com êxito. A taxa crescente de `206` indica um problema que precisa ser investigado. Uma lógica semelhante se aplica ao Status Múltiplo `207`, em que o êxito pode ser o pior dos códigos de resposta separados.

Você pode ler mais sobre código de status e código de resultados de solicitação na [postagem de blog](http://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Próximas etapas

- [Escrever uma telemetria de solicitação personalizada](app-insights-api-custom-events-metrics.md#trackrequest)
- Consulte [modelo de dados](application-insights-data-model.md) para modelo de dados e tipos do Application Insights.
- Saiba como [configurar o aplicativo ASP.NET Core](app-insights-asp-net.md) com o Application Insights.
- Confira as [plataformas](app-insights-platforms.md) com suporte do Application Insights.
