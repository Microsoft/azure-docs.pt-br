---
title: Mapeamento de script de fluxo de dados
description: Visão geral da linguagem code-behind do script de fluxo de dados do Data Factory
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/15/2021
ms.openlocfilehash: 7dd58a7d4a94b832e52930f8ac6507cdd8f7a20e
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100534814"
---
# <a name="data-flow-script-dfs"></a>Script de fluxo de dados (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O script de fluxo de dados (DFS) são os metadados subjacentes, semelhantes a uma linguagem de codificação, que é usada para executar as transformações incluídas em um fluxo de dados de mapeamento. Cada transformação é representada por uma série de propriedades que fornecem as informações necessárias para executar o trabalho corretamente. O script é visível e editável do ADF clicando no botão "script" na faixa de vista superior da interface do usuário do navegador.

![Botão de script](media/data-flow/scriptbutton.png "Botão de script")

Por exemplo, `allowSchemaDrift: true,` em uma transformação de origem, o serviço deve incluir todas as colunas do conjunto de dados de origem no fluxo, mesmo se elas não estiverem incluídas na projeção do esquema.

## <a name="use-cases"></a>Casos de uso
O DFS é produzido automaticamente pela interface do usuário. Você pode clicar no botão script para exibir e personalizar o script. Você também pode gerar scripts fora da interface do usuário do ADF e, em seguida, passá-los para o cmdlet do PowerShell. Ao depurar fluxos de dados complexos, você pode achar mais fácil verificar o code-behind do script em vez de verificar a representação do grafo de interface do usuário de seus fluxos.

Aqui estão alguns exemplos de casos de uso:
- Produzir programaticamente muitos fluxos de dados que são bastante semelhantes, ou seja, fluxos de dados de "carimbo".
- Expressões complexas que são difíceis de gerenciar na interface do usuário ou que resultam em problemas de validação.
- Depuração e melhor compreensão de vários erros retornados durante a execução.

Ao criar um script de fluxo de dados para usar com o PowerShell ou uma API, você deve recolher o texto formatado em uma única linha. Você pode manter guias e novas linhas como caracteres de escape. Mas o texto deve ser formatado para se ajustar dentro de uma propriedade JSON. Há um botão na interface do usuário do editor de scripts, na parte inferior, que formatará o script como uma única linha para você.

![botão Copiar](media/data-flow/copybutton.png "botão Copiar")

## <a name="how-to-add-transforms"></a>Como adicionar transformações
A adição de transformações requer três etapas básicas: adicionar os dados de transformação de núcleo, redirecionar o fluxo de entrada e, em seguida, rotear novamente o fluxo de saída. Isso pode ser visto mais fácil em um exemplo.
Digamos que comecemos com uma fonte simples para coletar fluxo de dados como o seguinte:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Se decidirmos adicionar uma transformação derivar, primeiro precisamos criar o texto de transformação principal, que tem uma expressão simples para adicionar uma nova coluna em maiúsculas chamada `upperCaseTitle` :
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Em seguida, pegamos o DFS existente e adicionamos a transformação:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

E agora redirecionamos o fluxo de entrada identificando a transformação que queremos que a nova transformação venha após (nesse caso, `source1` ) e copiando o nome do fluxo para a nova transformação:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Por fim, identificamos a transformação que desejamos vir após essa nova transformação e substituo seu fluxo de entrada (nesse caso, `sink1` ) pelo nome do fluxo de saída da nossa nova transformação:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Conceitos básicos do DFS
O DFS é composto por uma série de transformações conectadas, incluindo fontes, coletores e vários outros que podem adicionar novas colunas, filtrar dados, unir dados e muito mais. Normalmente, o script com início com uma ou mais fontes seguidas por muitas transformações e terminando com um ou mais coletores.

Todas as fontes têm a mesma construção básica:
```
source(
  source properties
) ~> source_name
```

Por exemplo, uma fonte simples com três colunas (MovieID, título, gêneros) seria:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Todas as transformações que não sejam fontes têm a mesma construção básica:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Por exemplo, uma transformação derivar simples que usa uma coluna (título) e a substitui por uma versão em maiúsculas seria a seguinte:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

E um coletor sem esquema seria simplesmente:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Trechos de script

Os trechos de script são código compartilhável do script de fluxo de dados que você pode usar para compartilhar entre fluxos de dados. Este vídeo abaixo fala sobre como usar trechos de script e utilizando o script de fluxo de dados para copiar e colar partes do script por trás de seus grafos de fluxo de dados:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Estatísticas de resumo agregadas
Adicione uma transformação Agregação ao seu fluxo de dados chamada "SummaryStats" e cole esse código abaixo para a função de agregação em seu script, substituindo o SummaryStats existente. Isso fornecerá um padrão genérico para estatísticas de resumo do perfil de dados.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Você também pode usar o exemplo abaixo para contar o número de números exclusivos e o número de linhas distintas em seus dados. O exemplo a seguir pode ser colado em um fluxo de dados com transformação de agregação chamada ValueDistAgg. Este exemplo usa uma coluna chamada "title". Certifique-se de substituir "título" pela coluna de cadeia de caracteres em seus dados que você deseja usar para obter contagens de valor.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Incluir todas as colunas em uma agregação
Esse é um padrão de agregação genérico que demonstra como você pode manter as colunas restantes em seus metadados de saída ao criar agregações. Nesse caso, usamos a ```first()``` função para escolher o primeiro valor em cada coluna cujo nome não seja "filme". Para usar isso, crie uma transformação Agregação chamada DistinctRows e cole-a em seu script sobre o script de agregação DistinctRows existente.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Criar impressão digital de hash de linha 
Use esse código em seu script de fluxo de dados para criar uma nova coluna derivada chamada ```DWhash``` que produz um ```sha1``` hash de três colunas.

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

Você também pode usar esse script abaixo para gerar um hash de linha usando todas as colunas presentes em seu fluxo, sem a necessidade de nomear cada coluna:

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>String_agg equivalente
Esse código funcionará como a função T-SQL ```string_agg()``` e irá agregar valores de cadeia de caracteres em uma matriz. Em seguida, você pode converter essa matriz em uma cadeia de caracteres para usar com destinos SQL.

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>Número de contagem de atualizações, upserts, inserções, exclusões
Ao usar uma transformação ALTER Row, talvez você queira contar o número de atualizações, upserts, inserções, que resultam de suas políticas de alteração de linha. Adicione uma transformação Agregação após a alteração de linha e cole esse script de fluxo de dados na definição de agregação para essas contagens.

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>Linha distinta usando todas as colunas
Esse trecho de código adicionará uma nova transformação agregada ao fluxo de dados que usará todas as colunas de entrada, gerará um hash que é usado para o agrupamento para eliminar duplicatas e, em seguida, fornecerá a primeira ocorrência de cada duplicata como saída. Você não precisa nomear explicitamente as colunas, elas serão geradas automaticamente de seu fluxo de dados de entrada.

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>Verificar se há nulos em todas as colunas
Este é um trecho de código que você pode colar em seu fluxo de dados para verificar genericamente todas as suas colunas em busca de valores nulos. Essa técnica aproveita a descompasso de esquema para examinar todas as colunas em todas as linhas e usa uma divisão condicional para separar as linhas com valores nulos das linhas sem nulos. 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

### <a name="automap-schema-drift-with-a-select"></a>Descompasso de esquema mapear com uma seleção
Quando você precisa carregar um esquema de banco de dados existente de um conjunto de colunas de entrada desconhecido ou dinâmico, você deve mapear as colunas do lado direito na transformação do coletor. Isso só é necessário quando você estiver carregando uma tabela existente. Adicione este trecho antes do coletor para criar um SELECT que mapeia automaticamente suas colunas. Deixe o mapeamento do coletor para o mapa automático.

```
select(mapColumn(
        each(match(true()))
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> automap
```

### <a name="persist-column-data-types"></a>Manter tipos de dados da coluna
Adicione esse script dentro de uma definição de coluna derivada para armazenar os nomes de coluna e os tipos de dados do fluxo de dados em um repositório persistente usando um coletor.

```
derive(each(match(type=='string'), $$ = 'string'),
    each(match(type=='integer'), $$ = 'integer'),
    each(match(type=='short'), $$ = 'short'),
    each(match(type=='complex'), $$ = 'complex'),
    each(match(type=='array'), $$ = 'array'),
    each(match(type=='float'), $$ = 'float'),
    each(match(type=='date'), $$ = 'date'),
    each(match(type=='timestamp'), $$ = 'timestamp'),
    each(match(type=='boolean'), $$ = 'boolean'),
    each(match(type=='long'), $$ = 'long'),
    each(match(type=='double'), $$ = 'double')) ~> DerivedColumn1
```

### <a name="fill-down"></a>Preencher
Aqui está como implementar o problema comum de "preenchimento" com conjuntos de dados quando você deseja substituir valores nulos pelo valor do valor não nulo anterior na sequência. Observe que essa operação pode ter implicações de desempenho negativas, pois você deve criar uma janela sintética em todo o conjunto de dados com um valor de categoria "fictício". Além disso, você deve classificar por um valor para criar a sequência de dados apropriada para localizar o valor não nulo anterior. Este trecho de código abaixo cria a categoria sintética como "fictícia" e classifica por uma chave substituta. Você pode remover a chave substituta e usar sua própria chave de classificação específica de dados. Este trecho de código pressupõe que você já adicionou uma transformação de origem chamada ```source1```

```
source1 derive(dummy = 1) ~> DerivedColumn
DerivedColumn keyGenerate(output(sk as long),
    startAt: 1L) ~> SurrogateKey
SurrogateKey window(over(dummy),
    asc(sk, true),
    Rating2 = coalesce(Rating, last(Rating, true()))) ~> Window1
```

### <a name="moving-average"></a>Média móvel
A média móvel pode ser implementada muito facilmente em fluxos de dados usando uma transformação do Windows. Este exemplo abaixo cria uma média de movimentação de 15 dias de preços de estoque para a Microsoft.

```
window(over(stocksymbol),
    asc(Date, true),
    startRowOffset: -7L,
    endRowOffset: 7L,
    FifteenDayMovingAvg = round(avg(Close),2)) ~> Window1
```

## <a name="next-steps"></a>Próximas etapas

Explore os fluxos de dados iniciando com o [artigo Visão geral de fluxos de dados](concepts-data-flow-overview.md)
