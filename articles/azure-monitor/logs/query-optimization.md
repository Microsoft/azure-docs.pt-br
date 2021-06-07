---
title: Otimizar consultas de log no Azure Monitor
description: Práticas recomendadas para otimizar consultas de log no Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 7b5412b2ca738f5d2099521062e37afcff90e938
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047325"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Otimizar consultas de log no Azure Monitor
Os logs de Azure Monitor usam o [Data Explorer do Azure (ADX)](/azure/data-explorer/) para armazenar dados de log e executar consultas para analisar esses dados. Ele cria, gerencia e mantém os clusters ADX para você e os otimiza para sua carga de trabalho de análise de log. Quando você executa uma consulta, ela é otimizada e roteada para o cluster ADX apropriado que armazena os dados do espaço de trabalho. Os logs de Azure Monitor e o Data Explorer do Azure usam muitos mecanismos de otimização de consulta automática. Embora as otimizações automáticas forneçam um aumento significativo, há alguns casos em que você pode melhorar drasticamente o desempenho da consulta. Este artigo explica as considerações de desempenho e várias técnicas para corrigi-las.

A maioria das técnicas é comum a consultas que são executadas diretamente no Azure Data Explorer e nos logs de Azure Monitor, embora haja várias considerações de logs de Azure Monitor exclusivas que são discutidas aqui. Para obter mais dicas de otimização de Data Explorer do Azure, consulte [práticas recomendadas de consulta](/azure/kusto/query/best-practices).

Consultas otimizadas irão:

- Execute mais rápido, reduza a duração geral da execução da consulta.
- Ter menos chances de ser limitado ou rejeitado.

Você deve dar atenção especial às consultas que são usadas para uso recorrente e intermitente, como dashboards, alertas, aplicativos lógicos e Power BI. O impacto de uma consulta ineficaz nesses casos é substancial.

## <a name="query-performance-pane"></a>Painel de desempenho de consulta
Depois de executar uma consulta no Log Analytics, clique na seta para baixo acima dos resultados da consulta para exibir o painel desempenho da consulta que mostra os resultados de vários indicadores de desempenho para a consulta. Esses indicadores de desempenho são descritos na seção a seguir.

![Painel de desempenho de consulta](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Indicadores de desempenho de consulta

Os seguintes indicadores de desempenho de consulta estão disponíveis para cada consulta executada:

- [Total de CPU](#total-cpu): computação geral usada para processar a consulta em todos os nós de computação. Ele representa o tempo usado para computação, análise e busca de dados. 

- [Dados usados para consulta processada](#data-used-for-processed-query): dados gerais que foram acessados para processar a consulta. Influenciado pelo tamanho da tabela de destino, pelo período de tempo usado, pelos filtros aplicados e pelo número de colunas referenciadas.

- Período [de tempo da consulta processada](#time-span-of-the-processed-query): a lacuna entre os dados mais recentes e mais antigos que foram acessados para processar a consulta. Influenciado pelo intervalo de tempo explícito especificado para a consulta.

- [Idade dos dados processados](#age-of-processed-data): a lacuna entre agora e os dados mais antigos que foram acessados para processar a consulta. Ele é altamente influencia a eficiência da busca de dados.

- [Número de espaços de trabalho](#number-of-workspaces): quantos espaços de trabalho foram acessados durante o processamento da consulta devido à seleção implícita ou explícita.

- [Número de regiões](#number-of-regions): quantas regiões foram acessadas durante o processamento de consulta com base devido à seleção implícita ou explícita de espaços de trabalho. As consultas de várias regiões são muito menos eficientes e os indicadores de desempenho apresentam cobertura parcial.

- [Paralelismo](#parallelism): indica o quanto o sistema conseguiu executar essa consulta em vários nós. Relevante apenas para consultas que têm alto consumo de CPU. Influenciado pelo uso de funções e operadores específicos.


## <a name="total-cpu"></a>Total de CPU
A CPU de computação real que foi investido para processar essa consulta em todos os nós de processamento de consulta. Como a maioria das consultas é executada em um grande número de nós, isso geralmente será muito maior do que a duração que a consulta realmente levou para ser executada. 

A consulta que utiliza mais de 100 segundos de CPU é considerada uma consulta que consome recursos excessivos. A consulta que utiliza mais de 1.000 segundos de CPU é considerada uma consulta abusiva e pode ser limitada.

O tempo de processamento de consulta é gasto em:
- Recuperação de dados – a recuperação de dados antigos consumirá mais tempo do que a recuperação de dados recentes.
- Processamento de dados – lógica e avaliação dos dados. 

Além do tempo gasto nos nós de processamento de consulta, há um tempo adicional gasto pelo Azure Monitor logs para: autenticar o usuário e verificar se eles têm permissão para acessar esses dados, localizar o armazenamento de dados, analisar a consulta e alocar os nós de processamento de consulta. Esse tempo não é incluído na consulta tempo total de CPU.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>Filtragem antecipada de registros antes do uso de funções de CPU alta

Alguns comandos e funções de consulta são pesados em seu consumo de CPU. Isso é especialmente verdadeiro para comandos que analisam JSON e XML ou extraem expressões regulares complexas. Essa análise pode ocorrer explicitamente por meio de funções [parse_json ()](/azure/kusto/query/parsejsonfunction) ou [parse_xml ()](/azure/kusto/query/parse-xmlfunction) ou implicitamente ao se referir a colunas dinâmicas.

Essas funções consomem CPU proporcionalmente ao número de linhas que estão processando. A otimização mais eficiente é adicionar onde as condições logo no início da consulta podem filtrar o máximo possível de registros antes que a função intensiva de CPU seja executada.

Por exemplo, as consultas a seguir produzem exatamente o mesmo resultado, mas a segunda é, de longe, a mais eficiente, pois a condição [Where](/azure/kusto/query/whereoperator) antes da análise exclui muitos registros:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
| summarize count() by FileHash, FilePath
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // exact removal of results. Early filter is not accurate enough
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>Evite usar cláusulas de Where avaliadas

As consultas que contêm cláusulas [Where](/azure/kusto/query/whereoperator) em uma coluna avaliada, em vez de colunas que estão fisicamente presentes no conjunto de linhas, perdem a eficiência. A filtragem em colunas avaliadas impede algumas otimizações do sistema quando grandes conjuntos de dados são manipulados.
Por exemplo, as consultas a seguir produzem exatamente o mesmo resultado, mas a segunda é mais eficiente como a condição [Where](/azure/kusto/query/whereoperator) refere-se à coluna interna

```Kusto
//less efficient
Syslog
| extend Msg = strcat("Syslog: ",SyslogMessage)
| where  Msg  has "Error"
| count 
```
```Kusto
//more efficient
Syslog
| where  SyslogMessage  has "Error"
| count 
```

Em alguns casos, a coluna avaliada é criada implicitamente pelo mecanismo de processamento de consulta, já que a filtragem é feita não apenas no campo:
```Kusto
//less efficient
SecurityEvent
| where tolower(Process) == "conhost.exe"
| count 
```
```Kusto
//more efficient
SecurityEvent
| where Process =~ "conhost.exe"
| count 
```




### <a name="use-effective-aggregation-commands-and-dimensions-in-summarize-and-join"></a>Usar comandos e dimensões de agregação efetivas em resumir e unir

Embora alguns comandos de agregação como [Max ()](/azure/kusto/query/max-aggfunction), [Sum ()](/azure/kusto/query/sum-aggfunction), [Count ()](/azure/kusto/query/count-aggfunction)e [AVG ()](/azure/kusto/query/avg-aggfunction) tenham baixo impacto na CPU devido à sua lógica, outros são mais complexos e incluem heurísticas e estimativas que permitem que eles sejam executados com eficiência. Por exemplo, [DContar ()](/azure/kusto/query/dcount-aggfunction) usa o algoritmo HyperLogLog para fornecer estimativa de fechamento para contagem distinta de grandes conjuntos de dados sem contar realmente cada valor; as funções de percentil estão fazendo aproximações semelhantes usando o algoritmo percentil de classificação mais próximo. Vários dos comandos incluem parâmetros opcionais para reduzir o impacto. Por exemplo, a função [makeset ()](/azure/kusto/query/makeset-aggfunction) tem um parâmetro opcional para definir o tamanho máximo do conjunto, o que afeta significativamente a CPU e a memória.

Os comandos [Join](/azure/kusto/query/joinoperator?pivots=azuremonitor) e [resume](/azure/kusto/query/summarizeoperator) podem causar alta utilização da CPU durante o processamento de um grande conjunto de dados. Sua complexidade está diretamente relacionada ao número de valores possíveis, conhecidos como *cardinalidade*, das colunas que estão usando como `by` em resumo ou como os atributos de junção. Para obter a explicação e a otimização de join e resume, consulte seus artigos de documentação e dicas de otimização.

Por exemplo, as consultas a seguir produzem exatamente o mesmo resultado porque o **caminho** de causa é sempre um para um mapeado para **CounterName** e **objectname**. A segunda é mais eficiente, uma vez que a dimensão de agregação é menor:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

O consumo de CPU também pode ser afetado por condições ou colunas estendidas que exigem computação intensiva. Todas as comparações de cadeia de caracteres triviais como igual a = [=](/azure/kusto/query/datatypes-string-operators) e [StartsWith](/azure/kusto/query/datatypes-string-operators) têm aproximadamente o mesmo impacto de CPU enquanto as correspondências de texto avançadas têm mais impacto. Especificamente, o operador [tem](/azure/kusto/query/datatypes-string-operators) é mais eficiente que o operador [Contains](/azure/kusto/query/datatypes-string-operators) . Devido a técnicas de manipulação de cadeias de caracteres, é mais eficiente procurar cadeias de caracteres com mais de quatro caracteres do que as cadeias curtas.

Por exemplo, as consultas a seguir produzem resultados semelhantes, dependendo da política de nomenclatura do computador, mas a segunda é mais eficiente:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Esse indicador apresenta apenas a CPU do cluster imediato. Na consulta de várias regiões, ele representaria apenas uma das regiões. Na consulta de vários espaços de trabalho, ele pode não incluir todos os espaços de trabalho.

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>Evite a análise completa de XML e JSON quando a análise de cadeia de caracteres funciona
A análise completa de um objeto XML ou JSON pode consumir alta utilização de recursos de CPU e memória. Em muitos casos, quando apenas um ou dois parâmetros são necessários e os objetos XML ou JSON são simples, é mais fácil analisá-los como cadeias de caracteres usando o [operador Parse](/azure/kusto/query/parseoperator) ou outras [técnicas de análise de texto](./parse-text.md). O aumento de desempenho será mais significativo à medida que o número de registros no objeto XML ou JSON aumentar. É essencial quando o número de registros atinge dezenas de milhões.

Por exemplo, a consulta a seguir retornará exatamente os mesmos resultados que as consultas acima sem executar a análise de XML completa. Observe que ele faz algumas suposições na estrutura do arquivo XML, como o elemento FilePath vem após FileHash e nenhum deles tem atributos. 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>Dados usados para consulta processada

Um fator crítico no processamento da consulta é o volume de dados que é verificado e usado para o processamento da consulta. O Azure Data Explorer usa otimizações agressivas que reduzem drasticamente o volume de dados em comparação com outras plataformas de dados. Ainda assim, há fatores críticos na consulta que podem afetar o volume de dados que é usado.

A consulta que processa mais de 2, 000KB de dados é considerada uma consulta que consome recursos excessivos. A consulta que está processando mais de 20, 000KB de dados é considerada uma consulta abusiva e pode ser limitada.

Nos logs de Azure Monitor, a coluna **TimeGenerated** é usada como uma maneira de indexar os dados. A restrição dos valores **TimeGenerated** para o mais estreito possível fará uma melhoria significativa no desempenho da consulta, limitando significativamente a quantidade de dados a serem processados.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>Evitar o uso desnecessário de operadores de pesquisa e de União

Outro fator que aumenta os dados que são processos é o uso de um grande número de tabelas. Isso geralmente acontece quando `search *` os `union *` comandos e são usados. Esses comandos forçam o sistema a avaliar e verificar dados de todas as tabelas no espaço de trabalho. Em alguns casos, pode haver centenas de tabelas no espaço de trabalho. Tente evitar o máximo possível usando "Search *" ou qualquer pesquisa sem escopo-o para uma tabela específica.

Por exemplo, as consultas a seguir produzem exatamente o mesmo resultado, mas a última é de longe a mais eficiente:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>Adicionar filtros antecipados à consulta

Outro método para reduzir o volume de dados é ter as condições [Where](/azure/kusto/query/whereoperator) no início da consulta. A plataforma de Data Explorer do Azure inclui um cache que permite saber quais partições incluem dados relevantes para uma condição WHERE específica. Por exemplo, se uma consulta contiver `where EventID == 4624` , ela distribuiria a consulta somente para nós que lidam com as partições com eventos correspondentes.

As consultas de exemplo a seguir produzem exatamente o mesmo resultado, mas a segunda é mais eficiente:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="avoid-multiple-scans-of-same-source-data-using-conditional-aggregation-functions-and-materialize-function"></a>Evitar várias verificações dos mesmos dados de origem usando funções de agregação condicionais e função de materializar
Quando uma consulta tem várias subconsultas que são mescladas usando operadores Join ou Union, cada uma delas examina toda a origem separadamente e, em seguida, mescla os resultados. Isso multiplica o número de vezes que os dados são examinados – fator crítico em conjuntos de dados muito grandes.

Uma técnica para evitar isso é usando as funções de agregação condicionais. A maioria das [funções de agregação](/azure/data-explorer/kusto/query/summarizeoperator#list-of-aggregation-functions) que são usadas no operador de resumo tem uma versão condicional que permite que você use um único operador de resumo com várias condições. 

Por exemplo, as consultas a seguir mostram o número de eventos de logon e o número de eventos de execução de processo para cada conta. Eles retornam os mesmos resultados, mas o primeiro está verificando os dados duas vezes, a segunda verificação apenas uma vez:

```Kusto
//Scans the SecurityEvent table twice and perform expensive join
SecurityEvent
| where EventID == 4624 //Login event
| summarize LoginCount = count() by Account
| join 
(
    SecurityEvent
    | where EventID == 4688 //Process execution event
    | summarize ExecutionCount = count(), ExecutedProcesses = make_set(Process) by Account
) on Account
```

```Kusto
//Scan only once with no join
SecurityEvent
| where EventID == 4624 or EventID == 4688 //early filter
| summarize LoginCount = countif(EventID == 4624), ExecutionCount = countif(EventID == 4688), ExecutedProcesses = make_set_if(Process,EventID == 4688)  by Account
```

Outro caso em que as subconsultas são desnecessárias é a filtragem prévia do [operador de análise](/azure/data-explorer/kusto/query/parseoperator?pivots=azuremonitor) para garantir que ele processe somente os registros que correspondem ao padrão específico. Isso é desnecessário, pois o operador Parse e outros operadores semelhantes retornam resultados vazios quando o padrão não corresponde. Aqui estão duas consultas que retornam exatamente os mesmos resultados, enquanto a segunda consulta verifica os dados apenas uma vez. Na segunda consulta, cada comando Parse é relevante apenas para seus eventos. Em seguida, o operador Extend mostra como se referir a uma situação de dados vazia.

```Kusto
//Scan SecurityEvent table twice
union(
SecurityEvent
| where EventID == 8002 
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| distinct FilePath
),(
SecurityEvent
| where EventID == 4799
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" * 
| distinct CallerProcessName1
)
```

```Kusto
//Single scan of the SecurityEvent table
SecurityEvent
| where EventID == 8002 or EventID == 4799
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" * //Relevant only for event 8002
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" *  //Relevant only for event 4799
| extend FilePath = iif(isempty(CallerProcessName1),FilePath,"")
| distinct FilePath, CallerProcessName1
```

Quando o mostrado acima não permite o uso de subconsultas, outra técnica é a dica ao mecanismo de consulta de que há um único dado de origem usado em cada um deles usando a [função materializar ()](/azure/data-explorer/kusto/query/materializefunction?pivots=azuremonitor). Isso é útil quando os dados de origem são provenientes de uma função que é usada várias vezes dentro da consulta. O materializar é eficaz quando a saída da subconsulta é muito menor do que a entrada. O mecanismo de consulta armazenará em cache e reutilizará a saída em todas as ocorrências.



### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>Reduzir o número de colunas que são recuperadas

Como o Azure Data Explorer é um armazenamento de dados de coluna, a recuperação de cada coluna é independente das outras. O número de colunas que são recuperadas diretamente influencia o volume de dados geral. Você só deve incluir as colunas na saída que são necessárias [Resumindo](/azure/kusto/query/summarizeoperator) os resultados ou [projetando](/azure/kusto/query/projectoperator) as colunas específicas. O Azure Data Explorer tem várias otimizações para reduzir o número de colunas recuperadas. Se ele determinar que uma coluna não é necessária, por exemplo, se não for referenciada no comando [resumir](/azure/kusto/query/summarizeoperator) , ela não a recuperará.

Por exemplo, a segunda consulta pode processar três vezes mais dados, já que ele precisa buscar uma coluna, mas três:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>Período de tempo da consulta processada

Todos os logs em logs de Azure Monitor são particionados de acordo com a coluna **TimeGenerated** . O número de partições acessadas está diretamente relacionado ao período de tempo. Reduzir o intervalo de tempo é a maneira mais eficiente de garantir uma execução de consulta de prompt.

A consulta com período de mais de 15 dias é considerada uma consulta que consome recursos excessivos. A consulta com período de mais de 90 dias é considerada uma consulta abusiva e pode ser limitada.

O intervalo de tempo pode ser definido usando o seletor de intervalo de tempo na tela de Log Analytics, conforme descrito em [escopo de consulta de log e intervalo de tempo em Azure Monitor log Analytics](./scope.md#time-range). Esse é o método recomendado, pois o intervalo de tempo selecionado é passado para o back-end usando os metadados de consulta. 

Um método alternativo é incluir explicitamente uma condição [Where](/azure/kusto/query/whereoperator) em **TimeGenerated** na consulta. Você deve usar esse método, pois garante que o período de tempo seja fixo, mesmo quando a consulta for usada de uma interface diferente.
Você deve garantir que todas as partes da consulta tenham filtros **TimeGenerated** . Quando uma consulta tem subconsultas buscando dados de várias tabelas ou da mesma tabela, cada uma tem que incluir sua própria condição [Where](/azure/kusto/query/whereoperator) .

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>Verificar se todas as subconsultas têm o filtro TimeGenerated

Por exemplo, na consulta a seguir, enquanto a tabela **perf** será verificada somente pelo último dia, a tabela de **pulsação** será verificada em busca de todo o seu histórico, que pode ser de até dois anos:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Um caso comum em que esse erro ocorre é quando [ARG_MAX ()](/azure/kusto/query/arg-max-aggfunction) é usado para localizar a ocorrência mais recente. Por exemplo:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Isso pode ser facilmente corrigido com a adição de um filtro de tempo na consulta interna:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Outro exemplo para essa falha é ao executar a filtragem de escopo de tempo logo após uma [União](/azure/kusto/query/unionoperator?pivots=azuremonitor) em várias tabelas. Ao executar a União, cada subconsulta deve ser delimitada. Você pode usar a instrução [Let](/azure/kusto/query/letstatement) para garantir a consistência do escopo.

Por exemplo, a consulta a seguir examinará todos os dados nas tabelas de *pulsação* e de *desempenho* , não apenas os últimos 1 dia:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

Essa consulta deve ser corrigida da seguinte maneira:

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>Limitações de medição de intervalo de tempo

A medida é sempre maior do que o tempo real especificado. Por exemplo, se o filtro na consulta for de sete dias, o sistema poderá verificar 7,5 ou 8,1 dias. Isso ocorre porque o sistema está particionando os dados em partes em tamanho variável. Para garantir que todos os registros relevantes sejam verificados, ele verifica toda a partição que pode abranger várias horas e ainda mais do que um dia.

Há vários casos em que o sistema não pode fornecer uma medição precisa do intervalo de tempo. Isso acontece na maioria dos casos em que o intervalo da consulta é menor que um dia ou em consultas de vários espaços de trabalho.


> [!IMPORTANT]
> Esse indicador apresenta apenas os dados processados no cluster imediato. Na consulta de várias regiões, ele representaria apenas uma das regiões. Na consulta de vários espaços de trabalho, ele pode não incluir todos os espaços de trabalho.

## <a name="age-of-processed-data"></a>Idade dos dados processados
O Azure Data Explorer usa várias camadas de armazenamento: em memória, discos SSD locais e BLOBs do Azure muito mais lentos. Quanto mais novos os dados, mais alto é a chance de que ele seja armazenado em uma camada mais eficaz com menor latência, reduzindo a duração da consulta e a CPU. Além dos dados em si, o sistema também tem um cache para metadados. Quanto mais antigos os dados, menos chance seus metadados estarão no cache.

A consulta que processa dados além de 14 dias de idade é considerada uma consulta que consome recursos excessivos.


Embora algumas consultas exijam o uso de dados antigos, há casos em que os dados antigos são usados por engano. Isso acontece quando as consultas são executadas sem fornecer um intervalo de tempo em seus metadados e nem todas as referências de tabela incluem o filtro na coluna **TimeGenerated** . Nesses casos, o sistema examinará todos os dados armazenados nessa tabela. Quando a retenção de dados é longa, ela pode cobrir intervalos de tempo longos e, portanto, dados tão antigos quanto o período de retenção de dados.

Esses casos podem ser por exemplo:

- Não definir o intervalo de tempo em Log Analytics com uma subconsulta que não seja limitada. Consulte o exemplo acima.
- Usando a API sem os parâmetros opcionais do intervalo de tempo.
- Usar um cliente que não força um intervalo de tempo, como o conector de Power BI.

Veja exemplos e observações na seção anterior, pois elas também são relevantes nesse caso.

## <a name="number-of-regions"></a>Número de regiões
Há várias situações em que uma única consulta pode ser executada em diferentes regiões:

- Quando vários espaços de trabalho estão explicitamente listados e estão localizados em regiões diferentes.
- Quando uma consulta com escopo de recurso está buscando dados e os dados são armazenados em vários espaços de trabalho que estão localizados em regiões diferentes.

A execução de consulta entre regiões exige que o sistema Serialize e transfira no back-end grandes partes de dados intermediários que geralmente são muito maiores do que os resultados finais da consulta. Ele também limita a capacidade do sistema de executar otimizações, heurística e utilizar caches.
Se não houver nenhum motivo para verificar todas essas regiões, você deve ajustar o escopo para abranger menos regiões. Se o escopo do recurso for minimizado, mas ainda muitas regiões forem usadas, isso pode acontecer devido a uma configuração incorreta. Por exemplo, os logs de auditoria e as configurações de diagnóstico são enviados para espaços de trabalho diferentes em regiões diferentes ou há várias configurações de configurações de diagnóstico. 

A consulta que abrange mais de três regiões é considerada uma consulta que consome recursos excessivos. A consulta que abrange mais de 6 regiões é considerada uma consulta abusiva e pode ser limitada.

> [!IMPORTANT]
> Quando uma consulta é executada em várias regiões, as medições de CPU e dados não serão precisas e representarão a medida somente em uma das regiões.

## <a name="number-of-workspaces"></a>Número de espaços de trabalho
Os espaços de trabalho são contêineres lógicos que são usados para separar e administrar dados de logs. O back-end otimiza os posicionamentos de espaço de trabalho em clusters físicos na região selecionada.

O uso de vários espaços de trabalho pode resultar de: 

- Onde vários espaços de trabalho são explicitamente listados.
- Quando uma consulta com escopo de recurso está buscando dados e os dados são armazenados em vários espaços de trabalho.
 
A execução de consultas entre regiões e entre clusters exige que o sistema Serialize e transfira no back-end grandes partes de dados intermediários que geralmente são muito maiores do que os resultados finais da consulta. Ele também limita a capacidade do sistema de executar otimizações, heurística e utilização de caches.

A consulta que abrange mais de 5 espaço de trabalho é considerada uma consulta que consome recursos excessivos. As consultas não podem abranger mais de 100 espaços de trabalho.

> [!IMPORTANT]
> Em alguns cenários de vários espaços de trabalho, as medições de CPU e dados não serão precisas e representarão a medida apenas para alguns dos espaços de trabalho.

## <a name="parallelism"></a>Paralelismo
Os logs de Azure Monitor estão usando clusters grandes do Data Explorer do Azure para executar consultas, e esses clusters variam em escala, potencialmente chegando a dezenas de nós de computação. O sistema dimensiona automaticamente os clusters de acordo com a lógica e a capacidade de posicionamento do espaço de trabalho.

Para executar uma consulta com eficiência, ela é particionada e distribuída para nós de computação com base nos dados necessários para seu processamento. Há algumas situações em que o sistema não pode fazer isso com eficiência. Isso pode levar a uma longa duração da consulta. 

Os comportamentos de consulta que podem reduzir o paralelismo incluem:

- O uso de funções de serialização e de janela, como o [operador serializar](/azure/kusto/query/serializeoperator), [Next ()](/azure/kusto/query/nextfunction), [Ant ()](/azure/kusto/query/prevfunction)e as funções [Row](/azure/kusto/query/rowcumsumfunction) . As funções de série temporal e análise de usuário podem ser usadas em alguns desses casos. A serialização ineficiente também poderá ocorrer se os operadores a seguir forem usados não no final da consulta: [intervalo](/azure/kusto/query/rangeoperator), [classificação](/azure/kusto/query/sortoperator), [ordem](/azure/kusto/query/orderoperator), [superior](/azure/kusto/query/topoperator), [superior Hitters](/azure/kusto/query/tophittersoperator), [GetSchema](/azure/kusto/query/getschemaoperator).
-    O uso da função de agregação [DContar ()](/azure/kusto/query/dcount-aggfunction) força o sistema a ter uma cópia central dos valores distintos. Quando a escala de dados é alta, considere usar os parâmetros opcionais da função DContar para reduzir a precisão.
-    Em muitos casos, o operador de [junção](/azure/kusto/query/joinoperator?pivots=azuremonitor) reduz o paralelismo geral. Examine a junção em ordem aleatória como alternativa quando o desempenho é problemático.
-    Em consultas de escopo de recurso, as verificações de RBAC ou RBAC do Azure kubernetes podem permanecer em situações em que há um número muito grande de atribuições de função do Azure. Isso pode levar a verificações mais longas que resultaria em um paralelismo inferior. Por exemplo, uma consulta é executada em uma assinatura em que há milhares de recursos e cada recurso tem muitas atribuições de função no nível de recurso, não na assinatura ou no grupo de recursos.
-    Se uma consulta estiver processando pequenas partes de dados, seu paralelismo será baixo, pois o sistema não o espalhará em muitos nós de computação.



## <a name="next-steps"></a>Próximas etapas

- [Documentação de referência para a linguagem de consulta Kusto](/azure/kusto/query/).