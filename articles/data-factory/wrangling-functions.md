---
title: Funções de Wrangling de dados no Azure Data Factory
description: Uma visão geral das funções de Wrangling de dados disponíveis no Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 659f6527d43e1b45a11fddf774050ca6d42bfe12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896656"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Funções de transformação no Power Query para Wrangling de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

O Wrangling de dados no Azure Data Factory permite que você faça a preparação de dados ágil sem código e Wrangling em escala de nuvem, convertendo Power Query ```M``` scripts no script de fluxo de dados. O ADF se integra com o [Power Query online](/powerquery-m/power-query-m-reference) e disponibiliza Power Query ```M``` funções para Wrangling de dados por meio da execução do Spark usando a infraestrutura do Spark do fluxo de dados. 

> [!NOTE]
> No momento, Power Query no ADF está disponíveis em visualização pública

Atualmente, não há suporte para todas as funções Power Query M para Wrangling de dados, apesar de estarem disponíveis durante a criação. Ao criar seus mashups, a seguinte mensagem de erro será exibida se não houver suporte para uma função:

`UserQuery : Expression.Error: The transformation logic is not supported as it requires dynamic access to rows of data, which cannot be scaled out.`

Abaixo está uma lista de funções de Power Query M com suporte.

## <a name="column-management"></a>Gerenciamento de coluna

* Seleção: [Table. SelectColumns](/powerquery-m/table-selectcolumns)
* Remoção: [Table. RemoveColumns](/powerquery-m/table-removecolumns)
* Renomeando: [Table. RenameColumns](/powerquery-m/table-renamecolumns), [tabela. PrefixColumns](/powerquery-m/table-prefixcolumns), [Table. TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* Reordenando: [Table. ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtragem de linha

Use a função M [Table. SelectRows](/powerquery-m/table-selectrows) para filtrar as seguintes condições:

* Igualdade e desigualdade
* Comparações numéricas, de texto e de data (mas não DateTime)
* Informações numéricas, como [número. ismesmo](/powerquery-m/number-iseven) / [Odd](/powerquery-m/number-iseven)
* Contenção de texto usando [Text. Contains](/powerquery-m/text-contains), [Text. StartsWith](/powerquery-m/text-startswith)ou [Text. EndsWith](/powerquery-m/text-endswith)
* Intervalos de datas incluindo todas as ' [funções de data](/powerquery-m/date-functions)de IsIn' ') 
* Combinações dessas usando as condições and, or ou not

## <a name="adding-and-transforming-columns"></a>Adicionando e transformando colunas

As seguintes funções M adicionam ou transformam colunas: [Table. AddColumn](/powerquery-m/table-addcolumn), [tabela. TransformColumns](/powerquery-m/table-transformcolumns), [Table. replacevalue](/powerquery-m/table-replacevalue), [Table. DuplicateColumn](/powerquery-m/table-duplicatecolumn). Abaixo estão as funções de transformação com suporte.

* Aritmética numérica
* Concatenação de texto
* AndTime de data aritmética (operadores aritméticos, [Data. Subdias](/powerquery-m/date-adddays), [Data. AddMonths](/powerquery-m/date-addmonths), [Data. addtrimestres](/powerquery-m/date-addquarters), [Data. Addsemanas](/powerquery-m/date-addweeks), [Data. AddYears](/powerquery-m/date-addyears))
* As durações podem ser usadas para aritmética de data e hora, mas devem ser transformadas em outro tipo antes de serem gravadas em um coletor (operadores aritméticos, [#duration](/powerquery-m/sharpduration), [duração. dias](/powerquery-m/duration-days), [duração. horas](/powerquery-m/duration-hours), duração. [minutos](/powerquery-m/duration-minutes), [duração. segundos](/powerquery-m/duration-seconds), [duração. TotalDays](/powerquery-m/duration-totaldays), duração. [TotalHours](/powerquery-m/duration-totalhours), [duração. TotalMinutes](/powerquery-m/duration-totalminutes), [duração. TotalSeconds](/powerquery-m/duration-totalseconds))    
* A maioria das funções numéricas padrão, científicas e trigonométricas (todas as funções em [operações](/powerquery-m/number-functions#operations), [arredondamento](/powerquery-m/number-functions#rounding)e [trigonometria](/powerquery-m/number-functions#trigonometry) , *exceto* Number. fatorial, Number. permutates e Number. combinações)
* Substituição ([replacer. ReplaceText](/powerquery-m/replacer-replacetext), [replaceer. replacevalue](/powerquery-m/replacer-replacevalue), [Text. Replace](/powerquery-m/text-replace), [Text. Remove](/powerquery-m/text-remove))
* Extração de texto posicional ([Text. PositionOf](/powerquery-m/text-positionof), [Text. Length](/powerquery-m/text-length), [Text. Start](/powerquery-m/text-start), [Text. end](/powerquery-m/text-end), [Text. middle](/powerquery-m/text-middle), [Text. ReplaceRange](/powerquery-m/text-replacerange), [Text. RemoveRange](/powerquery-m/text-removerange))
* Formatação de texto básica ([Text. Low](/powerquery-m/text-lower), [Text. Upper](/powerquery-m/text-upper), [Text. arrumar](/powerquery-m/text-trim) / [Start](/powerquery-m/text-trimstart) / [end](/powerquery-m/text-trimend), [Text. PadStart](/powerquery-m/text-padstart) / [end](/powerquery-m/text-padend), [Text. Reverse](/powerquery-m/text-reverse))
* Funções de data/hora ([Data. dia](/powerquery-m/date-day), [Data. mês](/powerquery-m/date-month), [Data. ano](/powerquery-m/date-year) [hora. hora](/powerquery-m/time-hour), [hora. minuto](/powerquery-m/time-minute), [hora. segundo](/powerquery-m/time-second), [Data. DayOfWeek](/powerquery-m/date-dayofweek), [Data. DayOfYear](/powerquery-m/date-dayofyear), [Date. DaysInMonth](/powerquery-m/date-daysinmonth))
* Se as expressões (mas as ramificações devem ter tipos correspondentes)
* Filtros de linha como uma coluna lógica
* Constantes de número, texto, lógica, data e DateTime

<a name="mergingjoining-tables"></a>Mesclando/Unindo tabelas
----------------------
* Power Query irá gerar uma junção aninhada (Table. NestedJoin; os usuários também podem gravar manualmente [Table. AddJoinColumn](/powerquery-m/table-addjoincolumn)).
    Os usuários devem expandir a coluna de junção aninhada para uma junção não aninhada (Table. ExpandTableColumn, sem suporte em nenhum outro contexto).
* A tabela de função M   [. Join](/powerquery-m/table-join) pode ser gravada diretamente para evitar a necessidade de uma etapa de expansão adicional, mas o usuário deve garantir que não haja nenhum nome de coluna duplicado entre as tabelas unidas
* Tipos de junção com suporte:   [interno](/powerquery-m/joinkind-inner),   [LeftOuter](/powerquery-m/joinkind-leftouter),   [RightOuter](/powerquery-m/joinkind-rightouter),   [FullOuter](/powerquery-m/joinkind-fullouter)
* Ambos os   [valores. Equals](/powerquery-m/value-equals) e   [Value. NullableEquals](/powerquery-m/value-nullableequals) têm suporte como comparadores de igualdade de chave

## <a name="group-by"></a>Agrupar por

Use [Table. Group](/powerquery-m/table-group) para agregar valores.
* Deve ser usado com uma função de agregação
* Funções de agregação com suporte:   [lista. soma](/powerquery-m/list-sum),   [lista. contagem](/powerquery-m/list-count),   [lista. média](/powerquery-m/list-average),   [lista. mín](/powerquery-m/list-min).,   [lista. máx](/powerquery-m/list-max).,   [lista. i](/powerquery-m/list-standarddeviation), lista.   [primeiro](/powerquery-m/list-first),   [lista. último](/powerquery-m/list-last)

## <a name="sorting"></a>Classificação

Use [Table. Sort](/powerquery-m/table-sort) para classificar valores.

## <a name="reducing-rows"></a>Reduzindo linhas

Manter e remover Top, manter o intervalo (funções de M correspondentes, somente contagens de suporte, não condições: [Table. firstn](/powerquery-m/table-firstn), [tabela. Skip](/powerquery-m/table-skip), [tabela. removefirstn](/powerquery-m/table-removefirstn), [tabela. Range](/powerquery-m/table-range), tabela. [Minnesota](/powerquery-m/table-minn), [Table. MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Funções conhecidas sem suporte

| Função | Status |
| -- | -- |
| Table.PromoteHeaders | Sem suporte. O mesmo resultado pode ser obtido com a configuração "primeira linha como cabeçalho" no conjunto de resultados. |
| Table.CombineColumns | Esse é um cenário comum que não tem suporte direto, mas pode ser obtido com a adição de uma nova coluna que concatena duas colunas especificadas.  Por exemplo, Table. AddColumn (RemoveEmailColumn, "Name", cada [FirstName] & "" & [LastName]) |
| Table.TransformColumnTypes | Isso tem suporte na maioria dos casos. Os cenários a seguir não têm suporte: transformando cadeia de caracteres para tipo de moeda, transformando cadeia de caracteres em tipo de hora, transformando cadeia de caracteres para tipo de porcentagem. |
| Table.NestedJoin | Apenas fazer uma junção resultará em um erro de validação. As colunas devem ser expandidas para que funcionem. |
| Table.Distinct | Não há suporte para a remoção de linhas duplicadas. |
| Table.RemoveLastN | Não há suporte para remover linhas inferiores. |
| Table.RowCount | Sem suporte, mas pode ser obtido com a adição de uma coluna personalizada contendo o valor 1 e, em seguida, a agregação dessa coluna com list. Sum. Table. Group tem suporte. | 
| Tratamento de erros em nível de linha | No momento, não há suporte para o tratamento de erros em nível de linha. Por exemplo, para filtrar valores não numéricos de uma coluna, uma abordagem seria transformar a coluna de texto em um número. Todas as células que não forem transformadas na transformação estarão em um estado de erro e precisarão ser filtradas. Esse cenário não é possível em M escalado horizontalmente. |
| Table.Transpose | Sem suporte |
| Table.Pivot | Sem suporte |

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar um data wrangling Power Query no ADF](wrangling-tutorial.md).
