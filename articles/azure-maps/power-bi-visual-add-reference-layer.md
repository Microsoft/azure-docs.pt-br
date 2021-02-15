---
title: Adicionar uma camada de referência ao Azure Maps Power BI Visual | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a usar a camada de referência no Visual do Microsoft Azure Maps para Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: eb6c9fed42f263ca6cfaa1ea975d31cb8f4a75cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261610"
---
# <a name="add-a-reference-layer"></a>Adicionar uma camada de referência

O recurso de camada de referência permite que um conjunto de texto espacial secundário seja carregado no Visual e sobreposto no mapa para fornecer contexto de adição. Esse conjunto de DataSet é hospedado por Power BI e deve ser um [arquivo geojson](https://wikipedia.org/wiki/GeoJSON) com uma `.json` extensão de `.geojson` arquivo ou.

Para adicionar um arquivo **geojson** como uma camada de referência, vá para o painel **formato** , expanda a seção **camada de referência** e pressione o botão **+ Adicionar arquivo local** .

Depois que um arquivo geojson for adicionado à camada de referência, o nome do arquivo será exibido no lugar do botão **+ Adicionar arquivo local** com um **X** ao lado dele. Pressione o botão **X** para remover os dados do Visual e excluir o arquivo geojson de Power bi.

O mapa a seguir exibe [2016 censo para o Colorado](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson), colorido por população.

> [!div class="mx-imgBorder"]
> ![Um mapa que exibe 2016 censo para Colorado, colorido pela população como uma camada de referência](media/power-bi-visual/reference-layer-CO-census-tract.png)

A seguir estão todas as configurações no painel **formato** que estão disponíveis na seção **camada de referência** .

| Configuração              | Descrição   |
|----------------------|---------------|
| Dados da camada de referência | O arquivo de dados geojson a ser carregado no Visual como uma camada adicional dentro do mapa. O botão **+ Adicionar arquivo local** abre uma caixa de diálogo de arquivo que o usuário pode usar para selecionar um arquivo geojson que tenha uma `.json` extensão de `.geojson` arquivo ou. |

> [!NOTE]
> Nesta visualização do Visual Maps do Azure, a camada de referência carregará apenas os primeiros recursos da forma 5.000 para o mapa. Esse limite será aumentado em uma atualização futura.

## <a name="styling-data-in-a-reference-layer"></a>Estilizando dados em uma camada de referência

As propriedades podem ser adicionadas a cada recurso dentro do arquivo geojson para personalizar como ele é estilizado no mapa. Esse recurso usa o recurso de camada de dados simples no SDK da Web do Azure Maps. Para obter mais informações, consulte este documento sobre [Propriedades de estilo com suporte](spatial-io-add-simple-data-layer.md#default-supported-style-properties). Não há suporte para imagens de ícone personalizado no Visual do Azure Maps como uma precaução de segurança.

Veja a seguir um exemplo de um recurso de ponto geojson que define sua cor exibida como vermelha.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>Próximas etapas

Adicione mais contexto ao mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bloco](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Mostrar tráfego em tempo real](power-bi-visual-show-real-time-traffic.md)
