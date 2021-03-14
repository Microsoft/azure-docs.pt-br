---
title: Consultando dados-Azure Time Series Insights Gen2 | Microsoft Docs
description: Visão geral dos conceitos de consulta de dados e da API REST no Azure Time Series Insights Gen2.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: b1b055fa7f083bd8bccda16498e2894d5d67eace
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374126"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>Consultando dados de Azure Time Series Insights Gen2

Azure Time Series Insights Gen2 permite consultar dados em eventos e metadados armazenados no ambiente por meio de APIs de superfície pública. Essas APIs também são usadas pelo [Azure Time Series insights Explorer TSI](./concepts-ux-panels.md).

Três categorias de API principais estão disponíveis no Azure Time Series Insights Gen2:

* **APIs de ambiente**: essas APIs habilitam consultas no próprio ambiente de Azure Time Series insights Gen2. Eles podem ser usados para reunir a lista de ambientes aos quais o chamador tem acesso e os metadados do ambiente.
* **APIs do Model-Query série temporal (TSM-Q)**: habilita operações de criação, leitura, atualização e exclusão (CRUD) em metadados armazenados no modelo de série temporal do ambiente. Eles podem ser usados para acessar e editar instâncias, tipos e hierarquias.
* **APIs de consulta de série temporal (TSQ)**: habilita a recuperação de dados de telemetria ou eventos conforme eles são registrados do provedor de origem e permite cálculos e agregações de alto desempenho nos dados usando funções escalares e agregadas avançadas.

Azure Time Series Insights Gen2 usa uma linguagem de expressão baseada em cadeia de caracteres avançada, [TSX (expressão de série temporal)](/rest/api/time-series-insights/reference-time-series-expression-syntax), para expressar cálculos em [variáveis de série temporal](./concepts-variables.md).

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Visão geral das APIs do Azure Time Series Insights Gen2

Há suporte para as APIs principais a seguir.

[![Visão geral de consulta de série temporal](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>APIs de ambiente

* [Obter a API de ambientes](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment): retorna a lista de ambientes aos quais o chamador está autorizado a acessar.
* [Obter API de disponibilidade de ambientes](/rest/api/time-series-insights/dataaccessgen2/query/getavailability): retorna a distribuição da contagem de eventos sobre o carimbo de data/hora do evento `$ts` . Essa API ajuda a determinar se há algum evento no ambiente, retornando a contagem de eventos divididos em intervalos de tempo, se existir algum.
* [Obter API do esquema de evento](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema): retorna os metadados do esquema de evento para um determinado intervalo de pesquisa. Essa API ajuda a recuperar todos os metadados e as propriedades disponíveis no esquema para determinado período pesquisado.

## <a name="time-series-model-query-tsm-q-apis"></a>APIs TSM-Q (Consulta do modelo do Time Series)

A maioria dessas APIs dá suporte à operação de execução em lote para habilitar operações CRUD de lote em várias entidades de modelo de série temporal:

* [API de configurações de modelo](/rest/api/time-series-insights/reference-model-apis): habilita *Get* e *patch* no tipo padrão e o nome do modelo do ambiente.
* [API de tipos](/rest/api/time-series-insights/reference-model-apis#types-api): habilita CRUD em tipos de série temporal e suas variáveis associadas.
* [API de hierarquias](/rest/api/time-series-insights/reference-model-apis#hierarchies-api): habilita CRUD em hierarquias de série temporal e seus caminhos de campo associados.
* [API de instâncias](/rest/api/time-series-insights/reference-model-apis#instances-api): habilita CRUD em instâncias de série temporal e seus campos de instância associados. Além disso, a API de instâncias do oferece suporte às seguintes operações:
  * [Pesquisa](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search): recupera uma lista parcial de ocorrências na pesquisa de instâncias de série temporal com base em atributos de instância.
  * [Sugerir](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest): pesquisa e sugere uma lista parcial de ocorrências na pesquisa de instâncias de série temporal com base em atributos de instância.

## <a name="time-series-query-tsq-apis"></a>APIs TSQ (consulta de série temporal)

Essas APIs estão disponíveis em ambas as lojas (passivas e frias) em nossa solução de armazenamento em várias camadas. 

* [API de obtenção de eventos](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents): habilita a consulta e a recuperação de eventos brutos e os carimbos de data/hora do evento associado, pois eles são registrados em Azure Time Series insights Gen2 do provedor de origem. Essa API permite a recuperação de eventos brutos para uma determinada ID de série temporal e intervalo de pesquisa. Essa API dá suporte à paginação para recuperar o conjunto de dados de resposta completo para a entrada selecionada.

  > [!IMPORTANT]
  > Como parte das [alterações futuras nas regras de saída e mesclagem JSON](./ingestion-rules-update.md), as matrizes serão armazenadas como um tipo **dinâmico** . As propriedades de carga armazenadas como esse tipo **só podem ser acessadas por meio da API Get Events**.

* [Obter a API da série](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries): habilita a consulta e a recuperação de valores computados e os carimbos de data/hora do evento associado aplicando cálculos definidos por variáveis em eventos brutos. Essas variáveis podem ser definidas no modelo de série temporal ou fornecidas embutidas na consulta. Essa API dá suporte à paginação para recuperar o conjunto de dados de resposta completo para a entrada selecionada.

* [API da série de agregação](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries): habilita a consulta e a recuperação de valores agregados e os carimbos de data/hora do intervalo associado aplicando cálculos definidos por variáveis em eventos brutos. Essas variáveis podem ser definidas no modelo de série temporal ou fornecidas embutidas na consulta. Essa API dá suporte à paginação para recuperar o conjunto de dados de resposta completo para a entrada selecionada.
  
  Para um intervalo de pesquisa e intervalos especificados, essa API retorna uma resposta agregada por intervalo por variável para uma ID de série temporal. O número de intervalos no conjunto de dado de resposta é calculado por meio da contagem de tiques de época (o número de milissegundos decorridos desde a época do UNIX – 1º de janeiro de 1970) e a divisão dos tiques pelo tamanho de span de intervalo especificado na consulta.

  Os carimbos de data/hora retornados no conjunto de respostas são dos limites do intervalo esquerdo, não dos eventos de amostra do intervalo.


### <a name="selecting-store-type"></a>Selecionando o tipo de repositório

As APIs acima só podem ser executadas em um dos dois tipos de armazenamento (frio ou quente) em uma única chamada. Os parâmetros de URL de consulta são usados para especificar o [tipo de repositório](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters) no qual a consulta deve ser executada. 

Se nenhum parâmetro for especificado, a consulta será executada no Cold Store, por padrão. Se uma consulta abranger um intervalo de tempo sobrepondo uma loja fria e passiva, é recomendável rotear a consulta para o armazenamento frio para obter a melhor experiência, uma vez que a loja a quente conterá apenas dados parciais. 

O [Azure Time Series insights Explorer](./concepts-ux-panels.md) e o [conector do Power bi](./how-to-connect-power-bi.md) fazem chamadas para as APIs acima e selecionarão automaticamente o parâmetro storetype correto, quando relevante. 


## <a name="next-steps"></a>Próximas etapas

* Leia mais sobre variáveis diferentes que podem ser definidas no [modelo de série temporal](./concepts-model-overview.md).
* Leia mais sobre como consultar dados do [Azure Time Series insights Explorer](./concepts-ux-panels.md).
