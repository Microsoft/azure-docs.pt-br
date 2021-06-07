---
title: Filtragem, classificação e paginação de entidades
description: Saiba mais sobre filtragem, ordenação e paginação de entidades dos Serviços de Mídia do Azure v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: overview
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c2748bb879c016ce7d13e73602e9bc0604738dff
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121176"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filtragem, classificação, paginação de entidades dos Serviços de Mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este tópico aborda as opções de consulta OData e o suporte à paginação disponíveis quando você lista entidades dos Serviços de Mídia do Azure v3.

## <a name="considerations"></a>Considerações

* As propriedades das entidades que são do tipo `Datetime` estão sempre no formato UTC.
* O espaço em branco na cadeia de consulta deverá ser codificado em URL antes de você enviar uma solicitação.

## <a name="comparison-operators"></a>Operadores de comparação

Use os seguintes operadores para comparar um campo com um valor constante:

Operadores de igualdade:

- `eq`: teste se um campo é *igual a* um valor constante.
- `ne`: teste se um campo é *diferente de* um valor constante.

Operadores de intervalo:

- `gt`: teste se um campo é *maior que* um valor constante.
- `lt`: teste se um campo é *menor que* um valor constante.
- `ge`: teste se um campo é *superior ou igual a* um valor constante.
- `le`: teste se um campo é *inferior ou igual a* um valor constante.

## <a name="filter"></a>Filtrar

Use `$filter` para fornecer um parâmetro de filtro OData para encontrar apenas os objetos de seu interesse.

O seguinte exemplo de REST filtra o conteúdo para mostrar o valor da `alternateId` de um ativo:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

O seguinte exemplo de C# filtra o conteúdo para mostrar a data da criação do ativo:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Ordenar por

Use `$orderby` para classificar os objetos retornados pelo parâmetro especificado. Por exemplo:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Para classificar os resultados em ordem ascendente ou decrescente, acrescente `asc` ou `desc` ao nome do campo, separado por um espaço. Por exemplo: `$orderby properties/created desc`.

## <a name="skip-token"></a>Token de omissão

Se uma resposta de consulta contiver muitos itens, o serviço retornará um valor `$skiptoken` (`@odata.nextLink`) que você usará para obter a próxima página de resultados. Use-o para percorrer o conjunto de resultados inteiro.

Nos Serviços de Mídia v3, não é possível configurar o tamanho da página. O tamanho da página varia de acordo com o tipo de entidade. Leia as seções individuais a seguir para obter detalhes.

Se entidades forem criadas ou excluídas durante a paginação por meio da coleção, as alterações serão refletidas nos resultados retornados (se essas alterações estiverem na parte da coleção que não foi baixada).

> [!TIP]
> Sempre use `nextLink` para enumerar a coleção e não depender de um tamanho de página específico.
>
> O valor de `nextLink` estará presente somente se houver mais de uma página de entidades.

Considere o exemplo a seguir, no qual `$skiptoken` é usado. Certifique-se de substituir *amstestaccount* pelo seu nome de conta e defina o valor *api-version* valor para a versão mais recente.

Se você solicitar uma lista de ativos como esta:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Receberá uma resposta semelhante a esta:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Em seguida, você pode solicitar a próxima página, enviando uma solicitação get para:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

O exemplo em C# a seguir mostra como enumerar todos os localizadores de streaming na conta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Como usar operadores lógicos para combinar opções de consulta

Os Serviços de Mídia v3 dão suporte aos operadores lógicos **OR** e **AND**. 

O seguinte exemplo de REST verifica o estado do trabalho:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Você constrói a mesma consulta C# desta forma: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Opções de filtragem e ordenação de entidades

A seguinte tabela mostra como aplicar as opções de filtragem e ordenação a diferentes entidades:

|Nome da entidade|Nome da propriedade|Filtrar|Order|
|---|---|---|---|
|[Ativos](/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` e `desc`|
||properties.alternateId |`eq`||
||properties.alternateId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` e `desc`|
|[Políticas de chave de conteúdo](/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.policyId|`eq`, `ne`||
|[Trabalhos](/rest/api/media/jobs)| name  | `eq`            | `asc` e `desc`|
||properties.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` e `desc`| 
|[Localizadores de streaming](/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` e `desc`|
||properties.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Políticas de streaming](/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Transformações](/rest/api/media/transforms)| name | `eq`            | `asc` e `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|

## <a name="next-steps"></a>Próximas etapas

* [Listar ativos](/rest/api/media/assets/list)
* [Listar políticas de chave de conteúdo](/rest/api/media/contentkeypolicies/list)
* [Listar trabalhos](/rest/api/media/jobs/list)
* [Listar políticas de streaming](/rest/api/media/streamingpolicies/list)
* [Listar localizadores de streaming](/rest/api/media/streaminglocators/list)
* [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
* [Cotas e limites](limits-quotas-constraints-reference.md)
