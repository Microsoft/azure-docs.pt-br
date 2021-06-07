---
title: Adicionar uma camada de bloco ao Azure Maps Power BI Visual | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a usar a camada de bloco no Visual do Microsoft Azure Maps para Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6ab99f32932f39d5ad140b7a16d16ceae30fff54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896201"
---
# <a name="add-a-tile-layer"></a>Adicionar uma camada de bloco

O recurso camada de bloco, como o recurso camada de referência, permite que dados adicionais sejam sobrepostos no mapa para fornecer mais contexto. As camadas de bloco permitem sobreimpor imagens sobre os blocos de mapa base do Azure Maps. Essa é uma ótima maneira de sobreposição de conjuntos de dados grandes ou complexos, como imagens de drones, ou milhões de linhas.

> [!div class="mx-imgBorder"]
> ![Um mapa que exibe uma camada de bolha acima de uma camada de peça mostrando dados de clima de infravermelho atuais do Azure Maps](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

Uma camada de bloco é carregada em blocos de um servidor. Essas imagens podem ser renderizadas previamente ou processadas dinamicamente. As imagens previamente renderizadas são armazenadas como qualquer outra imagem em um servidor usando uma Convenção de nomenclatura que a camada de bloco compreende. As imagens renderizadas dinamicamente usam um serviço para carregar as imagens perto do tempo real. As camadas de bloco são uma ótima maneira de Visualizar grandes conjuntos de altos no mapa. Uma camada de bloco não só pode ser gerada a partir de uma imagem, os dados vetoriais também podem ser renderizados como uma camada de bloco.

A caixa delimitadora e o intervalo de zoom de onde um serviço de bloco está disponível podem ser passados como configurações para limitar onde os blocos são solicitados. Isso é um aprimoramento de desempenho tanto para o Visual quanto para o serviço de bloco. Abaixo está uma visão geral de todas as configurações disponíveis no painel **formato** que estão disponíveis na seção **camada do bloco** .

| Configuração        | Descrição   |
|----------------|---------------|
| Url            | Uma URL formatada apontando para um serviço de bloco.  |
| Tamanho de bloco      | Um valor inteiro que especifica as dimensões de largura e altura dos blocos.   |
| Associação Norte    | Latitude do norte da caixa delimitadora em que os blocos estão disponíveis. |
| Associado ao sul    | Latitude do Sul da caixa delimitadora em que os blocos estão disponíveis. |
| Ligado ao leste     | Longitude do leste da caixa delimitadora em que os blocos estão disponíveis.  |
| Limite de oeste     | Longitude ocidental da caixa delimitadora em que os blocos estão disponíveis.   |
| Transparência   | Transparência da camada do bloco.   |
| É TMS         | Serviços de mapa de bloco, uma especificação que reverte o eixo de coordenadas Y da camada de peça. |
| Zoom mínimo       | Os blocos de nível de zoom mínimo estão disponíveis. |
| Zoom máximo       | Os blocos de nível de zoom máximo estão disponíveis.  |
| Posição da camada | Especifica a posição da camada em relação a outras camadas do mapa. |

## <a name="tile-url-formatting"></a>Formatação de URL de bloco

Há três convenções de nomenclatura de serviço de bloco diferentes com suporte no Visual Maps do Azure:

-   **X, Y, notação de zoom** -X é a coluna, y é a posição de linha do bloco na grade de blocos e a notação de zoom um valor com base no nível de zoom.
-   **Notação Quadkey** – combina as informações x, y e zoom em um único valor de cadeia de caracteres. Esse valor de cadeia de caracteres torna-se um identificador exclusivo para um único bloco.
-   **Caixa delimitadora** -especifique uma imagem no formato de coordenadas da caixa delimitadora: `{west},{south},{east},{north}` . Esse formato é normalmente usado pelos [serviços de mapeamento da Web (WMS)](https://www.opengeospatial.org/standards/wms).

A URL do bloco uma URL https para um modelo de URL de bloco que usa os seguintes parâmetros:

-   `{x}` - posição X do bloco. Também precisa `{y}` e `{z}`.
-   `{y}` - posição Y do bloco. Também precisa `{x}` e `{z}`.
-   `{z}` -Nível de zoom do bloco. Também precisa `{x}` e `{y}`.
-   `{quadkey}` -Identificador de bloco `quadkey` com base na Convenção de nomenclatura do sistema de blocos do Bing Maps.
-   `{bbox-epsg-3857}` -Uma cadeia de caracteres de caixa delimitadora com o formato `{west},{south},{east},{north}` no sistema de referência espacial do EPSG 3857.

Por exemplo, a seguir está uma URL de bloco formatada para o [serviço de bloco de radar meteorológico](/rest/api/maps/renderv2/getmaptilepreview) no mapas do Azure. Observe que `[subscription-key]` é um espaço reservado para sua chave de assinatura do Azure Maps.

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

Para obter mais informações sobre o sistema de divisão de mapas do Azure, consulte [níveis de zoom e grade de blocos](zoom-levels-and-tile-grid.md).

## <a name="next-steps"></a>Próximas etapas

Adicione mais contexto ao mapa:

> [!div class="nextstepaction"]
> [Mostrar tráfego em tempo real](power-bi-visual-show-real-time-traffic.md)