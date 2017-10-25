---
title: "Perfis de pontuação (API REST do Azure Search Versão 2015-02-28-Preview) | Microsoft Docs"
description: "A Pesquisa do Azure é um serviço de pesquisa de nuvem hospedado que dá suporte ao ajuste de resultados classificados com base em perfis de pontuação definidos pelo usuário."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: bfd62649-13d7-40b3-a8fa-85521a15084d
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.author: heidist
ms.date: 10/27/2016
ms.openlocfilehash: a67637d149a84313270c03d21acf8a9c1870be05
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scoring-profiles-azure-search-rest-api-version-2015-02-28-preview"></a>Perfis de pontuação (API REST do Azure Search Versão 2015-02-28-Preview)
> [!NOTE]
> Este artigo descreve os perfis de pontuação na versão [2015-02-28-Preview](search-api-2015-02-28-preview.md). No momento, não há nenhuma diferença entre a versão `2016-09-01` documentada no [MSDN](http://msdn.microsoft.com/library/azure/mt183328.aspx) e a versão `2015-02-28-Preview` descrita aqui, porém oferecemos este documento mesmo assim para fornecer uma abrangência de documentação em toda a API.
>
>

## <a name="overview"></a>Visão geral
A pontuação refere-se ao cálculo de um resultado de pesquisa para cada item retornado nos resultados de pesquisa. A pontuação é um indicador de relevância de um item no contexto da operação de pesquisa atual. Quanto maior a pontuação, mais relevante será o item. Nos resultados de pesquisa, os itens são classificados na ordem de alto para baixo, com base na pontuação de pesquisa calculada para cada item.

O Azure Search usa a pontuação padrão para calcular uma pontuação inicial, mas você pode personalizar o cálculo com um perfil de pontuação. Os perfis de pontuação oferecem maior controle sobre a classificação de itens nos resultados da pesquisa. Por exemplo, convém aumentar itens com base em seu potencial de receita, promover itens mais recentes ou talvez aumentar itens que estejam no inventário há muito tempo.

Um perfil de pontuação faz parte da definição de índice, composta de campos, funções e parâmetros.

Para dar uma ideia da aparência de um perfil de pontuação, o exemplo a seguir mostra um perfil simples chamado 'geo'. Ele aumenta a itens que têm o termo de pesquisa no campo `hotelName` . Ele também usa a função `distance` para favorecer itens que estão em um raio de dez quilômetros do local atual. Se alguém pesquisar o termo 'estalagem', e 'estalagem' fizer parte do nome do hotel, documentos que incluem hotéis com 'estalagem' aparecerão em posição mais alta nos resultados de pesquisa.

    "scoringProfiles": [
      {
        "name": "geo",
        "text": {
          "weights": { "hotelName": 5 }
        },
        "functions": [
          {
            "type": "distance",
            "boost": 5,
            "fieldName": "location",
            "interpolation": "logarithmic",
            "distance": {
              "referencePointParameter": "currentLocation",
              "boostingDistance": 10
            }
          }
        ]
      }
    ]

Para usar esse perfil de pontuação, sua consulta é formulada para especificar o perfil na cadeia de caracteres de consulta. Na consulta a seguir, observe o parâmetro de consulta `scoringProfile=geo` na solicitação.

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2015-02-28-Preview

Essa consulta pesquisa o termo 'estalagem' e passa o local atual. Observe que essa consulta inclui outros parâmetros, como `scoringParameter`. Os parâmetros de consulta são descritos em [Pesquisar documentos (API do Azure Search)](search-api-2015-02-28-preview.md#SearchDocs).

Clique em [Exemplo](#example) para examinar um exemplo mais detalhado de um perfil de pontuação.

## <a name="what-is-default-scoring"></a>O que é a pontuação padrão?
A pontuação calcula um resultado de pesquisa para cada item em um conjunto de resultados ordenados por classificação. É atribuída uma pontuação de pesquisa a cada item em um conjunto de resultados de pesquisa, e depois eles são classificados do mais alto para o mais baixo. Os itens com as pontuações mais altas são retornados para o aplicativo. Por padrão, os 50 primeiros são retornados, mas você pode usar o parâmetro `$top` para retornar um número maior ou menor de itens (até 1000 em uma única resposta).

Por padrão, uma pontuação de pesquisa é calculada com base nas propriedades estatísticas dos dados e da consulta. O Azure Search localiza documentos que incluem os termos de pesquisa na cadeia de caracteres de consulta (alguns deles ou todos, dependendo do `searchMode`), favorecendo documentos que contêm muitas instâncias do termo de pesquisa. A pontuação de pesquisa aumentará ainda mais se o termo for raro no corpus de dados, mas comum no documento. A base para essa abordagem de cálculo de relevância é conhecida como TF-IDF (ou frequência do termo ‒ frequência inversa do documento).

Supondo que não haja uma classificação personalizada, os resultados são então classificados pela pontuação de pesquisa antes de serem retornados ao aplicativo que fez a chamada. Se `$top` não for especificado, 50 itens com a pontuação de pesquisa mais alta serão retornados.

Os valores de pontuação de pesquisa podem ser repetidos em todo um conjunto de resultados. Por exemplo, você pode ter 10 itens com uma pontuação de 1,2, 20 itens com uma pontuação de 1,0 e 20 itens com uma pontuação de 0,5. Quando várias ocorrências têm a mesma pontuação de pesquisa, a ordenação dos mesmos itens pontuados não é definida e não é estável. Execute a consulta novamente, e você poderá ver itens mudando de posição. Se houver dois itens com uma pontuação idêntica, não há garantia de qual deles aparecerá primeiro.

## <a name="when-to-use-custom-scoring"></a>Quando usar a pontuação personalizado
Você deve criar um ou mais perfis de pontuação quando o comportamento de classificação padrão não é suficiente para atender a seus objetivos de negócios. Por exemplo, você pode decidir que a relevância de pesquisa deve favorecer itens adicionados recentemente. Da mesma forma, você pode ter um campo que contenha a margem de lucro ou algum outro campo indicando o potencial de receita. Aumentar as ocorrências que trazem benefícios para sua empresa pode ser um fator importante na decisão de usar perfis de pontuação.

A ordenação com base em relevância também é implementada por meio de perfis de pontuação. Considere páginas de resultados de pesquisa que você usou no passado e que lhe permitem classificar por preço, data, classificação ou relevância. No Azure Search, perfis de pontuação usam a opção 'relevância'. A definição de relevância é controlada por você, de acordo com objetivos de negócios e com o tipo de experiência de pesquisa que você deseja fornecer.

<a name="example"></a>

## <a name="example"></a>Exemplo
Como observado, a pontuação personalizada é implementada por meio de perfis de pontuação definidos em um esquema de índice.

Este exemplo mostra o esquema de um índice com dois perfis de pontuação (`boostGenre`, `newAndHighlyRated`). Qualquer consulta em relação a esse índice que inclua um dos perfis como um parâmetro de consulta usará o perfil para pontuar o conjunto de resultados.

    {
      "name": "musicstoreindex",
      "fields": [
        { "name": "key", "type": "Edm.String", "key": true },
        { "name": "albumTitle", "type": "Edm.String" },
        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
        { "name": "genre", "type": "Edm.String" },
        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
        { "name": "artistName", "type": "Edm.String" },
        { "name": "orderableOnline", "type": "Edm.Boolean" },
        { "name": "rating", "type": "Edm.Int32" },
        { "name": "tags", "type": "Collection(Edm.String)" },
        { "name": "price", "type": "Edm.Double", "filterable": false },
        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
        { "name": "inventory", "type": "Edm.Int32" },
        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
      ],
      "scoringProfiles": [
        {
          "name": "boostGenre",
          "text": {
            "weights": {
              "albumTitle": 1.5,
              "genre": 5,
              "artistName": 2
            }
          }
        },
        {
          "name": "newAndHighlyRated",
          "functions": [
            {
              "type": "freshness",
              "fieldName": "lastUpdated",
              "boost": 10,
              "interpolation": "quadratic",
              "freshness": {
                "boostingDuration": "P365D"
              }
            },
            {
              "type": "magnitude",
              "fieldName": "rating",
              "boost": 10,
              "interpolation": "linear",
              "magnitude": {
                "boostingRangeStart": 1,
                "boostingRangeEnd": 5,
                "constantBoostBeyondRange": false
              }
            }
          ]
        }
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["albumTitle", "artistName"]
        }
      ]
    }


## <a name="workflow"></a>Fluxo de trabalho
Para implementar um comportamento personalizado de pontuação, adicione um perfil de pontuação ao esquema que define o índice. Você pode ter vários até 16 perfis de pontuação em um índice (consulte [Limites de serviço](search-limits-quotas-capacity.md)), mas é possível especificar somente um perfil de cada vez em qualquer consulta específica.

Comece com o [Modelo](#bkmk_template) fornecido neste tópico.

Forneça um nome. Os perfis de pontuação são opcionais, mas se você adicionar um, o nome será obrigatório. Siga as convenções de nomenclatura para os campos (começa com uma letra, evita caracteres especiais e palavras reservadas). Consulte [Regras de nomenclatura](http://msdn.microsoft.com/library/azure/dn857353.aspx) para obter mais informações.

O corpo do perfil de pontuação é criado com campos ponderados e funções.

### <a name="weights"></a>Pesos
A propriedade `weights` de um perfil de pontuação especifica os pares de nome-valor que atribuem um peso relativo a um campo. No [Exemplo](#example), os campos albumTitle, gênero e artistName são aumentados em 1,5, 5 e 2, respectivamente. Por que o campo gênero aumentou muito mais do que os outros? Se a pesquisa for realizada com dados que são um pouco homogêneos (como é o caso de 'gênero' em `musicstoreindex`), talvez seja necessária uma variação maior nos pesos relativos. Por exemplo, em `musicstoreindex`, 'rock' é exibido como um gênero e em descrições de gênero escritas de forma idêntica. Se você quiser que gênero tenha um peso maior do que a descrição do gênero, o campo gênero precisará ter um peso relativo muito mais alto.

### <a name="functions"></a>Funções
As funções são usadas quando cálculos adicionais são necessários para contextos específicos. Os tipos de função válidos são `freshness`, `magnitude`, `distance` e `tag`. Cada função tem parâmetros que são exclusivos.

* `freshness` deve ser usado quando você deseja aumentar de acordo com o quão novo ou antigo um item é. Essa função só pode ser usada com campos datetime (`Edm.DataTimeOffset`). Observe que o atributo `boostingDuration` é usado apenas com a função de atualização.
* `magnitude` deve ser usado quando você deseja aumentar de acordo com o quão alto ou baixo um valor numérico é. Cenários que exigem essa função incluem aumentar de acordo com a margem de lucro, maior preço, menor preço ou uma contagem de downloads. Você poderá reverter o intervalo de alto para baixo, se desejar o padrão inverso (por exemplo, para aumentar itens com preço inferior mais do que os itens com preço superior). Com um intervalo de preços de $100 a $1, você definiria `boostingRangeStart` como 100 e `boostingRangeEnd` como 1 para aumentar os itens com preço inferior. Essa função só pode ser usada com campos duplo e inteiro.
* `distance` deve ser usado quando você desejar aumentar de acordo com a proximidade ou a localização geográfica. Essa função só pode ser usada com campos `Edm.GeographyPoint` .
* `tag` deve ser usado quando você desejar aumentar de acordo com as marcações em comum entre os documentos e as consultas de pesquisa. Essa função só pode ser usada com campos `Edm.String` e `Collection(Edm.String)`.

#### <a name="rules-for-using-functions"></a>Regras para o uso de funções
* O tipo de função (freshness, magnitude, distance, tag) deve estar em minúsculas.
* As funções não podem incluir valores nulos ou vazios. Especificamente, se incluir o nome do campo, você precisará defini-lo como algo.
* As funções só podem ser aplicadas a campos filtráveis. Consulte [Criar índice](search-api-2015-02-28-preview.md#CreateIndex) para obter mais informações sobre campos filtráveis.
* As funções só podem ser aplicadas a campos que são definidos na coleção de campos de um índice.

Depois que o índice for definido, crie o índice carregando o esquema de índice, seguido de documentos. Consulte [Criar índice](search-api-2015-02-28-preview.md#CreateIndex) e [Adicionar ou atualizar documentos](search-api-2015-02-28-preview.md#AddOrUpdateDocuments) para obter instruções sobre essas operações. Depois que o índice for criado, você deverá ter um perfil de pontuação funcional que funciona com seus dados de pesquisa.

<a name="bkmk_template"></a>

## <a name="template"></a>Modelo
Esta seção mostra a sintaxe e o modelo para perfis de pontuação. Consulte [Referência de atributos de índice](#bkmk_indexref) na próxima seção para obter descrições dos atributos.

    ...
    "scoringProfiles": [
      {
        "name": "name of scoring profile",
        "text": (optional, only applies to searchable fields) {
          "weights": {
            "searchable_field_name": relative_weight_value (positive #'s),
            ...
          }
        },
        "functions": (optional) [
          {
            "type": "magnitude | freshness | distance | tag",
            "boost": # (positive number used as multiplier for raw score != 1),
            "fieldName": "...",
            "interpolation": "constant | linear (default) | quadratic | logarithmic",

            "magnitude": {
              "boostingRangeStart": #,
              "boostingRangeEnd": #,
              "constantBoostBeyondRange": true | false (default)
            }

            // (- or -)

            "freshness": {
              "boostingDuration": "..." (value representing timespan over which boosting occurs)
            }

            // (- or -)

            "distance": {
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
            }

            // (- or -)

            "tag": {
              "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field)
            }
          }
        ],
        "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
      }
    ],
    "defaultScoringProfile": (optional) "...",
    ...

<a name="bkmk_indexref"></a>

## <a name="scoring-profile-property-reference"></a>Referência de propriedades de perfil de pontuação
> [!NOTE]
> Uma função de pontuação somente pode ser aplicada a campos filtráveis.
>
>

| Propriedade | Descrição |
| --- | --- |
| `name` |Obrigatório. Esse é o nome do perfil de pontuação. Ele segue as mesmas convenções de nomenclatura que os campos. Ele deve começar com uma letra, não pode conter pontos, dois-pontos ou símbolos @ e não pode se iniciar com a frase 'azureSearch' (diferencia maiúsculas de minúsculas). |
| `text` |Contém a propriedade Pesos. |
| `weights` |Opcional. Um par de nome-valor que especifica um nome de campo e o peso relativo. O peso relativo deve ser um inteiro positivo ou o número de ponto flutuante. Você pode especificar o nome do campo sem um peso correspondente. Os pesos são usados para indicar a importância de um campo em relação aos outros. |
| `functions` |Opcional. Observe que uma função de pontuação só pode ser aplicada a campos filtráveis. |
| `type` |Necessário para funções de pontuação. Indica o tipo de função a ser usada. Os valores válidos incluem `magnitude`, `freshness`, `distance` e `tag`. Você pode incluir mais de uma função em cada perfil de pontuação. O nome da função deve estar em letras minúsculas. |
| `boost` |Necessário para funções de pontuação. Um número positivo usado como multiplicador para pontuação bruta. Não pode ser igual a 1. |
| `fieldName` |Necessário para funções de pontuação. Uma função de pontuação só pode ser aplicada a campos que fazem parte da coleção de campos do índice e que são filtráveis. Além disso, cada tipo de função introduz restrições adicionais (a atualização é usada com campos datetime, a magnitude com campos de inteiro ou duplo, a distância com campos de local e a marca com campos de cadeia de caracteres ou coleção de cadeias de caracteres). Você só pode especificar um único campo por definição de função. Por exemplo, para usar a magnitude duas vezes no mesmo perfil, você precisaria incluir duas definições de magnitude, uma para cada campo. |
| `interpolation` |Necessário para funções de pontuação. Define a inclinação para a qual o aumento de pontuação ocorre, do início ao fim do intervalo. Os valores válidos incluem `linear` (padrão), `constant`, `quadratic` e `logarithmic`. Confira [Set interpolations](#bkmk_interpolation) (Definir interpolações) para obter detalhes. |
| `magnitude` |A função de pontuação magnitude é usada para alterar a classificação com base no intervalo de valores de um campo numérico. Alguns dos exemplos de uso mais comuns disso são:<ul><li>classificações por estrelas: alterar a pontuação com base no valor do campo "Classificação por estrelas". Quando dois itens forem relevantes, o item com a classificação mais alta será exibido primeiro.</li><li>Margem: quando dois documentos forem relevantes, um varejista poderá aumentar documentos que tenham margens superiores primeiro.</li><li>Contagens de cliques: para aplicativos que acompanham ações de clickthrough para produtos ou páginas, você pode usar a magnitude para aumentar os itens que tendem a obter mais tráfego.</li><li>Contagens de downloads: para aplicativos que acompanham downloads, a função de magnitude permite que você aumente os itens que têm mais downloads.</li></ul> |
| `magnitude:boostingRangeStart` |Define o valor inicial do intervalo em que a magnitude é pontuada. O valor deve ser um inteiro ou um número de ponto flutuante. Para classificações por estrelas de 1 a 4, isso seria 1. Para mais acima de 50% de margens, isso seria 50. |
| `magnitude:boostingRangeEnd` |Define o valor final do intervalo em que a magnitude é pontuada. O valor deve ser um inteiro ou um número de ponto flutuante. Para classificações por estrelas de 1 a 4, isso seria 4. |
| `magnitude:constantBoostBeyondRange` |Os valores válidos são true ou false (padrão). Quando definido como true, o aumento completo continuará a ser aplicado a documentos que tenham um valor para o campo de destino maior do que a extremidade superior do intervalo. Se for false, o aumento dessa função não será aplicado a documentos com um valor para o campo de destino que esteja fora do intervalo. |
| `freshness` |A função de pontuação atualização é usada para alterar as pontuações de classificação para os itens com base nos valores nos campos DateTimeOffset. Por exemplo, um item com uma data mais recente pode ter classificação mais alta do que itens mais antigos. (Observe que também é possível classificar itens, como os eventos de calendário, com datas futuras, de modo que os itens mais próximos à data atual possam ter uma classificação superior do que itens com data mais distantes.) Na versão atual do serviço, uma extremidade do intervalo será corrigida para a hora atual. A outra extremidade é um momento no passado com base no `boostingDuration`. Para aumentar um intervalo de horários no futuro, use um `boostingDuration` com valor negativo. A taxa à qual o aumento é alterado em um intervalo máximo e mínimo é determinada pela Interpolação é aplicada ao perfil de pontuação (consulte a figura abaixo). Para inverter o fator de aumento aplicado, escolha um fator de aumento que seja inferior a 1. |
| `freshness:boostingDuration` |Define um período de expiração após o qual o aumento será interrompido para um documento específico. Consulte [Definir boostingDuration](#bkmk_boostdur) na próxima seção para obter a sintaxe e exemplos. |
| `distance` |A função de pontuação distância é usada para afetar a pontuação de documentos com base em sua distância ou proximidade em relação a um local geográfico de referência. O local de referência é fornecido como parte da consulta em um parâmetro (usando o parâmetro de consulta `scoringParameter` ) como um argumento lon,lat. |
| `distance:referencePointParameter` |Um parâmetro a ser passado em consultas para usar como local de referência. scoringParameter é um parâmetro de consulta. Consulte [Pesquisar documentos](search-api-2015-02-28-preview.md#SearchDocs) para obter descrições dos parâmetros de consulta. |
| `distance:boostingDistance` |Um número que indica a distância em quilômetros do local de referência em que o intervalo de aumento termina. |
| `tag` |A função de pontuação marca é usada para afetar a pontuação de documentos com base em marcas em documentos e consultas de pesquisa. Documentos com marcas em comum com a consulta de pesquisa serão ser aumentados. As marcações para a consulta de pesquisa são fornecidas como um parâmetro de pontuação em cada solicitação de pesquisa (usando o parâmetro de consulta `scoringParameter` ). |
| `tag:tagsParameter` |Um parâmetro a ser passado em consultas para especificar as marcações para uma solicitação específica. `scoringParameter` é um parâmetro de consulta. Consulte [Pesquisar documentos](search-api-2015-02-28-preview.md#SearchDocs) para obter descrições dos parâmetros de consulta. |
| `functionAggregation` |Opcional. Aplicável apenas quando funções são especificadas. Os valores válidos incluem: `sum` (padrão), `average`, `minimum`, `maximum` e `firstMatching`. Uma pontuação de pesquisa é um valor único calculado por meio de diversas variáveis, incluindo várias funções. Esses atributos indicam como os aumentos de todas as funções são combinados em um único aumento agregado que, em seguida, é aplicado à pontuação de documento de base. A pontuação de base é fundamentada no valor tf-idf calculado por meio do documento e da consulta de pesquisa. |
| `defaultScoringProfile` |Ao se executar uma solicitação de pesquisa, se nenhum perfil de pontuação for especificado, a pontuação padrão será usada (somente tf-idf). Um nome de perfil de pontuação padrão pode ser definido aqui, fazendo com que o Azure Search use esse perfil quando nenhum perfil específico for fornecido na solicitação de pesquisa. |

<a name="bkmk_interpolation"></a>

## <a name="set-interpolations"></a>Set interpolations
As interpolações permitem que você defina a inclinação para a qual o aumento de pontuação ocorre, do início ao fim do intervalo. As seguintes interpolações podem ser usadas:

* `Linear`: para itens que estão dentro do intervalo máximo e mínimo, o aumento aplicado ao item será realizado em um período constantemente decrescente. Linear é a interpolação padrão para um perfil de pontuação.
* `Constant`: para itens que estão no intervalo de início e de término, um aumento constante será aplicado aos resultados de classificação.
* `Quadratic`: em comparação com uma interpolação Linear que tem um aumento que diminui constantemente, a opção Quadrática diminuirá inicialmente em um ritmo menor e, em seguida, à medida que se aproximar do intervalo de término, diminuirá em um intervalo muito maior. Essa opção de interpolação não é permitida em funções de pontuação de marca.
* `Logarithmic`: em comparação com uma interpolação Linear que tem um aumento que diminui constantemente, a opção Logarítmica diminuirá inicialmente em um ritmo maior e, em seguida, ao se aproximar do intervalo de término, será reduzida em um intervalo muito menor. Essa opção de interpolação não é permitida em funções de pontuação de marca.

<a name="Figure1"></a> ![][1]

<a name="bkmk_boostdur"></a>

## <a name="set-boostingduration"></a>Definir boostingDuration
`boostingDuration` é um atributo da função de atualização. Você pode usá-lo para definir um período de expiração após o qual o aumento será interrompido para um documento específico. Por exemplo, para aumentar uma linha de produtos ou marca por um período promocional de 10 dias, você especificaria o período de 10 dias como "P10D" para esses documentos. Ou, para aumentar eventos futuros na próxima semana especifique "-P7D".

`boostingDuration` deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um valor de duração ISO 8601). O padrão para isso é: `[-]P[nD][T[nH][nM][nS]]`.

A tabela a seguir fornece vários exemplos.

| Duração | boostingDuration |
| --- | --- |
| 1 dia |"P1D" |
| 2 dias e 12 horas |"P2DT12H" |
| 15 minutos |"PT15M" |
| 30 dias, 5 horas, 10 minutos e 6,334 segundos |"P30DT5H10M6.334S" |

Para obter mais exemplos, consulte [Esquema XML: tipos de dados (site W3.org)](http://www.w3.org/TR/xmlschema11-2/).

**Veja também**
[API REST do Serviço do Azure Search](http://msdn.microsoft.com/library/azure/dn798935.aspx) no MSDN <br/>
[Criar índice (API do Azure Search)](http://msdn.microsoft.com/library/azure/dn798941.aspx) no MSDN<br/>
[Adicionar um perfil de pontuação a um índice de pesquisa](http://msdn.microsoft.com/library/azure/dn798928.aspx) no MSDN<br/>

<!--Image references-->
[1]: ./media/search-api-scoring-profiles-2015-02-28-Preview/scoring_interpolations.png
