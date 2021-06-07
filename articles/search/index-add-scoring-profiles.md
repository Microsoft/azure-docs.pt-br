---
title: Aumentar a classificação de pesquisa usando perfis de Pontuação
titleSuffix: Azure Cognitive Search
description: Aumente as pontuações de classificação de pesquisa para resultados de Pesquisa Cognitiva do Azure adicionando perfis de pontuação.
manager: nitinme
author: shmed
ms.author: ramero
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 97797e309c32c6ea996d5ae1901b9a266a683173
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537626"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Adicionar perfis de pontuação a um índice da Azure Cognitive Search

A *Pontuação* computa uma pontuação de pesquisa para cada item em um conjunto de resultados ordenados de classificação. É atribuída uma pontuação de pesquisa a cada item em um conjunto de resultados de pesquisa, e depois eles são classificados do mais alto para o mais baixo.

 O Pesquisa Cognitiva do Azure usa a pontuação padrão para calcular uma pontuação inicial, mas você pode personalizar o cálculo por meio de um *perfil de Pontuação*. Os perfis de pontuação oferecem maior controle sobre a classificação de itens nos resultados da pesquisa. Por exemplo, convém aumentar itens com base em seu potencial de receita, promover itens mais recentes ou talvez aumentar itens que estejam no inventário há muito tempo.  

 O seguinte segmento de vídeo avança rapidamente para como os perfis de Pontuação funcionam no Azure Pesquisa Cognitiva.
 
> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=463&end=970]

## <a name="scoring-profile-definitions"></a>Definições de perfil de Pontuação

 Um perfil de pontuação faz parte da definição de índice, composta por funções, parâmetros e campos ponderados.  

 Para dar uma ideia da aparência de um perfil de pontuação, o exemplo a seguir mostra um perfil simples chamado 'geo'. Ele aumenta os itens que têm o termo de pesquisa no campo **hotelName**. Ele também usa a função `distance` para favorecer itens que estão em um raio de dez quilômetros do local atual. Se alguém pesquisar o termo "pousada" e "pousada" fizer parte do nome do hotel, os documentos que incluírem hotéis com "pousada" em um raio de 10 KM da localização atual serão exibidos nas posições mais altas nos resultados da pesquisa.  


```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
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
```  


 Para usar esse perfil de pontuação, sua consulta é formulada para especificar o perfil na cadeia de caracteres de consulta. Na consulta abaixo, observe o parâmetro de consulta `scoringProfile=geo` na solicitação.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2020-06-30 
```  

 Essa consulta pesquisa o termo 'estalagem' e passa o local atual. Observe que essa consulta inclui outros parâmetros, como `scoringParameter` . Os parâmetros de consulta são descritos em [Pesquisar documentos &#40;API REST do Azure Pesquisa Cognitiva&#41;](/rest/api/searchservice/Search-Documents).  

 Clique em [Exemplo](#bkmk_ex) para examinar um exemplo mais detalhado de um perfil de pontuação.  

## <a name="what-is-default-scoring"></a>O que é a pontuação padrão?  
 A pontuação calcula um resultado de pesquisa para cada item em um conjunto de resultados ordenados por classificação. É atribuída uma pontuação de pesquisa a cada item em um conjunto de resultados de pesquisa, e depois eles são classificados do mais alto para o mais baixo. Os itens com as pontuações mais altas são retornados para o aplicativo. Por padrão, os 50 primeiros são retornados, mas você pode usar o parâmetro `$top` para retornar um número maior ou menor de itens (até 1000 em uma única resposta).  

A pontuação de pesquisa é calculada com base nas propriedades estatísticas dos dados e da consulta. O Azure Pesquisa Cognitiva localiza documentos que incluem os termos de pesquisa na cadeia de caracteres de consulta (alguns ou todos, dependendo do `searchMode` ), favorecendo documentos que contêm muitas instâncias do termo de pesquisa. A pontuação de pesquisa aumentará ainda mais se o termo for raro no índice de dados, mas comum no documento. A base dessa abordagem para calcular a relevância é conhecida como [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) ou termo Frequency-inverter frequência de documento.  

 Supondo que não haja uma classificação personalizada, os resultados são então classificados pela pontuação de pesquisa antes de serem retornados ao aplicativo que fez a chamada. Se $top não estiver especificado, serão retornados os 50 itens com a maior pontuação de pesquisa.  

 Os valores de pontuação de pesquisa podem ser repetidos em todo um conjunto de resultados. Por exemplo, você pode ter 10 itens com uma pontuação de 1,2, 20 itens com uma pontuação de 1,0 e 20 itens com uma pontuação de 0,5. Quando várias ocorrências têm a mesma pontuação de pesquisa, a ordenação dos mesmos itens pontuados não é definida e não é estável. Execute a consulta novamente, e você poderá ver itens mudando de posição. Se houver dois itens com uma pontuação idêntica, não há garantia de qual deles aparecerá primeiro.  

## <a name="when-to-add-scoring-logic"></a>Quando adicionar a lógica de Pontuação 
 Você deve criar um ou mais perfis de pontuação quando o comportamento de classificação padrão não é suficiente para atender a seus objetivos de negócios. Por exemplo, você pode decidir que a relevância de pesquisa deve favorecer itens adicionados recentemente. Da mesma forma, você pode ter um campo que contenha a margem de lucro ou algum outro campo indicando o potencial de receita. Aumentar as ocorrências que trazem benefícios para sua empresa pode ser um fator importante na decisão de usar perfis de pontuação.  

 A ordenação com base em relevância também é implementada por meio de perfis de pontuação. Considere páginas de resultados de pesquisa que você usou no passado e que lhe permitem classificar por preço, data, classificação ou relevância. No Azure Pesquisa Cognitiva, os perfis de Pontuação orientam a opção ' relevância '. A definição de relevância é controlada por você, de acordo com objetivos de negócios e com o tipo de experiência de pesquisa que você deseja fornecer.  

##  <a name="example"></a><a name="bkmk_ex"></a> Exemplo  
 Conforme observado anteriormente, a pontuação personalizada é implementada por meio de um ou mais perfis de pontuação definidos em um esquema de índice.  

 Este exemplo mostra o esquema de um índice com dois perfis de pontuação (`boostGenre`, `newAndHighlyRated`). Qualquer consulta em relação a esse índice que inclua um dos perfis como um parâmetro de consulta usará o perfil para pontuar o conjunto de resultados.  

```json
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
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="workflow"></a>Fluxo de trabalho  
 Para implementar um comportamento personalizado de pontuação, adicione um perfil de pontuação ao esquema que define o índice. É possível ter até 100 perfis de pontuação em um índice (consulte [Limites de Serviço](search-limits-quotas-capacity.md)), mas você poderá especificar apenas um perfil no momento em qualquer consulta.  

 Comece com o [Modelo](#bkmk_template) fornecido neste tópico.  

 Forneça um nome. Os perfis de pontuação são opcionais, mas se você adicionar um, o nome será obrigatório. Siga as convenções de nomenclatura para os campos (começa com uma letra, evita caracteres especiais e palavras reservadas). Consulte [regras de nomenclatura &#40;pesquisa cognitiva do Azure&#41;](/rest/api/searchservice/naming-rules) para obter a lista completa.  

 O corpo do perfil de pontuação é criado com campos ponderados e funções.  

|||  
|-|-|  
|**Gramatura**| Especificar pares de nome-valor que atribuem um peso relativo a um campo. No [exemplo](#bkmk_ex), os campos campos AlbumTitle, gênero e artistas são aumentados 1,5, 5 e 2, respectivamente. Por que o campo gênero aumentou muito mais do que os outros? Se a pesquisa for conduzida sobre dados relativamente homogêneos (como é o caso de 'gênero' em `musicstoreindex`), uma variação maior nos pesos relativos poderá ser necessária. Por exemplo, em `musicstoreindex`, 'rock' é exibido como um gênero e em descrições de gênero escritas de forma idêntica. Se você quiser que gênero tenha um peso maior do que a descrição do gênero, o campo gênero precisará ter um peso relativo muito mais alto.|  
|**Funções**|Usadas quando cálculos adicionais são necessários para contextos específicos. Os valores válidos são `freshness`, `magnitude`, `distance` e `tag`. Cada função tem parâmetros que são exclusivos.<br /><br /> -   `freshness` devem ser usados quando você deseja aumentar de acordo com a indicação de que um item é novo ou antigo. Essa função somente pode ser usada com campos `datetime` (edm.DataTimeOffset). Observe que o `boostingDuration` atributo é usado apenas com a `freshness` função.<br />-   `magnitude` devem ser usados quando você deseja aumentar de acordo com a indicação de que um valor numérico é alto ou baixo. Cenários que exigem essa função incluem aumentar de acordo com a margem de lucro, maior preço, menor preço ou uma contagem de downloads. Essa função só pode ser usada com campos duplo e inteiro.<br />     Para a função `magnitude`, é possível inverter o intervalo, do maior para o menor, se você quiser o padrão inverso (por exemplo, para dar mais destaque aos itens com preços mais baixos, do que aos itens com preços mais altos). Com um intervalo de preços de $100 a $1, você definiria `boostingRangeStart` como 100 e `boostingRangeEnd` como 1 para aumentar os itens com preço inferior.<br />-   `distance` devem ser usados quando você deseja aumentar de acordo com a proximidade ou a localização geográfica. Essa função só pode ser usada com campos `Edm.GeographyPoint` .<br />-   `tag` devem ser usados quando você deseja aumentar de acordo com marcas em comum entre documentos e consultas de pesquisa. Essa função só pode ser usada com campos `Edm.String` e `Collection(Edm.String)`.<br /><br /> **Regras para o uso de funções**<br /><br /> O tipo de função (`freshness`, `magnitude`, `distance`), `tag` deve ser em minúsculas.<br /><br /> As funções não podem incluir valores nulos ou vazios. Especificamente, se incluir o nome do campo, você precisará defini-lo como algo.<br /><br /> As funções só podem ser aplicadas a campos filtráveis. Consulte [criar índice &#40;API REST do Azure Pesquisa Cognitiva&#41;](/rest/api/searchservice/create-index) para obter mais informações sobre campos filtráveis.<br /><br /> As funções só podem ser aplicadas a campos que são definidos na coleção de campos de um índice.|  

 Depois que o índice for definido, crie o índice carregando o esquema de índice, seguido de documentos. Consulte [criar índice &#40;API REST do azure Pesquisa Cognitiva&#41;](/rest/api/searchservice/create-index) e [Adicionar, atualizar ou excluir documentos &#40;API rest do Azure pesquisa cognitiva&#41;](/rest/api/searchservice/addupdate-or-delete-documents) para obter instruções sobre essas operações. Depois que o índice for criado, você deverá ter um perfil de pontuação funcional que funciona com seus dados de pesquisa.  

##  <a name="template"></a><a name="bkmk_template"></a> Modelos  
 Esta seção mostra a sintaxe e o modelo para perfis de pontuação. Consulte [Referência de atributos de índice](#bkmk_indexref) na próxima seção para obter descrições dos atributos.  

```  
. . .   
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

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...",   
. . .  
```  

##  <a name="index-attributes-reference"></a><a name="bkmk_indexref"></a> Referência de atributos de índice  

> [!NOTE]  
>  Uma função de pontuação somente pode ser aplicada a campos filtráveis.  

|Atributo|Descrição|  
|---------------|-----------------|  
|`name`|Obrigatórios. Esse é o nome do perfil de pontuação. Ele segue as mesmas convenções de nomenclatura que os campos. O nome deve começar com uma letra, não pode conter pontos, dois-pontos ou símbolos @ e não pode iniciar com a frase "azureSearch" (diferencia maiúsculas de minúsculas).|  
|`text`|Contém a propriedade pesos.|  
|`weights`|Opcional. Contém pares de nome-valor que especificam um nome de campo e um peso relativo. O peso relativo deve ser um inteiro positivo ou o número de ponto flutuante.<br /><br /> Os pesos são usados para indicar a importância de um campo pesquisável em relação a outro.|  
|`functions`|Opcional. Uma função de pontuação somente pode ser aplicada a campos filtráveis.|  
|`type`|Necessário para funções de pontuação. Indica o tipo de função a ser usada. Os valores válidos incluem magnitude, atualização, distância e marca. Você pode incluir mais de uma função em cada perfil de pontuação. O nome da função deve estar em letras minúsculas.|  
|`boost`|Necessário para funções de pontuação. Um número positivo usado como multiplicador para pontuação bruta. Não pode ser igual a 1.|  
|`fieldname`|Necessário para funções de pontuação. Uma função de pontuação só pode ser aplicada a campos que fazem parte da coleção de campos do índice e que são filtráveis. Além disso, cada tipo de função introduz restrições adicionais (a atualização é usada com campos datetime, a magnitude com campos de inteiro ou duplo, a distância com campos de local e a marca com campos de cadeia de caracteres ou coleção de cadeias de caracteres). Você só pode especificar um único campo por definição de função. Por exemplo, para usar a magnitude duas vezes no mesmo perfil, você precisaria incluir duas definições de magnitude, uma para cada campo.|  
|`interpolation`|Necessário para funções de pontuação. Define a inclinação para a qual o aumento de pontuação ocorre, do início ao fim do intervalo. Os valores válidos incluem Linear (padrão), Constante, Quadrática e Logarítmica. Confira [Set interpolations](#bkmk_interpolation) (Definir interpolações) para obter detalhes.|  
|`magnitude`|A função de pontuação magnitude é usada para alterar a classificação com base no intervalo de valores de um campo numérico. Alguns dos exemplos de uso mais comuns disso são:<br /><br /> -   **Classificações por estrelas:** Altere a pontuação com base no valor do campo "classificação por estrelas". Quando dois itens forem relevantes, o item com a classificação mais alta será exibido primeiro.<br />-   **Margem:** Quando dois documentos são relevantes, um varejista pode desejar aumentar os documentos que têm margens mais altas primeiro.<br />-   **Contagens de cliques:** Para aplicativos que controlam o clique em ações para produtos ou páginas, você pode usar a magnitude para impulsionar itens que tendem a obter o máximo de tráfego.<br />-   **Contagens de download:** Para aplicativos que acompanham downloads, a função magnitude permite que você aumente os itens que têm mais downloads.|  
|`magnitude` &#124; `boostingRangeStart`|Define o valor inicial do intervalo em que a magnitude é pontuada. O valor deve ser um inteiro ou um número de ponto flutuante. Para classificações por estrelas de 1 a 4, isso seria 1. Para mais acima de 50% de margens, isso seria 50.|  
|`magnitude` &#124; `boostingRangeEnd`|Define o valor final do intervalo em que a magnitude é pontuada. O valor deve ser um inteiro ou um número de ponto flutuante. Para classificações por estrelas de 1 a 4, isso seria 4.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Os valores válidos são true ou false (padrão). Quando definido como true, o aumento completo continuará a ser aplicado a documentos que tenham um valor para o campo de destino maior do que a extremidade superior do intervalo. Se for false, o aumento dessa função não será aplicado a documentos com um valor para o campo de destino que esteja fora do intervalo.|  
|`freshness`|A função de pontuação de atualização é usada para alterar as pontuações de classificação de itens com base nos valores dos campos `DateTimeOffset`. Por exemplo, um item com uma data mais recente pode ter classificação mais alta do que itens mais antigos.<br /><br /> Também é possível classificar itens como eventos de calendário com datas futuras, de forma que os itens mais próximos da apresentação possam ser classificados mais altos do que os itens no futuro.<br /><br /> Na versão atual do serviço, uma extremidade do intervalo será corrigida para a hora atual. A outra extremidade é um momento no passado com base no `boostingDuration`. Para aumentar um intervalo de tempo no futuro, use um negativo `boostingDuration`.<br /><br /> A taxa à qual o aumento é alterado em um intervalo máximo e mínimo é determinada pela Interpolação é aplicada ao perfil de pontuação (consulte a figura abaixo). Para inverter o fator de aumento aplicado, escolha um fator de aumento que seja inferior a 1.|  
|`freshness` &#124; `boostingDuration`|Define um período de expiração após o qual o aumento será interrompido para um documento específico. Consulte [Definir boostingDuration](#bkmk_boostdur) na próxima seção para obter a sintaxe e exemplos.|  
|`distance`|A função de pontuação distância é usada para afetar a pontuação de documentos com base em sua distância ou proximidade em relação a um local geográfico de referência. A localização de referência é fornecida como parte da consulta em um parâmetro (usando a opção de cadeia de caracteres `scoringParameterquery`) como um argumento lon,lat.|  
|`distance` &#124; `referencePointParameter`|Um parâmetro a ser passado em consultas para usar como local de referência. `scoringParameter` é um parâmetro de consulta. Consulte [Pesquisar documentos &#40;API REST do Azure Pesquisa Cognitiva&#41;](/rest/api/searchservice/Search-Documents) para obter descrições dos parâmetros de consulta.|  
|`distance` &#124; `boostingDistance`|Um número que indica a distância em quilômetros do local de referência em que o intervalo de aumento termina.|  
|`tag`|A função de pontuação marca é usada para afetar a pontuação de documentos com base em marcas em documentos e consultas de pesquisa. Documentos com marcas em comum com a consulta de pesquisa serão ser aumentados. As marcações para a consulta de pesquisa são fornecidas como um parâmetro de pontuação em cada solicitação de pesquisa (usando o parâmetro de consulta `scoringParameterquery` ).|  
|`tag` &#124; `tagsParameter`|Um parâmetro a ser passado em consultas para especificar as marcações para uma solicitação específica. `scoringParameter` é um parâmetro de consulta. Consulte [Pesquisar documentos &#40;API REST do Azure Pesquisa Cognitiva&#41;](/rest/api/searchservice/Search-Documents) para obter descrições dos parâmetros de consulta.|  
|`functionAggregation`|Opcional. Aplicável apenas quando funções são especificadas. Os valores válidos incluem: soma (padrão), média, mínimo, máximo e firstMatching. Uma pontuação de pesquisa é um valor único calculado por meio de diversas variáveis, incluindo várias funções. Esse atributo indica como os aumentos de todas as funções são combinados em um único aumento agregado que é aplicado à pontuação do documento base. A pontuação base é baseada no valor [TF-IDF](http://www.tfidf.com/) computado do documento e da consulta de pesquisa.|  
|`defaultScoringProfile`|Ao executar uma solicitação de pesquisa, se nenhum perfil de pontuação for especificado, a pontuação padrão será usada ([TF-IDF](http://www.tfidf.com/) somente).<br /><br /> Um nome de perfil de Pontuação padrão pode ser definido aqui, fazendo com que o Azure Pesquisa Cognitiva Use esse perfil quando nenhum perfil específico é fornecido na solicitação de pesquisa.|  

##  <a name="set-interpolations"></a><a name="bkmk_interpolation"></a> Definir interpolações  
 As interpolações permitem que você defina a forma da inclinação usada para pontuação. Como a pontuação é decrescente, a inclinação está sempre diminuindo, mas é a interpolação que determina a curva da inclinação descendente. As seguintes interpolações podem ser usadas:  

| Interpola | Descrição |  
|-|-|  
|`linear`|Para itens que estão dentro do intervalo máximo e mínimo, o aumento aplicado ao item será feito em uma quantidade constantemente decrescente. Linear é a interpolação padrão para um perfil de pontuação.|  
|`constant`|Para itens que estão dentro do intervalo inicial e final, um aumento constante será aplicado aos resultados de classificação.|  
|`quadratic`|Em comparação com uma interpolação Linear que tem um aumento constantemente decrescente, a Quadrática inicialmente diminuirá em menor ritmo e, em seguida, à medida que se aproximar do intervalo final, diminuirá em um intervalo muito maior. Essa opção de interpolação não é permitida em funções de pontuação de marca.|  
|`logarithmic`|Em comparação com uma interpolação Linear que tem um aumento constantemente decrescente, a Logarítmica inicialmente diminuirá em um ritmo maior e, em seguida, à medida que se aproximar do intervalo final, diminuirá em um intervalo muito menor. Essa opção de interpolação não é permitida em funções de pontuação de marca.|  

 ![Constante, linear, quadrático log10 linhas no gráfico](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="set-boostingduration"></a><a name="bkmk_boostdur"></a> Definir boostingDuration  
 `boostingDuration` é um atributo da função `freshness`. Você pode usá-lo para definir um período de expiração após o qual o aumento será interrompido para um documento específico. Por exemplo, para aumentar uma linha de produtos ou marca por um período promocional de 10 dias, você especificaria o período de 10 dias como "P10D" para esses documentos.  

 `boostingDuration` deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um valor de duração ISO 8601). O padrão para isso é: "P [nD] [T [nH] [nM] [nS]]".  

 A tabela a seguir fornece vários exemplos.  

|Duration|boostingDuration|  
|--------------|----------------------|  
|1 dia|"P1D"|  
|2 dias e 12 horas|"P2DT12H"|  
|15 minutos|"PT15M"|  
|30 dias, 5 horas, 10 minutos e 6,334 segundos|"P30DT5H10M6.334S"|  

 Para obter mais exemplos, consulte [Esquema XML: tipos de dados (site W3.org)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Confira também  

+ [Referência da API REST](/rest/api/searchservice/)
+ [Criar API de índice](/rest/api/searchservice/create-index)
+ [SDK do .NET do Azure Cognitive Search](/dotnet/api/overview/azure/search?)