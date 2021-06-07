---
title: Regiões de publicação & pontos de extremidade-LUIS
description: A região especificada no portal do Azure é a mesma em que você publicará o aplicativo LUIS e uma URL de ponto de extremidade será gerada para essa mesma região.
ms.service: cognitive-services
ms.subservice: language-understanding
author: aahill
ms.author: aahi
ms.topic: reference
ms.date: 01/21/2021
ms.custom: references_regions
ms.openlocfilehash: 8b43fc472f3247a93414a0b18d9098c6dfb94917
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681600"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Criação e publicação de regiões e as chaves associadas

As regiões de criação do LUIS têm suporte no portal do LUIS. Para publicar um aplicativo LUIS em mais de uma região, é necessário ter pelo menos uma chave por região.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Regiões de criação do LUIS

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]

O LUIS tem um portal que você pode usar independentemente da região, [www.Luis.ai](https://www.luis.ai). Você ainda deve criar e publicar na mesma região.

Regiões de criação têm [regiões de failover emparelhadas](../../best-practices-availability-paired-regions.md)

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Regiões de publicação e recursos do Azure

O aplicativo é publicado em todas as regiões associadas aos recursos do LUIS adicionados no portal do LUIS. Por exemplo, para um aplicativo criado em [www.Luis.ai][www.luis.ai], se você criar um recurso de serviço Luis ou cognitiva na **westus** e [adicioná-lo ao aplicativo como um recurso](luis-how-to-azure-subscription.md), o aplicativo será publicado nessa região.

## <a name="public-apps"></a>Aplicativos públicos
Um aplicativo público é publicado em todas as regiões para que um usuário com uma chave de recurso do LUIS baseada em região possa acessar o aplicativo em qualquer região associada com a chave de recurso.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Regiões de publicação estão vinculadas a regiões de criação

O aplicativo da região de criação só pode ser publicado em uma região de publicação correspondente. Se seu aplicativo estiver, no momento, na região de criação incorreta, exporte-o e importe-o para a região de criação correta para sua região de publicação.

> [!NOTE]
> Os aplicativos LUIS criados no https://www.luis.ai agora podem ser publicados em todos os pontos de extremidade, incluindo as regiões [Europeia](#publishing-to-europe) e [australiana](#publishing-to-australia) .

## <a name="publishing-to-europe"></a>Publicando na Europa

 Região global | Região da API de criação | Região de publicação e de consulta<br>`API region name`   |  Formato da URL do ponto de extremidade   |
|-----|------|------|------|
| Europa | `westeurope`| França Central<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Norte da Europa<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Europa Ocidental<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Sul do Reino Unido<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publicando na Austrália

 Região global | Região da API de criação | Região de publicação e de consulta<br>`API region name`   |  Formato da URL do ponto de extremidade   |
|-----|------|------|------|
| Austrália | `australiaeast` | Leste da Austrália<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="other-publishing-regions"></a>Outras regiões de publicação

 Região global | Região da API de criação | Região de publicação e de consulta<br>`API region name`   |  Formato da URL do ponto de extremidade   |
|-----|------|------|------|
| África | `westus`<br>[www.luis.ai][www.luis.ai]| Norte da África do Sul<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Índia Central<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Leste da Ásia<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Japan East<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Oeste do Japão<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Coreia Central<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Sudeste Asiático<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| DOS EAU norte<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Canadá Central<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Centro dos EUA<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Leste dos EUA<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | Leste dos EUA 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | Centro-Norte dos EUA<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | Centro-Sul dos Estados Unidos<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Centro-Oeste dos EUA<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | Oeste dos EUA<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Oeste dos EUA 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| América do Sul | `westus`<br>[www.luis.ai][www.luis.ai] | Brazil South<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |

## <a name="endpoints"></a>Pontos de extremidade

Saiba mais sobre os [pontos de extremidade de criação e previsão](developer-reference-resource.md).

## <a name="failover-regions"></a>Regiões de failover

Cada região tem uma região secundária para fazer failover para. O failover da Europa na Europa e na Austrália faz failover dentro da Austrália.

Regiões de criação têm [regiões de failover emparelhadas](../../best-practices-availability-paired-regions.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência a entidades predefinidas](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai