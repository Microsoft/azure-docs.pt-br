---
title: 'Início Rápido: Criar, carregar e consultar um índice usando o PowerShell e a API REST - Azure Search'
description: Criar, carregar e consultar um índice usando o PowerShell Invoke-RestMethod e a API REST do Azure Search.
ms.date: 04/08/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 2deba4bf941d561fcef7c2dff804646732e7ce24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817154"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell-and-the-rest-api"></a>Início Rápido: Criar um índice de Azure Search usando o PowerShell e a API REST
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Portal](search-create-index-portal.md)
> 

Este artigo orienta você pelo processo de criar, carregar e consultar uma Azure Search [índice](search-what-is-an-index.md) usando o PowerShell e o [API de REST do serviço Azure Search](https://docs.microsoft.com/rest/api/searchservice/). A definição de índice e o conteúdo pesquisável é fornecido no corpo da solicitação como conteúdo JSON bem formado.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços e as ferramentas a seguir são usados neste Início Rápido. 

[Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. É possível usar um serviço gratuito para este início rápido. 

[PowerShell 5.1 ou posterior](https://github.com/PowerShell/PowerShell), usando [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) para etapas sequenciais e interativas.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas REST exigem a URL do serviço e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou o Azure Search à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

2. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-fiddler/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="connect-to-azure-search"></a>Conectar-se ao Azure Search

No PowerShell, crie uma **$headers** objeto para armazenar o tipo de conteúdo e a chave de API. Você só precisa definir esse cabeçalho de uma vez para a duração da sessão, mas ela será adicionada a cada solicitação. 

```powershell
$headers = @{
   'api-key' = '<your-admin-api-key>'
   'Content-Type' = 'application/json' 
   'Accept' = 'application/json' }
```

Criar uma **$url** coleção de índices do objeto que especifica o serviço. O `mydemo` nome do serviço destina-se como um espaço reservado. Substitua por um serviço de pesquisa válido em uma assinatura atual em todo este exemplo.

```powershell
$url = "https://mydemo.search.windows.net/indexes?api-version=2017-11-11"
```

Execute **Invoke-RestMethod** para enviar uma solicitação GET para o serviço e verifique se a conexão. Adicione **ConvertTo-Json** para que você pode exibir as respostas enviadas do serviço.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

Se o serviço está vazio e não tiver nenhum índice, os resultados são semelhantes ao exemplo a seguir. Caso contrário, você verá uma representação JSON de definições de índice.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
    "value":  [

              ]
}
```

## <a name="1---create-an-index"></a>1 - Criar um índice

A menos que você estiver usando o portal, um índice deve existir no serviço antes de carregar dados. Esta etapa define o índice e envia por push para o serviço. O [criar índice (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index) é usado para esta etapa.

Os elementos necessários de um índice incluem um nome e uma coleção de campos. A coleção de campos define a estrutura de um *documento*. Cada campo tem um nome, tipo e atributos que determinam como ele é usado (por exemplo, se ele é texto completo pesquisável, filtrável ou recuperável nos resultados da pesquisa). Dentro de um índice, um dos campos do tipo `Edm.String` deve ser designado como o *chave* para a identidade do documento.

Esse índice é chamado "hotéis" e tem as definições de campo que você vê abaixo. Especifica a definição de índice de um [analisador de idioma](index-add-language-analyzers.md) para o `description_fr` campo porque ele se destina a armazenar texto em francês, que adicionaremos um exemplo mais adiante.

Cole este exemplo no PowerShell para criar uma **$body** objeto que contém o esquema de índice.

```powershell
$body = @"
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
"@
```

Definir o URI para a coleção de índices em seu serviço e o *hotéis* índice.

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11"
```

Execute o comando com **$url**, **$headers**, e **$body** para criar o índice no serviço. 

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
```
Resultados devem ser semelhantes a este (truncado para os primeiros dois campos para fins de brevidade):

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag":  "\"0x8D6A99E2DED96B0\"",
    "name":  "hotels",
    "defaultScoringProfile":  null,
    "fields":  [
                   {
                       "name":  "hotelId",
                       "type":  "Edm.String",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  false,
                       "facetable":  false,
                       "key":  true,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
                   {
                       "name":  "baseRate",
                       "type":  "Edm.Double",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  true,
                       "facetable":  true,
                       "key":  false,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
. . .
```

> [!Tip]
> Para verificação, você pode também verificar a lista de índices no portal do ou executar novamente o comando usado para verificar a conexão de serviço para ver os *hotéis* índice listados na coleção de índices.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Carregar documentos

Para enviar por push documentos, use uma solicitação HTTP POST para o ponto de extremidade de URL do seu índice. A API REST para essa tarefa é [adicionar, atualizar ou excluir documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

Cole este exemplo no PowerShell para criar uma **$body** objeto que contém os documentos que você deseja carregar. 

Essa solicitação inclui dois completo e um registro parcial. O registro parcial demonstra que você pode carregar documentos incompletos. O `@search.action` parâmetro especifica como a indexação é feita. Os valores válidos incluem carregar, mesclar, mergeOrUpload e delete. O comportamento de mergeOrUpload ou cria um novo documento para hotelId = 3 ou atualiza o conteúdo se ele já existe.

```powershell
$body = @"
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        }
    ]
}
"@
```

Definir o ponto de extremidade o *hotéis* coleção docs e incluir a operação de índice (índices/hotéis/docs/index).

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11"
```

Execute o comando com **$url**, **$headers**, e **$body** para carregar documentos em um índice de hotéis.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
```
Resultados devem ser semelhantes ao exemplo a seguir. Você deve ver um código de status de 201. Para obter uma descrição de todos os códigos de status, consulte [códigos de status HTTP (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2017_11_11.IndexResult)",
    "value":  [
                  {
                      "key":  "1",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  },
                  {
                      "key":  "2",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  },
                  {
                      "key":  "3",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  }
              ]
}
```

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Esta etapa mostra como consultar um índice usando o [API de pesquisa de documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Definir o ponto de extremidade o *hotéis* coleção docs e adicione um **pesquisa** parâmetro para incluir cadeias de caracteres de consulta. Essa cadeia de caracteres é uma pesquisa vazia e retorna uma lista de todos os documentos de unranked.

```powershell
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*'
```

Execute o comando para enviar o **$url** para o serviço.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

Resultados devem ser semelhantes à seguinte saída.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#docs(*)",
    "value":  [
                  {
                      "@search.score":  1.0,
                      "hotelId":  "1",
                      "baseRate":  199.0,
                      "description":  "Best hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Fancy Stay",
                      "category":  "Luxury",
                      "tags":  "pool view wifi concierge",
                      "parkingIncluded":  false,
                      "smokingAllowed":  false,
                      "lastRenovationDate":  "2010-06-27T00:00:00Z",
                      "rating":  5,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "2",
                      "baseRate":  79.99,
                      "description":  "Cheapest hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Roach Motel",
                      "category":  "Budget",
                      "tags":  "motel budget",
                      "parkingIncluded":  true,
                      "smokingAllowed":  true,
                      "lastRenovationDate":  "1982-04-28T00:00:00Z",
                      "rating":  1,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "3",
                      "baseRate":  129.99,
                      "description":  "Close to town hall and the river",
                      "description_fr":  null,
                      "hotelName":  null,
                      "category":  null,
                      "tags":  "",
                      "parkingIncluded":  null,
                      "smokingAllowed":  null,
                      "lastRenovationDate":  null,
                      "rating":  null,
                      "location":  null
                  }
              ]
}
```

Experimente alguns outros exemplos de consultas para ter uma noção do que a sintaxe. Você pode fazer uma pesquisa de cadeia de caracteres, consultas $filter textuais, limitar o conjunto de resultados, o escopo de pesquisa para campos específicos e muito mais.

```powershell
# Query example 1
# Search the entire index for the term 'budget'
# Return only the `hotelName` field, "Roach hotel"
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=budget&$select=hotelName'

# Query example 2 
# Apply a filter to the index to find hotels cheaper than $150 per night
# Returns the `hotelId` and `description`. Two documents match.
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$filter=baseRate lt 150&$select=hotelId,description'

# Query example 3
# Search the entire index, order by a specific field (`lastRenovationDate`) in descending order
# Take the top two results, and show only `hotelName` and `lastRenovationDate`
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate'
```
## <a name="clean-up"></a>Limpar 

Você deve excluir o índice se você não precisar mais dela. Um serviço gratuito é limitado a três índices. Você talvez queira excluir quaisquer índices que não estiver usando ativamente para que você possa percorrer os outros tutoriais.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>Próximas etapas

Tente adicionar descrições de francês para o índice. O exemplo a seguir inclui sequências em francês e demonstra as ações de pesquisa adicionais. Use mergeOrUpload para criar ou adicionar a campos existentes. As cadeias de caracteres a seguir precisam ser codificados em UTF-8.

```json
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "1",
            "description_fr": "Meilleur hôtel en ville"
        },
        {
            "@search.action": "merge",
            "hotelId": "2",
            "description_fr": "Hôtel le moins cher en ville"
        }
    ]
}
```
