---
title: Filtragem, ordenação, paginação de entidades de serviços de mídia - Azure | Microsoft Docs
description: Este artigo discute a filtragem, a classificação e a paginação de entidades dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 28c880e8709074d808a41d9920361eaa2b20ecc4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60732358"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtragem, classificação, paginação de entidades dos Serviços de Mídia

Os Serviços de Mídia dão suporte às seguintes opções de consulta OData para entidades dos Serviços de Mídia v3: 

* $filter 
* $orderby 
* $top 
* $skipToken 

Descrição do operador:

* Eq = igual a
* Ne = não é igual a
* Ge = maior que ou igual a
* Le = menor ou igual a
* Gt = maior que
* Lt = menor que

As propriedades das entidades que são do tipo Datetime estão sempre no formato UTC.

## <a name="page-results"></a>Resultados da página

Se uma resposta de consulta contiver muitos itens, o serviço retornará uma propriedade "\@ odata.nextLink" para obter a próxima página de resultados. Isso pode ser usado para percorrer o conjunto de resultados inteiro. É possível configurar o tamanho da página. O tamanho da página varia por tipo de entidade. Leia as seções individuais a seguir para obter detalhes.

Se entidades são criadas ou excluídas durante a paginação por meio da coleção, as alterações são refletidas nos resultados retornados (se essas alterações estiverem na parte da coleção que não foi baixada). 

> [!TIP]
> Você deve sempre usar o próximo link para enumerar a coleção e não depender de um tamanho de página específico.

## <a name="assets"></a>Ativos

### <a name="filteringordering"></a>Filtragem/pedidos

A tabela a seguir mostra como as opções de filtragem e classificação podem ser aplicadas às propriedades [Asset](https://docs.microsoft.com/rest/api/media/assets): 

|NOME|Filter|Classificar|
|---|---|---|
|ID|||
|Nome|eq, gt, lt| ordem crescente e decrescente|
|properties.alternateId |eq||
|properties.alternateId |eq||
|properties.container |||
|properties.created| eq, gt, lt| ordem crescente e decrescente|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|tipo|||

O exemplo C# a seguir filtra a data da criação:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Paginação 

A paginação é suportada para cada uma das quatro ordens de classificação habilitadas. Atualmente, o tamanho da página é 1000.

#### <a name="c-example"></a>Exemplo de C#

O exemplo de C# a seguir mostra como enumerar todos os ativos na conta.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>Exemplo REST

Considere o seguinte exemplo de onde $skiptoken é usado. Certifique-se de substituir *amstestaccount* pelo seu nome de conta e defina o valor *api-version* valor para a versão mais recente.

Se você solicitar uma lista de ativos como esta:

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Você deve ver uma resposta semelhante a essa:

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Em seguida, você pode solicitar a próxima página, enviando uma solicitação get para:

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Para obter exemplos REST, consulte [Ativos - Lista](https://docs.microsoft.com/rest/api/media/assets/list)

## <a name="content-key-policies"></a>Políticas da Chave de Conteúdo

### <a name="filteringordering"></a>Filtragem/pedidos

A tabela abaixo mostra como essas opções podem ser aplicadas às propriedades [ContentKeyPolicies](https://docs.microsoft.com/rest/api/media/contentkeypolicies): 

|NOME|Filter|Classificar|
|---|---|---|
|ID|||
|Nome|eq, ne, ge, le, gt, lt|ordem crescente e decrescente|
|properties.created |eq, ne, ge, le, gt, lt|ordem crescente e decrescente|
|properties.description |eq, ne, ge, le, gt, lt||
|properties.lastModified|eq, ne, ge, le, gt, lt|ordem crescente e decrescente|
|Properties.Options |||
|properties.policyId|eq, ne||
|tipo|||

### <a name="pagination"></a>Paginação

A paginação é suportada para cada uma das quatro ordens de classificação habilitadas. Atualmente, o tamanho da página é 10.

O seguinte exemplo em C# mostra como enumerar todas as **Políticas de Chave de Conteúdo** na conta.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Para exemplos de REST, consulte [Políticas de Chave de Conteúdo - Lista](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

## <a name="jobs"></a>Trabalhos

### <a name="filteringordering"></a>Filtragem/pedidos

A tabela abaixo mostra como essas opções podem ser aplicadas às propriedades [Jobs](https://docs.microsoft.com/rest/api/media/jobs): 

| NOME    | Filter                        | Classificar |
|---------|-------------------------------|-------|
| Nome                    | eq            | ordem crescente e decrescente|
| properties.state        | eq, ne        |                         |
| properties.created      | gt, ge, lt, le| ordem crescente e decrescente|
| properties.lastModified | gt, ge, lt, le | ordem crescente e decrescente| 

### <a name="pagination"></a>Paginação

A paginação de trabalhos é compatível com os Serviços de Mídia do Microsoft Azure v3.

O exemplo de C# a seguir mostra como enumerar todos os trabalhos na conta.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

Para obter exemplos REST, consulte [Trabalhos - Lista](https://docs.microsoft.com/rest/api/media/jobs/list)

## <a name="streaming-locators"></a>Localizadores de Streaming

### <a name="filteringordering"></a>Filtragem/pedidos

A tabela a seguir mostra como essas opções podem ser aplicadas às propriedades do StreamingLocator: 

|NOME|Filter|Classificar|
|---|---|---|
|ID |||
|Nome|eq, ne, ge, le, gt, lt|ordem crescente e decrescente|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |eq, ne, ge, le, gt, lt|ordem crescente e decrescente|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |eq, ne, ge, le, gt, lt|ordem crescente e decrescente|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|Tipo   |||

### <a name="pagination"></a>Paginação

A paginação é suportada para cada uma das quatro ordens de classificação habilitadas. Atualmente, o tamanho da página é 10.

O seguinte exemplo C# mostra como enumerar todos os StreamingLocators na conta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

Para obter exemplos REST, consulte [localizadores de Streaming - lista](https://docs.microsoft.com/rest/api/media/streaminglocators/list)

## <a name="streaming-policies"></a>Políticas de Streaming

### <a name="filteringordering"></a>Filtragem/pedidos

A tabela a seguir mostra como essas opções podem ser aplicadas às propriedades StreamingPolicy: 

|NOME|Filter|Classificar|
|---|---|---|
|ID|||
|Nome|eq, ne, ge, le, gt, lt|ordem crescente e decrescente|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |eq, ne, ge, le, gt, lt|ordem crescente e decrescente|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|Tipo|||

### <a name="pagination"></a>Paginação

A paginação é suportada para cada uma das quatro ordens de classificação habilitadas. Atualmente, o tamanho da página é 10.

O seguinte exemplo C# mostra como enumerar todos os StreamingPolicies na conta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

Para obter exemplos REST, consulte [Políticas de Streaming - Lista](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)

## <a name="transform"></a>Transformar

### <a name="filteringordering"></a>Filtragem/pedidos

A tabela abaixo mostra como essas opções podem ser aplicadas às propriedades [Transforms](https://docs.microsoft.com/rest/api/media/transforms): 

| NOME    | Filter                        | Classificar |
|---------|-------------------------------|-------|
| Nome                    | eq            | ordem crescente e decrescente|
| properties.created      | gt, ge, lt, le| ordem crescente e decrescente|
| properties.lastModified | gt, ge, lt, le | ordem crescente e decrescente|

## <a name="next-steps"></a>Próximas etapas

[Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
