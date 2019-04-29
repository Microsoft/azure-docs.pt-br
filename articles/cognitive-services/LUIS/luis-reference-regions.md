---
title: Regiões e pontos de extremidade de publicação
titleSuffix: Azure Cognitive Services
description: 3 regiões de criação e seus portais de dar suporte a todas as regiões de publicação muitos. A região na qual você publica seu aplicativo LUIS corresponde à região ou ao local especificado no portal do Azure quando você cria uma chave do ponto de extremidade LUIS do Azure. Quando você publica um aplicativo, o LUIS gera automaticamente uma URL de ponto de extremidade para a região associada à chave.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 20ea2eb632a6d685351178691cc3d0f58a567902
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599275"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Criação e publicação de regiões e as chaves associadas

Três regiões de criação são compatíveis com os portais de LUIS correspondentes. Para publicar um aplicativo LUIS em mais de uma região, é necessário ter pelo menos uma chave por região. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Regiões de criação do LUIS
Há três portais de criação LUIS, com base na região. É necessário criar e publicar na mesma região. 

|LUIS|Região de criação|Nome da região do Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai]|EUA<br>não Europa<br>não Austrália| `westus`|
|[au.luis.ai][au.luis.ai]|Austrália| `australiaeast`|
|[eu.luis.ai][eu.luis.ai]|Europa|`westeurope`|

Criação de regiões têm [failover regiões emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publicação de regiões e recursos do Azure
O aplicativo é publicado em todas as regiões associadas aos recursos do LUIS adicionados no portal do LUIS. Por exemplo, para um aplicativo criado no [www.luis.ai][www.luis.ai], se você criar um recurso de LUIS ou serviço cognitivo no **westus** e [adicioná-lo para o aplicativo como um recurso ](luis-how-to-azure-subscription.md), o aplicativo for publicado nessa região. 

## <a name="public-apps"></a>Aplicativos públicos
Um aplicativo público é publicado em todas as regiões para que um usuário com uma chave de recurso do LUIS baseada em região possa acessar o aplicativo em qualquer região associada com a chave de recurso.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Regiões de publicação estão vinculados a criação de regiões

O aplicativo da região de criação só pode ser publicado em uma região de publicação correspondente. Se seu aplicativo estiver, no momento, na região de criação incorreta, exporte-o e importe-o para a região de criação correta para sua região de publicação. 

Os aplicativos LUIS criados no https://www.luis.ai podem ser publicados em todos os pontos de extremidade, exceto nas regiões [europeias](#publishing-to-europe) e [australianas](#publishing-to-australia). 

## <a name="publishing-to-europe"></a>Publicando na Europa

Para publicar nas regiões europeias, crie os aplicativos LUIS em https://eu.luis.ai apenas. Se você tentar publicar em outro lugar usando uma chave na região Europa, o LUIS exibirá uma mensagem de aviso. Em vez disso, use https://eu.luis.ai. Os aplicativos LUIS criados em [https://eu.luis.ai][eu.luis.ai] não são migrados automaticamente para outras regiões. Exporte e importe o aplicativo LUIS para migrá-lo.

## <a name="europe-publishing-regions"></a>Regiões de publicação da Europa

 Região global | Região da API de criação e site de criação| Região de publicação e de consulta<br>`API region name`   |  Formato da URL do ponto de extremidade   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| França Central<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Norte da Europa<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa Ocidental<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Sul do Reino Unido<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publicando na Austrália

Para publicar nas regiões australianas, crie os aplicativos LUIS em https://au.luis.ai apenas. Se você tentar publicar em outro lugar usando uma chave na região australiana, o LUIS exibirá uma mensagem de aviso. Em vez disso, use https://au.luis.ai. Os aplicativos LUIS criados em [https://au.luis.ai][au.luis.ai] não são migrados automaticamente para outras regiões. Exporte e importe o aplicativo LUIS para migrá-lo.

## <a name="australia-publishing-regions"></a>Publicando as regiões da Austrália

 Região global | Região da API de criação e site de criação| Região de publicação e de consulta<br>`API region name`   |  Formato da URL do ponto de extremidade   |
|-----|------|------|------|
| [Austrália](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Leste da Austrália<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publicação em outras regiões

Para publicar em outras regiões, você cria LUIS aplicativos no [ https://www.luis.ai ](https://www.luis.ai) somente. 

## <a name="other-publishing-regions"></a>Outras regiões de publicação

 Região global | Região da API de criação e site de criação| Região de publicação e de consulta<br>`API region name`   |  Formato da URL do ponto de extremidade   |
|-----|------|------|------|
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Índia Central<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Ásia Oriental<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Leste do Japão<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Oeste do Japão<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Coreia Central<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Sudeste Asiático<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Canadá Central<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Centro dos EUA<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Leste dos EUA<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | Leste dos EUA 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | Centro-Norte dos EUA<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | Centro-Sul dos Estados Unidos<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Centro-Oeste dos EUA<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | Oeste dos EUA<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Oeste dos EUA 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| América do Sul | `westus`<br>[www.luis.ai][www.luis.ai] | Sul do Brasil<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Pontos de extremidade

Atualmente, o LUIS tem 2 pontos de extremidade: um para a criação e outro para análise de previsão de consulta.

|Finalidade|URL|
|--|--|
|Criação|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Análise de texto (previsão de consulta)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

A tabela a seguir explica os parâmetros, indicados com chaves `{}`, na tabela anterior.

|Parâmetro|Finalidade|
|--|--|
|region|Região do Azure – a criação e a publicação têm regiões diferentes|
|appID|A ID do aplicativo LUIS usada na rota de URL e encontrada no painel do aplicativo|
|q|texto de expressão enviado do aplicativo cliente como chat bot|

## <a name="failover-regions"></a>Regiões de failover

Cada região tem uma região secundária para failover. Falha na Europa over dentro da Europa e na Austrália faz failover dentro da Austrália.

Criação de regiões têm [failover regiões emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência a entidades predefinidas](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
