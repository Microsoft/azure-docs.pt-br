---
title: Splunk para a consulta de log do Azure Monitor | Microsoft Docs
description: Ajuda para usuários que estão familiarizados com o Splunk para aprender sobre as consultas de log do Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: c59b5646e011afa6b8487e8145a1cb07e6e2a8ff
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015571"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk para a consulta de log do Azure Monitor

Este artigo destina-se a ajudar os usuários que estão familiarizados com o Splunk para aprender a linguagem de consulta do Kusto para escrever consultas de log no Azure Monitor. Comparações diretas são feitas entre os dois para compreender as principais diferenças e também as semelhanças em que você pode aproveitar seu conhecimento existente.

## <a name="structure-and-concepts"></a>Estrutura e conceitos

A tabela a seguir compara os conceitos e estruturas de dados entre logs do Splunk e os do Azure Monitor.

 | Conceito  | Splunk | Azure Monitor |  Comentário
 | --- | --- | --- | ---
 | Unidade de implantação  | cluster |  cluster |  O Azure Monitor permite consultas arbitrárias entre clusters. O Splunk, não. |
 | Caches de dados |  buckets  |  Políticas de retenção e armazenamento em cache |  Controla o período e o nível de armazenamento em cache dos dados. Essa configuração afeta diretamente o desempenho das consultas e o custo da implantação. |
 | Partição lógica dos dados  |  índice  |  Banco de Dados  |  Permite a separação lógica dos dados. Ambas as implementações permitem uniões e junções entre essas partições. |
 | Metadados de eventos estruturados | N/D | table |  O Splunk não tem o conceito exposto à linguagem de pesquisa de metadados de evento. Os logs do Azure Monitor têm o conceito de uma tabela, que tem colunas. Cada instância de evento é mapeada para uma linha. |
 | Registro de dados | event | linha |  Mudança de terminologia apenas. |
 | Atributo de registro de dados | field |  coluna |  No Azure Monitor, isso é predefinido como parte da estrutura de tabela. No Splunk, cada evento tem seu próprio conjunto de campos. |
 | Tipos | tipo de dados |  tipo de dados |  Os tipos de dados do Azure Monitor são mais explícitos, visto que são definidos nas colunas. Ambos têm a capacidade de trabalhar dinamicamente com os tipos de dados e o conjunto praticamente equivalente de tipos de dados, incluindo suporte a JSON. |
 | Consulta e pesquisa  | pequisa | Consulta |  Os conceitos são essencialmente os mesmos entre o Azure Monitor e o Splunk. |
 | Hora da ingestão de evento | Hora do sistema | ingestion_time() |  No Splunk, cada evento obtém um carimbo de data/hora do sistema do momento em que o evento foi indexado. No Azure Monitor, você pode definir uma política chamada ingestion_time que expõe uma coluna do sistema que pode ser referenciada por meio da função ingestion_time(). |

## <a name="functions"></a>Funções

A tabela a seguir especifica as funções no Azure Monitor que são equivalentes às funções do Splunk.

|Splunk | Azure Monitor |Comentário
|---|---|---
|strcat | strcat()| (1) |
|divisão  | split() | (1) |
|if     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|upper<br>lower |toupper()<br>tolower()|(1) |
| substituir | replace() | (1)<br> Observe também que, embora `replace()` use três parâmetros em ambos os produtos, os parâmetros são diferentes. |
| substr | substring() | (1)<br>Observe também que Splunk usa índices com base em um. O Azure Monitor usa índices com base em zero. |
| tolower |  tolower() | (1) |
| toupper | toupper() | (1) |
| match | matches regex |  (2)  |
| regex | matches regex | No Splunk, `regex` é um operador. No Azure Monitor, é um operador relacional. |
| searchmatch | == | No Splunk, `searchmatch` permite pesquisar a cadeia de caracteres exata.
| random | rand()<br>rand(n) | A função do Splunk retorna um número de zero a 2<sup>31</sup>-1. O Azure Monitor retorna um número entre 0,0 e 1,0 ou, se um parâmetro é fornecido, entre 0 e n-1.
| now | now() | (1)
| relative_time | totimespan() | (1)<br>No Azure Monitor, o equivalente do Splunk de relative_time(datetimeVal, offsetVal) é datetimeVal + totimespan(offsetVal).<br>Por exemplo, <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> torna-se <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) no Splunk, a função é invocada com o operador `eval`. No Azure Monitor, ela é usada como parte de `extend` ou `project`.<br>(2) no Splunk, a função é invocada com o operador `eval`. No Azure Monitor, ela pode ser usada com o operador `where`.


## <a name="operators"></a>Operadores

As seções a seguir fornecem exemplos do uso de operadores diferentes entre o Azure Monitor e o Splunk.

> [!NOTE]
> Para fins do exemplo a seguir, o campo do Splunk _rule_ mapeia para uma tabela no Azure Monitor e o carimbo de data/hora padrão do Splunk mapeia para a coluna _ingestion_time()_ do Log Analytics.

### <a name="search"></a>Pesquisar
No Splunk, você pode omitir a palavra-chave `search` e especificar uma cadeia de caracteres sem aspas. No Azure Monitor, você deve iniciar cada consulta com `find`, uma cadeia de caracteres sem aspas é um nome de coluna e o valor de pesquisa deve ser uma cadeia de caracteres entre aspas. 

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **search** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| **Azure Monitor** | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |


### <a name="filter"></a>Filtrar
As consultas de log do Azure Monitor iniciam em um conjunto de resultados tabulares de onde filtrar. No Splunk, a filtragem é a operação padrão no índice atual. Você também pode usar o operador `where` no Splunk, mas isso não é recomendado.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **search** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| **Azure Monitor** | **where** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |

### <a name="getting-n-eventsrows-for-inspection"></a>Obtendo n eventos/linhas para inspeção 
Consultas de log do Azure Monitor também dão suporte a `take` como um alias para `limit`. No Splunk, se os resultados forem ordenados, `head` retornará os primeiros n resultados. No Azure Monitor, o limite não é ordenado, mas retorna as primeiras n linhas encontradas.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **principal** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| **Azure Monitor** | **limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |

### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Obtendo os primeiros n eventos/linhas ordenados por um campo/coluna
Para obter os resultados finais, use `tail` no Splunk. No Azure Monitor, você pode especificar a direção de classificação com `asc`.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **principal** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| **Azure Monitor** | **início** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |

### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Estendendo o conjunto de resultados com novos campos/colunas
O Splunk também tem uma função `eval`, que não é comparável ao operador `eval`. O operador `eval` no Splunk e o operador `extend` no Azure Monitor dá suporte apenas a funções de valor escalar e a operadores aritméticos.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| **Azure Monitor** | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |

### <a name="rename"></a>Renomear 
Azure Monitor usa o `project-rename` operador para renomear um campo. `project-rename` permite que a consulta aproveite todos os índices criados previamente para um campo. Splunk tem um `rename` operador para fazer o mesmo.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **rename** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| **Azure Monitor** | **projeto-renomear** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |

### <a name="format-resultsprojection"></a>Formatar os resultados/projeção
O Splunk não parece ter um operador semelhante a `project-away`. Você pode usar a interface do usuário para filtrar os campos ausentes.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **table** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| **Azure Monitor** | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |

### <a name="aggregation"></a>Agregação
Confira as [Agregações em consultas de log do Azure Monitor](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations) para as diferentes funções de agregação.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **Estatísticas** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| **Azure Monitor** | **summarize** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |


### <a name="join"></a>Join
Unir-se ao Splunk tem limitações significativas. A subconsulta tem um limite de 10000 resultados (definido no arquivo de configuração de implantação), e há um número limitado de tipos de junção.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| **Azure Monitor** | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |

### <a name="sort"></a>Classificar
No Splunk, para classificar em ordem crescente, você deve usar o operador `reverse`. O Azure Monitor também dá suporte à definição de onde colocar os nulos, no início ou no final.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **sort** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| **Azure Monitor** | **ordenar por** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |

### <a name="multivalue-expand"></a>Expansão de múltiplos valores
Este é um operador semelhante no Splunk e no Azure Monitor.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **mvexpand** |  `mvexpand foo` |
| **Azure Monitor** | **mvexpand** | `mvexpand foo` |

### <a name="results-facets-interesting-fields"></a>Facetas de resultados, campos interessantes
No Log Analytics, no portal do Azure, apenas a primeira coluna é exposta. Todas as colunas estão disponíveis por meio da API.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **campos** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| **Azure Monitor** | **facetas** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |

### <a name="de-duplicate"></a>Eliminar duplicação
Você pode usar `summarize arg_min()` para inverter a ordem da qual o registro é escolhido.

| | Operador | Exemplo |
|:---|:---|:---|
| **Splunk** | **eliminação** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| **Azure Monitor** | **summarize arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |

## <a name="next-steps"></a>Próximas etapas

- Faça uma lição sobre como [escrever consultas de log no Azure Monitor](get-started-queries.md).
