---
title: Consultar o grafo de gêmeos
titleSuffix: Azure Digital Twins
description: Consulte como consultar o grafo gêmeos do Azure digital para obter informações.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 3fd504ec36abae3f00cd2a7eb4e1f7b639be0cea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462670"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Consultar o grafo gêmeos do Azure digital

Este artigo oferece exemplos de consulta e instruções mais detalhadas para usar a **linguagem de consulta do gêmeos digital do Azure** para consultar seu gráfico de [entrelaçamento](concepts-twins-graph.md) para obter informações. (Para obter uma introdução à linguagem de consulta e uma lista completa de seus recursos, consulte [*conceitos: linguagem de consulta*](concepts-query-language.md).)

Este artigo começa com consultas de exemplo que ilustram a estrutura da linguagem de consulta e as operações de consulta comuns para gêmeos digital. Em seguida, ele descreve como executar suas consultas depois de tê-las gravadas, usando a [API de consulta](/rest/api/digital-twins/dataplane/query) do gêmeos digital do Azure ou um [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis).

> [!NOTE]
> Se você estiver executando as consultas de exemplo abaixo com uma API ou chamada do SDK, precisará condensar o texto da consulta em uma única linha.

## <a name="show-all-digital-twins"></a>Mostrar todos os gêmeos digitais

Aqui está a consulta básica que retornará uma lista de todos os gêmeos digitais na instância:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="query-by-property"></a>Consulta por Propriedade

Obter gêmeos digital por **Propriedades** (incluindo a ID e os metadados):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty1":::

Conforme mostrado na consulta acima, a ID de uma pesquisa digital é consultada usando o campo de metadados `$dtId` .

>[!TIP]
> Se você estiver usando Cloud Shell para executar uma consulta com campos de metadados que começam com `$` o, você deverá escapar o `$` com um acento grave para permitir que Cloud Shell saiba que não é uma variável e deve ser consumida como um literal no texto da consulta.

Você também pode obter gêmeos com base no **fato de uma determinada propriedade ser definida**. Aqui está uma consulta que obtém gêmeos que têm uma propriedade *Location* definida:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty2":::

Isso pode ajudá-lo a obter gêmeos por suas propriedades de *marca* , conforme descrito em [Adicionar marcas ao gêmeos digital](how-to-use-tags.md). Aqui está uma consulta que obtém todos os gêmeos marcados com *vermelho*:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Você também pode obter gêmeos com base no **tipo de uma propriedade**. Aqui está uma consulta que obtém gêmeos cuja propriedade de *temperatura* é um número:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty3":::

>[!TIP]
> Se uma propriedade for do tipo `Map` , você poderá usar as chaves e os valores do mapa diretamente na consulta, desta forma:
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty4":::

## <a name="query-by-model"></a>Consulta por modelo

O `IS_OF_MODEL` operador pode ser usado para filtrar com base no [**modelo**](concepts-models.md)de entrelaçamento.

Ele considera a [herança](concepts-models.md#model-inheritance) e o [controle de versão](how-to-manage-model.md#update-models)de modelo e é avaliado como **verdadeiro** para um determinado "or" se o "My" atender a qualquer uma dessas condições:

* O ' n ' implementa diretamente o modelo fornecido ao `IS_OF_MODEL()` , e o número de versão do modelo em ' n ' é *maior ou igual ao* número de versão do modelo fornecido
* O n implementa um modelo que *estende* o modelo fornecido ao `IS_OF_MODEL()` e o número de versão do modelo estendido de r é *maior ou igual ao* número de versão do modelo fornecido

Por exemplo, se você consultar gêmeos do modelo `dtmi:example:widget;4` , a consulta retornará todos os gêmeos com base na **versão 4 ou superior** do modelo de **widget** e também gêmeos com base na versão **4 ou superior** de quaisquer **modelos herdados do widget**.

`IS_OF_MODEL` pode levar vários parâmetros diferentes e o restante desta seção é dedicado a suas diferentes opções de sobrecarga.

O uso mais simples de `IS_OF_MODEL` usa apenas um `twinTypeName` parâmetro: `IS_OF_MODEL(twinTypeName)` .
Aqui está um exemplo de consulta que passa um valor nesse parâmetro:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel1":::

Para especificar uma coleção de entrelaçamento a ser pesquisada quando houver mais de uma (como quando um `JOIN` é usado), adicione o `twinCollection` parâmetro: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Aqui está um exemplo de consulta que adiciona um valor para esse parâmetro:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel2":::

Para fazer uma correspondência exata, adicione o `exact` parâmetro: `IS_OF_MODEL(twinTypeName, exact)` .
Aqui está um exemplo de consulta que adiciona um valor para esse parâmetro:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel3":::

Você também pode passar todos os três argumentos juntos: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Aqui está um exemplo de consulta que especifica um valor para todos os três parâmetros:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel4":::

## <a name="query-by-relationship"></a>Consultar por relação

Ao consultar com base em **relações** de gêmeos digital, a linguagem de consulta gêmeos digital do Azure tem uma sintaxe especial.

Os relacionamentos são capturados para o escopo da consulta na cláusula `FROM`. Uma distinção importante das linguagens do tipo SQL "clássico" é que cada expressão nessa `FROM` cláusula não é uma tabela; em vez disso, a `FROM` cláusula expressa uma passagem de relacionamento entre entidades e é escrita com uma versão do gêmeos digital do Azure do `JOIN` .

Lembre-se de que com os recursos do [modelo](concepts-models.md) de gêmeos digital do Azure, as relações não existem independentemente do gêmeos. Isso significa que a `JOIN` da linguagem de consulta dos Gêmeos Digitais do Azure é um pouco diferente da `JOIN` geral do SQL, pois os relacionamentos aqui não podem ser consultados de modo independente e devem estar vinculados a um gêmeo.
Para incorporar essa diferença, a palavra-chave `RELATED` é usada na cláusula `JOIN` para fazer referência a um conjunto de relacionamentos de um gêmeo.

A seção a seguir fornece vários exemplos de como isso se parece.

> [!TIP]
> Conceitualmente, esse recurso imita a funcionalidade centrada no documento de CosmosDB, onde `JOIN` pode ser executado em objetos filho dentro de um documento. CosmosDB usa a `IN` palavra-chave para indicar que o deve `JOIN` ser iterado em elementos de matriz dentro do documento de contexto atual.

### <a name="relationship-based-query-examples"></a>Exemplos de consulta baseada em relação

Para obter um conjunto de resultados que inclui relações, use uma única `FROM` instrução seguida por N `JOIN` instruções, em que as `JOIN` instruções expressam relações no resultado de uma `FROM` instrução ou anterior `JOIN` .

Aqui está um exemplo de consulta baseada em relação. Esse trecho de código seleciona todas as gêmeos digitais com uma propriedade de *ID* de ' abc ' e todas as gêmeos digitais relacionadas a essas gêmeos digitais por meio de uma relação *Contains* .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship1":::

> [!NOTE]
> O desenvolvedor não precisa correlacionar isso `JOIN` com um valor de chave na `WHERE` cláusula (ou especificar um valor de chave embutido com a `JOIN` definição). Essa correlação é calculada automaticamente pelo sistema, pois as próprias propriedades de relacionamento identificam a entidade de destino.

### <a name="query-the-properties-of-a-relationship"></a>Consultar as propriedades de um relacionamento

Da mesma forma que os gêmeos digitais têm propriedades descritas por meio de DTDL, os relacionamentos também podem ter propriedades. Você pode consultar gêmeos **com base nas propriedades de suas relações**.
A linguagem de consulta do gêmeos digital do Azure permite filtrar e projeção de relações, atribuindo um alias à relação dentro da `JOIN` cláusula.

Como exemplo, considere uma relação *servicedBy* que tem uma propriedade *reportedCondition* . Na consulta abaixo, essa relação recebe um alias de ' R ' para referenciar sua propriedade.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship2":::

No exemplo acima, observe como *reportedCondition* é uma propriedade da relação *servicedBy* em si (não de algumas propriedades digitais que têm uma relação de *servicedBy* ).

### <a name="query-with-multiple-joins"></a>Consulta com várias junções

Há suporte para até cinco `JOIN` s em uma única consulta. Isso permite que você percorra vários níveis de relações de uma só vez.

Aqui está um exemplo de uma consulta de várias junções, que obtém todas as lâmpadas contidas nos painéis leves nas salas 1 e 2.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship3":::

## <a name="count-items"></a>Contar itens

Você pode contar o número de itens em um conjunto de resultados usando a `Select COUNT` cláusula:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount1":::

Adicione uma `WHERE` cláusula para contar o número de itens que atendem a um determinado critério. Aqui estão alguns exemplos de contagem com um filtro aplicado com base no tipo de modelo de entrelaçamento (para obter mais informações sobre essa sintaxe, consulte [*consulta por modelo*](#query-by-model) abaixo):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount2":::

Você também pode usar `COUNT` o junto com a `JOIN` cláusula. Aqui está uma consulta que conta todas as lâmpadas contidas nos painéis leves das salas 1 e 2:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount3":::

## <a name="filter-results-select-top-items"></a>Filtrar resultados: selecionar itens principais

Você pode selecionar os vários itens "principais" em uma consulta usando a `Select TOP` cláusula.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectTop":::

## <a name="filter-results-specify-return-set-with-projections"></a>Filtrar resultados: especificar conjunto de retorno com projeções

Usando projeções na `SELECT` instrução, você pode escolher as colunas que uma consulta retornará.

>[!NOTE]
>Neste momento, não há suporte para propriedades complexas. Para garantir que as propriedades de projeção sejam válidas, combine as projeções com uma `IS_PRIMITIVE` verificação.

Aqui está um exemplo de uma consulta que usa projeção para retornar gêmeos e relações. A consulta a seguir projeta o *consumidor*, a *fábrica* e a *borda* de um cenário em que um *alocador* com uma ID de *ABC* está relacionado ao *consumidor* por meio de uma relação de *Factory. Customer* e essa relação é apresentada como a *borda*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections1":::

Você também pode usar projeção para retornar uma propriedade de uma folha de entrelaçar. A consulta a seguir projeta a propriedade *Name* dos *consumidores* que estão relacionados à *fábrica* com uma ID de *ABC* por meio de uma relação de *Factory. Customer*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections2":::

Você também pode usar projeção para retornar uma propriedade de uma relação. Como no exemplo anterior, a consulta a seguir projeta a propriedade *Name* dos *consumidores* relacionados à *fábrica* com uma ID de *ABC* por meio de uma relação de *Factory. Customer*; Mas agora ele também retorna duas propriedades dessa relação, *Prop1* e *prop2*. Ele faz isso nomeando a *borda* da relação e reunindo suas propriedades.  

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections3":::

Você também pode usar aliases para simplificar consultas com projeção.

A consulta a seguir faz as mesmas operações que o exemplo anterior, mas ele alias os nomes de propriedade para `consumerName` , `first` , `second` e `factoryArea` .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections4":::

Aqui está uma consulta semelhante que consulta o mesmo conjunto como acima, mas projeta apenas a propriedade *Consumer.Name* como `consumerName` e projeta a *fábrica* completa como um conjunto de entrelaçamento.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections5":::

## <a name="build-efficient-queries-with-the-in-operator"></a>Criar consultas eficientes com o operador IN

Você pode reduzir significativamente o número de consultas necessárias criando uma matriz de gêmeos e consultando com o `IN` operador. 

Por exemplo, considere um cenário no qual os *edifícios* contêm *andares* e *andares* contêm *salas*. Para pesquisar salas em um edifício que esteja quente, uma delas é seguir estas etapas.

1. Encontre andares na compilação com base na `contains` relação.

    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWithout":::

2. Para localizar salas, em vez de considerar os andares um a um e executar uma `JOIN` consulta para localizar os quartos de cada um, você pode consultar com uma coleção dos andares no edifício (chamado *andar* na consulta abaixo).

    No aplicativo cliente:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    Em consulta:
    
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWith":::

## <a name="other-compound-query-examples"></a>Outros exemplos de consulta composta

Você pode **combinar** qualquer um dos tipos de consulta acima usando operadores de combinação para incluir mais detalhes em uma única consulta. Aqui estão alguns exemplos adicionais de consultas compostas que consultam mais de um tipo de descritor de texto ao mesmo tempo.

* Os dispositivos que a *sala 123* tem, retornam os dispositivos MxChip que atendem à função de operador
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples1":::
* Obtenha gêmeos que têm uma relação chamada *Contains* com outro "My" que tem uma ID de *ID1*
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples2":::
* Obter todas as salas deste modelo de sala que estão contidas por *floor11*
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples3":::

## <a name="run-queries-with-the-api"></a>Executar consultas com a API

Depois de decidir sobre uma cadeia de caracteres de consulta, execute-a fazendo uma chamada para a [**API de consulta**](/rest/api/digital-twins/dataplane/query).

Você pode chamar a API diretamente ou usar um dos [SDKs](how-to-use-apis-sdks.md#overview-data-plane-apis) disponíveis para o gêmeos digital do Azure.

O trecho de código a seguir ilustra a chamada do [SDK do .net (C#)](/dotnet/api/overview/azure/digitaltwins/client) de um aplicativo cliente:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="RunQuery":::

A consulta usada nesta chamada retorna uma lista de gêmeos digitais, que o exemplo acima representa com objetos [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin) . O tipo de retorno de seus dados para cada consulta dependerá dos termos que você especificar com a `SELECT` instrução:
* As consultas que começam com `SELECT * FROM ...` retornarão uma lista de gêmeos digital (que pode ser serializada como `BasicDigitalTwin` objetos ou outros tipos de troca digital personalizados que você possa ter criado).
* As consultas que começam no formato `SELECT <A>, <B>, <C> FROM ...` retornarão um dicionário com chaves `<A>` , `<B>` e `<C>` .
* Outros formatos de `SELECT` instruções podem ser criados para retornar dados personalizados. Você pode considerar a criação de suas próprias classes para lidar com conjuntos de resultados muito personalizados. 

### <a name="query-with-paging"></a>Consulta com paginação

As chamadas de consulta dão suporte à paginação. Aqui está um exemplo completo usando `BasicDigitalTwin` como tipo de resultado de consulta com tratamento de erro e paginação:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [APIs e SDKs do gêmeos digital do Azure](how-to-use-apis-sdks.md), incluindo a API de consulta que é usada para executar as consultas deste artigo.
