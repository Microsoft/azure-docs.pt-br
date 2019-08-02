---
title: Pesquisar entidades com a API de Pesquisa de Entidade do Bing
titleSuffix: Azure Cognitive Services
description: Use a API de Pesquisa de Entidade do Bing para extrair e pesquisar entidades e locais nas consultas de pesquisa.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 54c295c353e83c5d7068c30952d1cbf125788f60
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404994"
---
# <a name="searching-for-entities-with-the-bing-entity-api"></a>Pesquisando entidades com a API de Entidade do Bing

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Sugerir termos de pesquisa com a API de Sugestão Automática do Bing

Se você fornecer uma caixa de pesquisa em que o usuário insere seu termo de pesquisa, use a [API de Sugestão Automática do Bing](../../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API retorna cadeias de caracteres de consulta sugeridas com base em termos de pesquisa parciais como os tipos de usuário.

Depois que o usuário insere seu termo de pesquisa, a URL codifica o termo antes de definir o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query). Por exemplo, se o usuário inserir *Pedro Martins*, defina `q` com *Pedro+Martins* ou *Pedro% 20Martins*.

Se o termo de pesquisa contiver um erro de ortografia, a resposta da pesquisa incluirá um objeto [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext). O objeto mostra a ortografia original e a ortografia corrigida usada pelo Bing para a pesquisa.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="the-bing-entity-search-api-response"></a>A resposta da API de Pesquisa de Entidade do Bing

A resposta da API contém um objeto [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse). Se o Bing encontrar uma entidade ou um lugar relevante, o objeto incluirá o campo `entities`, o campo `places` ou ambos. Caso contrário, o objeto de resposta não incluirá nenhum dos campos.
> [!NOTE]
> As respostas de entidade dão suporte a vários mercados, mas a resposta de Locais dá suporte somente a locais de negócios dos EUA. 

O campo `entities` é um objeto [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) que contém uma lista de objetos [Entity](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity) (confira o campo `value`). A lista pode conter uma única entidade dominante, várias entidades de desambiguidade ou ambas. 

Uma entidade dominante é retornada quando o Bing acredita que ela é a única entidade que atende à solicitação (não há nenhuma ambiguidade sobre qual entidade atende à solicitação). Se várias entidades puderem atender à solicitação, a lista conterá mais de uma entidade de desambiguidade. Por exemplo, se a solicitação usar o título genérico de uma franquia de filme, a lista provavelmente conterá entidades de desambiguidade. Porém, se a solicitação especificar um título específico da franquia, a lista provavelmente conterá uma única entidade dominante.

As entidades incluem personalidades conhecidas como cantores, atores, atletas, modelos, etc., locais e pontos de referência como Monte Rainier ou Lincoln Memorial e coisas como uma banana, um goldendoodle, um livro ou um título de filme. O campo [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) contém dicas que identificam o tipo da entidade. Por exemplo, se ela é uma pessoa, um filme, um animal ou uma atração. Para obter uma lista dos possíveis tipos, confira [Tipos de entidade](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

O exemplo a seguir mostra uma resposta que inclui uma entidade dominante e de desambiguidade.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
                "_type": "ContractualRules/LicenseAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "license": {
                    "name": "CC-BY-SA",
                    "url": "https://creativecommons.org/licenses/by-sa/3.0/"
                },
                "licenseNotice": "Text under CC-BY-SA license"
            },
            {
                "_type": "ContractualRules/LinkAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

A entidade inclui um campo `name`, `description` e `image`. Ao exibir esses campos em sua experiência do usuário, você precisa atribuí-los. O campo `contractualRules` contém uma lista de atribuições que precisam ser aplicadas. A regra contratual identifica o campo ao qual a atribuição se aplica. Para obter informações sobre como aplicar a atribuição, confira [Atribuição](#data-attribution).

```json
"contractualRules": [{
    "_type": "ContractualRules/LicenseAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "license": {
        "name": "CC-BY-SA",
        "url": "https://creativecommons.org/licenses/by-sa/3.0/"
    },
    "licenseNotice": "Text under CC-BY-SA license"
},
{
    "_type": "ContractualRules/LinkAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

Quando você exibe as informações de entidade (nome, descrição e imagem), também precisa usar a URL no campo `webSearchUrl` para criar um link para a página dos resultados da pesquisa do Bing que contém a entidade.

## <a name="find-places"></a>Encontrar locais

O campo `places` é um objeto [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) que contém uma lista de objetos [Place](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#place) (confira os [Tipos de entidade](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types) para obter mais informações). A lista contém uma ou mais entidades locais que atendem à solicitação.

Os lugares incluem restaurantes, hotéis ou empresas locais. O campo [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) contém dicas que identificam o tipo da entidade local. A lista contém uma lista de dicas, como Place, LocalBusiness e Restaurant. Cada dica sucessiva na matriz restringe o tipo da entidade. Para obter uma lista dos possíveis tipos, confira [Tipos de entidade](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> As respostas de entidade dão suporte a vários mercados, mas a resposta de Locais dá suporte somente a locais de negócios dos EUA. 

Consultas de entidade com reconhecimento de local como *restaurante próximo ao meu local* exigem o local do usuário para fornecer resultados precisos. As solicitações sempre devem usar os cabeçalhos X-Search-Location e X-MSEdge-ClientIP para especificar o local do usuário. Se o Bing considerar que a consulta pode se beneficiar do local do usuário, ele definirá o campo `askUserForLocation` de [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext) como **true**. 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

Um resultado de local inclui o nome, o endereço, o número de telefone e a URL do local para o site da entidade. Quando você exibe as informações de entidade, também precisa usar a URL no campo `webSearchUrl` para criar um link para a página dos resultados da pesquisa do Bing que contém a entidade.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> Você ou um terceiro em seu nome, não poderá usar, reter, armazenar, armazenar em cache, compartilhar ou distribuir nenhum dado da API de Entidades para fins de teste, desenvolvimento, treinamento, distribuição ou disponibilização de qualquer serviço ou recurso que não seja da Microsoft.  

## <a name="data-attribution"></a>Atribuição de dados

As respostas da API de Entidade do Bing contêm informações pertencentes a terceiros. Você é responsável por garantir o uso apropriado, por exemplo, respeitando qualquer licença Creative Commons da qual sua experiência do usuário pode depender.

Se uma resposta ou um resultado incluir os campos `contractualRules`, `attributions` ou `provider`, os dados precisarão ser atribuídos. Se a resposta não incluir nenhum desses campos, nenhuma atribuição será necessária. Se a resposta incluir o campo `contractualRules` e os campos `attributions` e/ou `provider`, você precisará usar as regras contratuais para atribuir os dados.

O exemplo a seguir mostra uma entidade que inclui uma regra contratual MediaAttribution e uma Image que inclui um campo `provider`. A regra MediaAttribution identifica a imagem como o destino da regra e, portanto, você ignorará o campo `provider` da imagem e, em vez disso, usará a regra MediaAttribution para fornecer a atribuição.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

Se uma regra contratual incluir o campo `targetPropertyName`, a regra se aplicará somente ao campo de destino. Caso contrário, a regra se aplicará ao objeto pai que contém o campo `contractualRules`.

No exemplo a seguir, a regra `LinkAttribution` inclui o campo `targetPropertyName` e, portanto, a regra se aplica ao campo `description`. Para as regras que se aplicam a campos específicos, é necessário incluir uma linha imediatamente após os dados de destino que contenha um hiperlink para o site do provedor. Por exemplo, para atribuir a descrição, inclua uma linha imediatamente após o texto de descrição que contenha um hiperlink para os dados no site do provedor; nesse caso, crie um link para contoso.com.

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>Atribuição de licença

Se a lista de regras contratuais incluir uma regra [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#licenseattribution), será necessário exibir o aviso na linha imediatamente após o conteúdo ao qual a licença se aplica. A regra `LicenseAttribution` usa o campo `targetPropertyName` para identificar a propriedade à qual a licença se aplica.

Veja a seguir um exemplo que inclui uma regra `LicenseAttribution`.

![Atribuição de licença](../media/cognitive-services-bing-entities-api/licenseattribution.png)

O aviso de licença exibido precisa incluir um hiperlink para o site que contenha informações sobre a licença. Normalmente, você transforma o nome da licença em um hiperlink. Por exemplo, se o aviso for **Texto sob a licença CC-BY-SA** e CC-BY-SA for o nome da licença, transforme CC-BY-SA em um hiperlink.

### <a name="link-and-text-attribution"></a>Atribuição de link e texto

As regras [LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#linkattribution) e [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#textattribution) normalmente são usadas para identificar o provedor dos dados. O campo `targetPropertyName` identifica o campo ao qual a regra se aplica.

Para atribuir os provedores, inclua uma linha imediatamente após o conteúdo ao qual as atribuições se aplicam (por exemplo, o campo de destino). A linha deve ser claramente rotulada para indicar que os provedores são a fonte dos dados. Por exemplo, "Dados de: contoso.com". Para regras `LinkAttribution`, é necessário criar um hiperlink para o site do provedor.

Veja a seguir um exemplo que inclui regras `LinkAttribution` e `TextAttribution`.

![Atribuição de link de texto](../media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Atribuição de mídia

Se a entidade incluir uma imagem e você exibi-la, você precisará fornecer um link clickthrough para o site do provedor. Se a entidade incluir uma regra [MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mediaattribution), use a URL da regra para criar o link para clicar. Caso contrário, use a URL incluída no campo `provider` da imagem para criar o link para clicar.

Veja a seguir um exemplo que inclui o campo `provider` e as regras contratuais de uma imagem. Como o exemplo inclui a regra contratual, ignore o campo `provider` da imagem e aplique a regra `MediaAttribution`.

![Atribuição de mídia](../media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Experiência de pesquisa ou semelhante a de uma pesquisa

Assim como ocorre com a API de Pesquisa na Web do Bing, a API de Pesquisa de Entidade do Bing só pode ser usada como resultado de uma consulta ou uma pesquisa direta do usuário ou como resultado de uma ação em um aplicativo ou uma experiência que logicamente possa ser interpretada como uma solicitação de pesquisa do usuário. Para fins de ilustração, veja a seguir alguns exemplos de experiências aceitáveis de pesquisa ou semelhantes a de uma pesquisa.

- O usuário insere uma consulta diretamente em uma caixa de pesquisa em um aplicativo
- O usuário seleciona uma imagem ou um texto específico e solicita “mais informações”
- O usuário solicita um bot de pesquisa sobre um tópico específico
- O usuário permanece em determinado objeto ou entidade em um cenário de tipo de pesquisa visual

Caso não tenha certeza se sua experiência pode ser considerada uma experiência semelhante a de uma pesquisa, é recomendável que você verifique isso com a Microsoft.

## <a name="throttling-requests"></a>Solicitações de limitação

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Próximas etapas

* Experimente um [Início Rápido](../quickstarts/csharp.md) para começar a pesquisar entidades com a API de Pesquisa de Entidade do Bing.
