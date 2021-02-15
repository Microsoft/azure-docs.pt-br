---
title: Algoritmo de similaridade de classificação
titleSuffix: Azure Cognitive Search
description: Como definir o algoritmo de similaridade para experimentar o novo algoritmo de similaridade para classificação
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: e2caa09d41abb1842100ed8259e82ec411390ccb
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520622"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Algoritmo de classificação no Azure Pesquisa Cognitiva

> [!IMPORTANT]
> A partir de 15 de julho de 2020, os serviços de pesquisa recém-criados usarão a função de classificação BM25 automaticamente, que provou na maioria dos casos para fornecer classificações de pesquisa que se alinham melhor com as expectativas do usuário do que a classificação padrão atual. Além da classificação superior, o BM25 também permite opções de configuração para ajustar os resultados com base em fatores como o tamanho do documento.  
>
> Com essa alteração, você provavelmente verá pequenas alterações na ordenação dos resultados da pesquisa. Para aqueles que desejam testar o impacto dessa alteração, o algoritmo BM25 está disponível na API – versão 2019-05-06-Preview e em 2020-06-30.  

Este artigo descreve como você pode usar o novo algoritmo de classificação BM25 nos serviços de pesquisa existentes para novos índices criados e consultados usando a API de visualização.

O Azure Pesquisa Cognitiva está no processo de adoção da implementação oficial do Lucene do algoritmo Okapi BM25, o *BM25Similarity*, que substituirá a implementação *ClassicSimilarity* usada anteriormente. Como o algoritmo ClassicSimilarity mais antigo, o BM25Similarity é uma função de recuperação do tipo TF-IDF que usa a frequência de termos (TF) e a frequência de documentos inverso (IDF) como variáveis para calcular pontuações de relevância para cada par de consulta de documento, que é usado para classificação. 

Embora seja conceitualmente semelhante ao algoritmo de similaridade clássico mais antigo, o BM25 usa sua raiz na recuperação de informações do probabilística para melhorá-lo. O BM25 também oferece opções avançadas de personalização, como permitir que o usuário decida como a pontuação de relevância é dimensionada com a frequência de termos correspondentes.

## <a name="how-to-test-bm25-today"></a>Como testar o BM25 hoje mesmo

Ao criar um novo índice, você pode definir uma propriedade de **similaridade** para especificar o algoritmo. Você pode usar o `api-version=2019-05-06-Preview` , conforme mostrado abaixo, ou `api-version=2020-06-30` .

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

A propriedade de **similaridade** é útil durante esse período provisório quando ambos os algoritmos estão disponíveis, somente nos serviços existentes. 

| Propriedade | Descrição |
|----------|-------------|
| similaridade | Opcional. Os valores válidos incluem *"#Microsoft. Azure. Search. ClassicSimilarity"* ou *"#Microsoft. Azure. Search. BM25Similarity"*. <br/> Requer `api-version=2019-05-06-Preview` ou posterior em um serviço de pesquisa criado antes de 15 de julho de 2020. |

Para novos serviços criados após 15 de julho de 2020, BM25 é usado automaticamente e é o único algoritmo de similaridade. Se você tentar definir a **similaridade** como `ClassicSimilarity` em um novo serviço, um erro 400 será retornado porque não há suporte para esse algoritmo em um novo serviço.

Para os serviços existentes criados antes de 15 de julho de 2020, a similaridade clássica permanece o algoritmo padrão. Se a propriedade de **similaridade** for omitida ou definida como NULL, o índice usará o algoritmo clássico. Se você quiser usar o novo algoritmo, será necessário definir a **similaridade** conforme descrito acima.

## <a name="bm25-similarity-parameters"></a>Parâmetros de similaridade de BM25

A similaridade BM25 adiciona dois parâmetros personalizáveis do usuário para controlar a pontuação de relevância calculada.

### <a name="k1"></a>k1

O parâmetro *K1* controla a função de dimensionamento entre a frequência de termos de cada termo correspondente à pontuação de relevância final de um par de consulta de documento.

Um valor igual a zero representa um "modelo binário", em que a contribuição de um único termo de correspondência é a mesma para todos os documentos correspondentes, independentemente de quantas vezes esse termo aparece no texto, enquanto um valor K1 maior permite que a pontuação continue aumentando conforme mais instâncias do mesmo termo forem encontradas no documento. Por padrão, o Azure Pesquisa Cognitiva usa um valor de 1,2 para o parâmetro K1. Usar um valor K1 mais alto pode ser importante nos casos em que esperamos que vários termos façam parte de uma consulta de pesquisa. Nesses casos, poderemos preferir favorecer documentos que correspondam a muitos dos diferentes termos de consulta sendo pesquisados em documentos que correspondem apenas a um único, várias vezes. Por exemplo, ao consultar o índice em busca de documentos que contenham os termos "Apollo spaceflight", podemos querer diminuir a pontuação de um artigo sobre o grego Mythology, que contém o termo "Apollo", em algumas dúzias de vezes, sem mencionar "spaceflight", em comparação com outro artigo que menciona explicitamente "Apollo" e "spaceflight" apenas algumas vezes. 
 
### <a name="b"></a>b

O parâmetro *b* controla como o comprimento de um documento afeta a pontuação de relevância.

Um valor de 0,0 significa que o comprimento do documento não influenciará a pontuação, enquanto um valor de 1,0 significa que o impacto da frequência do termo na pontuação de relevância será normalizado pelo comprimento do documento. O valor padrão usado no Pesquisa Cognitiva do Azure para o parâmetro b é 0,75. Normalizar a frequência de termo pelo comprimento do documento é útil nos casos em que desejamos penalizar documentos mais longos. Em alguns casos, documentos mais longos (como um romance completo) são mais prováveis de conter muitos termos irrelevantes, em comparação com documentos muito menores.

### <a name="setting-k1-and-b-parameters"></a>Definindo os parâmetros K1 e b

Para personalizar os valores b ou K1, basta adicioná-los como propriedades ao objeto de similaridade ao usar BM25:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

O algoritmo de similaridade só pode ser definido no momento da criação do índice. Isso significa que o algoritmo de similaridade que está sendo usado não pode ser alterado para índices existentes. Os parâmetros *"b"* e *"K1"* podem ser modificados ao atualizar uma definição de índice existente que usa BM25. Alterar esses valores em um índice existente colocará o índice offline por pelo menos alguns segundos, fazendo com que as solicitações de indexação e consulta falhem. Por isso, será necessário definir o parâmetro "allowIndexDowntime = true" na cadeia de caracteres de consulta de sua solicitação de atualização:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

## <a name="see-also"></a>Consulte também  

+ [referência da API REST](/rest/api/searchservice/)
+ [Adicionar perfis de pontuação ao índice](index-add-scoring-profiles.md)
+ [Criar API de índice](/rest/api/searchservice/create-index)
+ [SDK do .NET do Azure Cognitive Search](/dotnet/api/overview/azure/search)