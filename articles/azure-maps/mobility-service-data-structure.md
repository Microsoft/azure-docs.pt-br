---
title: Estruturas de dados dos serviços de mobilidade (versão prévia) no Microsoft Azure Maps
description: Entenda como os dados são organizados em áreas de metrô nos serviços de mobilidade do Azure Maps (versão prévia). Veja quais campos armazenam informações sobre as linhas e paradas de trânsito público.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8ffedc18ba331733723a6293756b60b733cc32cf
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904713"
---
# <a name="data-structures-in-azure-maps-mobility-services-preview"></a>Estruturas de dados nos serviços de mobilidade do Azure Maps (versão prévia) 

> [!IMPORTANT]
> Os serviços de mobilidade do Azure Maps estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Este artigo apresenta o conceito de área de metrô nos [serviços de mobilidade do Azure Maps](/rest/api/maps/mobility). Discutiremos alguns campos comuns que são retornados quando esse serviço é consultado para interrupções e linhas de trânsito públicos. É recomendável ler este artigo antes de desenvolver com as APIs de serviços de mobilidade.

## <a name="metro-area"></a>Área de metrô

Os dados dos serviços de mobilidade (versão prévia) são agrupados por áreas metropolitanas com suporte. Áreas de metrô não seguem limites de cidade. Uma área de metrô pode conter várias cidades, uma cidade com alta densidade e cidades ao redor. Na verdade, um país/região pode ser uma área de metrô. 

O `metroID` é uma ID da área de metrô que pode ser usada para chamar a [API obter informações da área de metrô](/rest/api/maps/mobility/getmetroareainfopreview). Use a API "Get metro" do Azure Maps para solicitar tipos de trânsito, agências de trânsito, alertas ativos e detalhes adicionais para o metro escolhido. Você também pode solicitar as áreas de metrô e metroIDs com suporte. As IDs de área de metrô estão sujeitas a alterações.

**metroID:** 522   **nome:** Seattle-Tacoma-Bellevue

![Seattle – área de metrô](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Parar IDs

As interrupções de trânsito podem ser referenciadas por dois tipos de IDs, a ID de [GFTS (especificação de feed de trânsito geral)](http://gtfs.org/) e a ID de parada do Azure Maps. A ID de GFTS é conhecida como stopKey e a ID de parada do Azure Maps é referida como stopid. Quando se referirem a um trânsito com frequência, você será incentivado a usar a ID de parada do Azure Maps. StopId é mais estável e provavelmente permanecerá o mesmo, desde que a parada física exista. A ID de parada GTFS é atualizada com mais frequência. Por exemplo, GTFS Stop ID pode ser atualizado de acordo com a solicitação do provedor GTFS ou quando uma nova versão do GTFS é liberada. Embora a parada física não tenha sido alterada, a ID de parada GTFS pode ser alterada.

Para começar, você pode solicitar que o trânsito próximo seja interrompido usando a [API de trânsito próximo](/rest/api/maps/mobility/getnearbytransitpreview).

## <a name="line-groups-and-lines"></a>Linhas e grupos de linha

Os serviços de mobilidade (versão prévia) usam um modelo de dados paralelo para linhas e grupos de linhas. Esse modelo é usado para lidar melhor com as alterações herdadas das rotas [GTFS](http://gtfs.org/) e dos dados de viagens.


### <a name="line-groups"></a>Grupos de linhas

Um grupo de linhas é uma entidade, que agrupa todas as linhas que são logicamente parte do mesmo grupo. Normalmente, um grupo de linhas contém duas linhas, uma de ponto a a B e a outra retornando do ponto B para um. Ambas as linhas pertencem à mesma agência de transporte público e têm o mesmo número de linha. No entanto, pode haver casos em que um grupo de linhas tem mais de duas linhas ou apenas uma única linha dentro dela.


### <a name="lines"></a>Linhas

Conforme discutido acima, cada grupo de linhas é composto por um conjunto de linhas. Cada grupo de linhas é composto de duas linhas e cada linha descreve uma direção.  No entanto, há casos em que mais linhas compõem um grupo de linhas. Por exemplo, há uma linha que às vezes faz uma despasseio por uma determinada vizinhança e, às vezes, não. Em ambos os casos, ele opera com o mesmo número de linha. Além disso, um grupo de linhas pode ser composto por uma única linha. Uma linha circular com uma única direção é um grupo de Ling com uma linha.

Para começar, você pode solicitar grupos de linhas usando a [API obter linha de trânsito](/rest/api/maps/mobility/gettransitlineinfopreview).


## <a name="next-steps"></a>Próximas etapas

Saiba como solicitar dados de trânsito usando os serviços de mobilidade (versão prévia):

> [!div class="nextstepaction"]
> [Como solicitar dados de trânsito](how-to-request-transit-data.md)

Saiba como solicitar dados em tempo real usando os serviços de mobilidade (versão prévia):

> [!div class="nextstepaction"]
> [Como solicitar dados em tempo real](how-to-request-real-time-data.md)

Explorar a documentação da API dos serviços de mobilidade do Azure Maps (versão prévia)

> [!div class="nextstepaction"]
> [Documentação da API dos serviços de mobilidade](/rest/api/maps/mobility)