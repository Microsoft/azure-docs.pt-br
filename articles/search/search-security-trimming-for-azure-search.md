---
title: Filtros de segurança para os resultados de corte
titleSuffix: Azure Cognitive Search
description: Saiba como implementar privilégios de segurança no nível do documento para os resultados do Azure Pesquisa Cognitiva Search, usando filtros de segurança e identidades de usuário.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 8bd162fcf2011d2ccce716564763e7f54f19ff69
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631796"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Filtros de segurança para aparar resultados no Azure Pesquisa Cognitiva

Você pode aplicar filtros de segurança para cortar os resultados da pesquisa no Azure Pesquisa Cognitiva com base na identidade do usuário. Essa experiência de pesquisa geralmente requer comparar a identidade de qualquer pessoa que solicite a pesquisa em relação a um campo contendo os princípios que têm permissões ao documento. Quando uma correspondência é encontrada, o usuário ou a entidade (como um grupo ou função) tem acesso ao documento.

Uma maneira de obter filtragem de segurança é por meio de uma disjunção complicada de expressões de igualdade: por exemplo, `Id eq 'id1' or Id eq 'id2'` e assim por diante. Essa abordagem está propensa a erros, é de difícil manutenção e, em casos em que a lista contém centenas ou milhares de valores, diminui o tempo de resposta de consulta em muitos segundos. 

Uma abordagem mais simples e rápida é a função `search.in`. Se você usar `search.in(Id, 'id1, id2, ...')`, em vez de uma expressão de igualdade, poderá esperar tempos de resposta inferiores a um segundo.

Este artigo mostra como realizar a filtragem de segurança usando as seguintes etapas:
> [!div class="checklist"]
> * Criar um campo que contenha os identificadores de entidade 
> * Enviar por push ou atualizar os documentos existentes com os identificadores de entidade relevantes
> * Emitir uma solicitação de pesquisa com `search.in``filter`

>[!NOTE]
> O processo de recuperar os identificadores de entidade não é abordado neste documento. Você deve obtê-lo do seu provedor de serviço de identidade.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tenha uma [assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), um[serviço de pesquisa cognitiva do Azure](search-create-service-portal.md)e um [índice](search-what-is-an-index.md).  

## <a name="create-security-field"></a>Criar campo de segurança

Seus documentos devem incluir um campo especificando a quais grupos você tem acesso. Essas informações tornam-se os critérios de filtro com relação aos quais os documentos são selecionados ou rejeitados do conjunto de resultados retornado para o emissor.
Vamos supor que nós temos um índice de arquivos protegidos e cada arquivo pode ser acessado por um conjunto diferente de usuários.

1. Adicione o campo `group_ids` (você pode escolher qualquer nome aqui) como um `Collection(Edm.String)`. Verifique se o campo tem um atributo `filterable` definido como `true` para que os resultados da pesquisa sejam filtrados com base no acesso do usuário. Por exemplo, se você definir o campo `group_ids` como `["group_id1, group_id2"]` para o documento com `file_name` "secured_file_b", somente os usuários que pertençam ao grupo ids "group_id1" ou "group_id2" terão acesso de leitura ao arquivo.
   
   Verifique se o atributo `retrievable` do campo está definido como `false` para que ele não seja retornado como parte da solicitação de pesquisa.

2. Também adicione os campos `file_id` e `file_name` para este exemplo.  

    ```JSON
    {
        "name": "securedfiles",  
        "fields": [
            {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
            {"name": "file_name", "type": "Edm.String"},
            {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
        ]
    }
    ```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Envio de dados por push para seu índice usando a API REST
  
Emita uma solicitação HTTP POST para o ponto de extremidade de URL do índice. O corpo da solicitação HTTP é um objeto JSON que contém os documentos a serem adicionados:

```http
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2020-06-30  
Content-Type: application/json
api-key: [admin key]
```

No corpo da solicitação, especifique o conteúdo dos seus documentos:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Se você precisar atualizar um documento existente com a lista de grupos, poderá usar a ação `merge` ou `mergeOrUpload`:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Para obter detalhes completos sobre como adicionar ou atualizar os documentos, leia [Editar documentos](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="apply-the-security-filter"></a>Aplicar o filtro de segurança

Para cortar documentos com base em acesso `group_ids`, você deverá emitir uma consulta de pesquisa com um filtro `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))`, em que 'group_id1, group_id2,...' são os grupos aos quais o emissor da solicitação de pesquisa pertence.

Esse filtro corresponde a todos os documentos para os quais o campo `group_ids` contém um dos identificadores determinados.
Para obter detalhes completos sobre como pesquisar documentos usando o Azure Pesquisa Cognitiva, você pode ler [documentos de pesquisa](/rest/api/searchservice/search-documents).
Observe que este exemplo mostra como pesquisar documentos usando uma solicitação POST.

Emita a solicitação HTTP POST:

```http
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2020-06-30
Content-Type: application/json  
api-key: [admin or query key]
```

Especifique o filtro no corpo da solicitação:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Você deve obter os documentos de volta, em que `group_ids` contém "group_id1" ou "group_id2". Em outras palavras, você obtém os documentos aos quais o emissor da solicitação tem acesso de leitura.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```

## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu um padrão para filtrar resultados com base na identidade do usuário e na `search.in()` função. Você pode usar essa função para passar identificadores principais para que o usuário solicitante coincida com os identificadores de entidade de segurança associados a cada documento de destino. Quando uma solicitação de pesquisa é manipulada, a função `search.in` filtra os resultados da pesquisa aos quais nenhuma das entidades do usuário têm acesso de leitura. Os identificadores de entidade podem representar itens como grupos de segurança, funções ou até mesmo a própria identidade do usuário.

Para obter um padrão alternativo com base em Active Directory ou para revisitar outros recursos de segurança, consulte os links a seguir.

* [Filtros de segurança para aparar resultados usando identidades de Active Directory](search-security-trimming-for-azure-search-with-aad.md)
* [Segurança no Azure Pesquisa Cognitiva](search-security-overview.md)