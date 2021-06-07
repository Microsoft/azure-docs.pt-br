---
title: Normalização de texto para filtros, facetas, classificação
titleSuffix: Azure Cognitive Search
description: Especifique os normalizadores para campos de texto em um índice para personalizar o comportamento de correspondência de palavra-chave estrita em filtragem, facetamento e classificação.
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: 3e7a33d9213d7af44d2cfc50baa847534618f7e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608947"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>Normalização de texto para filtragem, facetamento e classificação sem diferenciação de maiúsculas e minúsculas

 > [!IMPORTANT]
 > O normalizador está em visualização pública, disponível por meio da **API REST de 2020-06-30-Preview**. Os recursos de visualização são oferecidos no estado em que se encontram, sob termos de uso complementares.

Pesquisar e recuperar documentos de um índice de Pesquisa Cognitiva do Azure requer a correspondência da consulta com o conteúdo do documento. O conteúdo pode ser analisado para produzir tokens para correspondência, pois é o caso quando o `search` parâmetro é usado ou pode ser usado como está para correspondência de palavra-chave estrita, como visto com `$filter` , `facets` e `$orderby` . Essa abordagem tudo ou nada cobre a maioria dos cenários, mas é curta onde o pré-processamento simples, como o uso de maiúsculas e minúsculas, a remoção de acentos, asciifolding e assim por diante, é necessário sem passar pela cadeia de análise inteira.

Considere os seguintes exemplos:

+ `$filter=City eq 'Las Vegas'` retornará apenas documentos que contenham o texto exato "Las Vegas" e excluirá documentos com "LAS VEGAS" e "Las Vegas", que é inadequado quando o caso de uso requer todos os documentos, independentemente da capitalização.

+ `search=*&facet=City,count:5` retornará "Las Vegas", "LAS VEGAS" e "Las Vegas" como valores distintos, apesar de ser a mesma cidade.

+ `search=usa&$orderby=City` retornará as cidades em lexicográfica ordem: "Las Vegas", "Seattle", "Las Vegas", mesmo que a intenção seja ordenar as mesmas cidades, independentemente do caso. 

## <a name="normalizers"></a>Normalizadores

Um *normalizador* é um componente do mecanismo de pesquisa responsável pelo processamento de texto de pré-processamento para correspondência de palavra-chave. Os normalizadores são semelhantes aos analisadores, exceto pelo fato de não serem indexados pela consulta. Algumas das transformações que podem ser obtidas usando os normalizadores são:

+ Converter em letras minúsculas ou maiúsculas.
+ Normalize acentos e sinais diacríticos como ö ou ê para caracteres equivalentes ASCII "o" e "e".
+ Mapear caracteres como `-` e espaço em branco em um caractere especificado pelo usuário.

Os normalizadores podem ser especificados em campos de texto no índice e aplicados na indexação e na execução da consulta.

## <a name="predefined-and-custom-normalizers"></a>Normalizadores predefinidos e personalizados 

O Azure Pesquisa Cognitiva dá suporte a normalizadores predefinidos para casos de uso comuns, juntamente com a capacidade de personalização, conforme necessário.

| Categoria | Descrição |
|----------|-------------|
| [Normalizadores predefinidos](#predefined-normalizers) | Fornecido de forma integrada e pode ser usado sem nenhuma configuração. |
|[Normalizadores personalizados](#add-custom-normalizers) | Para cenários avançados. Requer configuração definida pelo usuário de uma combinação de elementos existentes, consistindo em filtros char e token. <sup>1</sup>|

<sup>(1)</sup> os normalizadores personalizados não especificam criadores, pois os normalizadores sempre produzem um único token.

## <a name="how-to-specify-normalizers"></a>Como especificar os normalizadores

Os normalizadores podem ser especificados por campo em campos de texto ( `Edm.String` e `Collection(Edm.String)` ) que tenham pelo menos uma `filterable` das `sortable` Propriedades, ou `facetable` definidas como true. A configuração de um normalizador é opcional e é `null` por padrão. Recomendamos a avaliação de normalizadores predefinidos antes de configurar um personalizado para facilitar o uso. Tente um normalizador diferente se os resultados não forem esperados.

Os normalizadores só podem ser especificados quando um novo campo é adicionado ao índice. É recomendável avaliar as necessidades de normalização e atribuir normalizadores nos estágios iniciais de desenvolvimento ao descartar e recriar índices é uma rotina. Os normalizadores não podem ser especificados em um campo que já tenha sido criado.

1. Ao criar uma definição de campo no [índice](/rest/api/searchservice/create-index), defina a propriedade  **normalizator** como um dos seguintes: um [normalizador predefinido](#predefined-normalizers) como `lowercase` , ou um normalizador personalizado (definido no mesmo esquema de índice).  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. Os normalizadores personalizados devem ser definidos na seção **[normalizadores]** do índice primeiro e, em seguida, ser atribuídos à definição de campo, conforme mostrado na etapa anterior. Para obter mais informações, consulte [criar índice](/rest/api/searchservice/create-index) e também [Adicionar normalizadores personalizados](#add-custom-normalizers).


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> Para alterar o normalizador de um campo existente, você precisará recompilar o índice inteiramente (não é possível recriar campos individuais).

Uma boa solução para índices de produção, em que a recriação de índices é dispendiosa, é criar um novo campo idêntico ao antigo, mas com o novo normalizador e usá-lo no lugar do antigo. Use [Atualizar índice](/rest/api/searchservice/update-index) para incorporar o novo campo e [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) para populá-lo. Posteriormente, como parte da manutenção planejada do índice, será possível limpar o índice para remover campos obsoletos.

## <a name="add-custom-normalizers"></a>Adicionar normalizadores personalizados

Os normalizadores personalizados são definidos dentro do esquema de índice e podem ser especificados usando a Propriedade Field. A definição do normalizador personalizado inclui um nome, um tipo, um ou mais filtros de caractere e filtros de token. Os filtros de caracteres e filtros de token são os blocos de construção de um normalizador personalizado e responsáveis pelo processamento do texto. Esses filtros são aplicados da esquerda para a direita.

 O `token_filter_name_1` é o nome do filtro de token e `char_filter_name_1` e `char_filter_name_2` são os nomes dos filtros de caracteres (consulte as tabelas filtros de [token com suporte](#supported-token-filters) e filtros de caractere abaixo para obter os valores válidos).

A definição do normalizador é uma parte do índice maior. Confira informações sobre o resto do índice em [Criar API de índice](/rest/api/searchservice/create-index).

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Os normalizadores personalizados podem ser adicionados durante a criação do índice ou mais tarde, atualizando um existente. A adição de um normalizador personalizado a um índice existente exige que o sinalizador **allowIndexDowntime** seja especificado no [índice de atualização](/rest/api/searchservice/update-index) e fará com que o índice fique indisponível por alguns segundos.

## <a name="normalizers-reference"></a>Referência de normalizadores

### <a name="predefined-normalizers"></a>Normalizadores predefinidos
|**Nome**|**Descrição e opções**|  
|-|-|  
|padrão| Aplica minúsculas ao texto seguido por asciifolding.|  
|letras minúsculas| Transforma caracteres em minúsculas.|
|letras maiúsculas| Transforma caracteres em maiúsculas.|
|asciifolding| Transforma os caracteres que não estão no bloco Unicode latino básico em seu equivalente ASCII, se houver. Por exemplo, alterando à.|
|elision| Remove corrotina do início dos tokens.|

### <a name="supported-char-filters"></a>Filtros de caracteres com suporte
Para obter mais detalhes sobre os filtros de caracteres, consulte [referência de filtros de caracteres](index-add-custom-analyzers.md#CharFilter).
+ [correlação](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>Filtros de token com suporte
A lista a seguir mostra os filtros de token com suporte para os normalizadores e é um subconjunto dos filtros de token geral envolvidos na análise léxica. Para obter mais detalhes sobre os filtros, consulte [referência de filtros de token](index-add-custom-analyzers.md#TokenFilters).

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [minúsculas](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [letras maiúsculas](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>Exemplo de normalizador personalizado

O exemplo a seguir ilustra uma definição personalizada do normalizador com filtros de caractere e filtros de token correspondentes. As opções personalizadas para filtros de caracteres e filtros de token são especificadas separadamente como construções nomeadas e, em seguida, referenciadas na definição do normalizador, conforme ilustrado abaixo.

* Um normalizador personalizado "my_custom_normalizer" é definido na `normalizers` seção da definição do índice.
* O normalizador é composto de dois filtros de caracteres e três filtros de token: corrotina, minúsculas e filtro asciifolding personalizado "my_asciifolding".
* O primeiro filtro de caractere "map_dash" substitui todos os traços por sublinhados, enquanto o segundo "remove_whitespace" Remove todos os espaços.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

## <a name="see-also"></a>Confira também
+ [Analisadores para processamento linguístico e de texto](search-analyzers.md)

+ [API REST para pesquisar documentos](/rest/api/searchservice/search-documents) 
