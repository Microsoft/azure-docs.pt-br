---
title: Analisar dados de texto nos logs do Azure Monitor | Microsoft Docs
description: Descreve as diferentes opções para análise de dados de log em registros do Azure Monitor quando os dados são ingeridos e recuperados em uma consulta, comparando as vantagens relativas de cada uma.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: bwren
ms.openlocfilehash: ad4839a1b9e951a2bb206518254826a066330000
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61426724"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Analisar dados de texto nos logs do Azure Monitor
Alguns dados de log coletados pelo Azure Monitor incluirão várias informações em uma única propriedade. A análise desses dados em várias propriedades facilita seu uso em consultas. Um exemplo comum é um [log personalizado](../../log-analytics/log-analytics-data-sources-custom-logs.md) que coleta uma entrada de log inteira com vários valores em uma única propriedade. Criando propriedades separadas para os diferentes valores, você pode fazer pesquisas e agregações em cada uma delas.

Este artigo descreve diferentes opções para análise de dados de log no Azure Monitor quando os dados são ingeridos e recuperados em uma consulta, comparando as vantagens relativas de cada uma.


## <a name="parsing-methods"></a>Métodos de análise
Você pode analisar os dados no momento da ingestão, quando os dados são coletados, ou no momento da consulta, ao analisar os dados com uma consulta. Cada estratégia tem vantagens únicas, conforme descrito abaixo.

### <a name="parse-data-at-collection-time"></a>Analisar os dados em tempo de coleta
Ao analisar os dados em tempo de coleção, você configura [Campos Personalizados](../../log-analytics/log-analytics-custom-fields.md) que criam propriedades na tabela. As consultas não precisam incluir qualquer lógica de análise e apenas usam essas propriedades como qualquer outro campo na tabela.

As vantagens desse método incluem as seguintes:

- Consulta mais fácil dos dados coletados, pois você não precisa incluir comandos de análise na consulta.
- Melhor desempenho de consulta, pois a consulta não precisa executar a análise.
 
As desvantagens desse método incluem as seguintes:

- Precisa ser definido com antecedência. Não é possível incluir dados que já foram coletados.
- Se você alterar a lógica de análise, ela será aplicada somente aos novos dados.
- Menos opções de análise do que as disponíveis em consultas.
- Aumento do tempo de latência para a coleta de dados.
- Os erros podem ser difíceis de serem tratados.


### <a name="parse-data-at-query-time"></a>Analisar os dados no momento da consulta
Ao analisar os dados no momento da consulta, você inclui a lógica na consulta para analisar os dados em vários campos. A tabela real em si não é modificada.

As vantagens desse método incluem as seguintes:

- Aplica-se a todos os dados, incluindo os dados que já foram coletados.
- As alterações na lógica podem ser imediatamente aplicadas a todos os dados.
- Opções de análise flexíveis, incluindo lógica predefinida para estruturas de dados específicas.
 
As desvantagens desse método incluem as seguintes:

- Exige consultas mais complexas. Isso pode ser atenuado com o uso de [funções para simular uma tabela](#use-function-to-simulate-a-table).
- Precisa replicar a lógica de análise em várias consultas. Pode compartilhar alguma lógica por meio de funções.
- Pode criar sobrecarga ao executar uma lógica complexa em conjuntos de registros muito grandes (bilhões de registros).

## <a name="parse-data-as-its-collected"></a>Analisar dados conforme eles são coletados
Para obter detalhes sobre a análise de dados conforme eles são coletados, confira [Criar campos personalizados no Azure Monitor](../platform/custom-fields.md). Isso cria propriedades personalizadas na tabela que podem ser usadas pelas consultas como qualquer outra propriedade.

## <a name="parse-data-in-query-using-patterns"></a>Analisar dados na consulta usando padrões
Quando os dados que você deseja analisar podem ser identificados por um padrão repetido nos registros, use operadores diferentes na [linguagem de consulta Kusto](/azure/kusto/query/) para extrair os dados específicos em uma ou mais propriedades novas.

### <a name="simple-text-patterns"></a>Padrões de texto simples

Use o operador [parse](/azure/kusto/query/parseoperator) na consulta para criar uma ou mais propriedades personalizadas que podem ser extraídas de uma expressão de cadeia de caracteres. Especifique o padrão a ser identificado e os nomes das propriedades a serem criadas. Isso é particularmente útil para dados com cadeias de caracteres de chave-valor com um formato semelhante a _chave=valor_.

Considere um log personalizado com os dados no formato a seguir.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

A consulta a seguir analisa esses dados em propriedades individuais. A linha com _project_ é adicionada para retornar somente as propriedades calculadas e não _RawData_, que é a única propriedade que contém toda a entrada do log personalizado.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Veja a seguir outro exemplo que divide o nome de usuário de um UPN na tabela _AzureActivity_.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Expressões regulares
Se os dados podem ser identificados com uma expressão regular, você pode usar [funções que usam expressões regulares](/azure/kusto/query/re2) para extrair valores individuais. O exemplo a seguir usa [extract](/azure/kusto/query/extractfunction) para dividir o campo _UPN_ de registros _AzureActivity_ e, em seguida, retornar usuários distintos.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Para habilitar a análise eficiente em grande escala, o Azure Monitor usa a versão re2 de Expressões Regulares, que é semelhante, mas não idêntica, a algumas das outras variantes de expressão regular. Veja a [Sintaxe da expressão re2](https://aka.ms/kql_re2syntax) para obter detalhes.


## <a name="parse-delimited-data-in-a-query"></a>Analisar dados delimitados em uma consulta
Os dados delimitados separam os campos com um caractere comum como uma vírgula em um arquivo CSV. Use a função [split](/azure/kusto/query/splitfunction) para analisar os dados delimitados usando um delimitador especificado por você. Use isso com o operador [extend](/azure/kusto/query/extendoperator) para retornar todos os campos nos dados ou para especificar os campos individuais a serem incluídos no resultado.

> [!NOTE]
> Como a divisão retorna um objeto dinâmico, talvez os resultados precisem ser explicitamente convertidos em tipos de dados como cadeia de caracteres para serem usados em operadores e filtros.

Considere um log personalizado com os dados no formato CSV a seguir.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

A consulta a seguir analisa esses dados e faz um resumo por duas das propriedades calculadas. A primeira linha divide a propriedade _RawData_ em uma matriz de cadeia de caracteres. Cada uma das próximas linhas fornece um nome para propriedades individuais e as adiciona ao resultado usando funções para convertê-las no tipo de dados apropriado.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>Analisar estruturas predefinidas em uma consulta
Se os dados estão formatados em uma estrutura conhecida, use uma das funções na [linguagem de consulta Kusto](/azure/kusto/query/) para a análise de estruturas predefinidas:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [Consulta de URL](/azure/kusto/query/parseurlqueryfunction)
- [Caminho do arquivo](/azure/kusto/query/parsepathfunction)
- [Agente do usuário](/azure/kusto/query/parse-useragentfunction)
- [Cadeia de caracteres de versão](/azure/kusto/query/parse-versionfunction)

A consulta de exemplo a seguir analisa o campo _Propriedades_ da tabela _AzureActivity_, que é estruturada em JSON. Ela salva os resultados em uma propriedade dinâmica chamada _parsedProp_, que inclui o valor nomeado individual no JSON. Esses valores são usados para filtrar e resumir os resultados da consulta.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Essas funções de análise podem exigir uso intensivo do processador e, portanto, devem ser usadas somente quando a consulta usa várias propriedades dos dados formatados. Caso contrário, a correspondência de padrões simples será mais rápida.

O exemplo a seguir mostra o detalhamento do tipo TGT Preauth do controlador de domínio. O tipo existe somente no campo EventData, que é uma cadeia de caracteres XML, mas nenhum outro dado desse campo é necessário. Nesse caso, [parse](/azure/kusto/query/parseoperator) é usado para selecionar os dados necessários.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Usar uma função para simular uma tabela
Você pode ter várias consultas que executam a mesma análise de uma tabela específica. Nesse caso, [crie uma função](functions.md) que retorna os dados analisados em vez de replicar a lógica de análise em cada consulta. Em seguida, você pode usar o alias da função no lugar da tabela original em outras consultas.

Considere o exemplo de log personalizado delimitado por vírgula acima. Para usar os dados analisados em várias consultas, crie uma função usando a consulta a seguir e salve-a com o alias _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Agora você pode usar o alias _MyCustomCSVLog_ no lugar do nome da tabela real em consultas semelhantes à mostrada a seguir.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [registrar consultas](log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções.