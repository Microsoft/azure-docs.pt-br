---
title: Modelos de imagem no SDK da Web do Azure Maps | Mapas do Microsoft Azure
description: Saiba como adicionar ícones de imagem e polígonos preenchidos por padrão a mapas usando o SDK da Web do Azure Maps. Exiba a imagem disponível e os modelos de padrão de preenchimento.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 5f455a1132a0f63e1ba3eb5d2a57a1f9bfa9a867
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895674"
---
# <a name="how-to-use-image-templates"></a>Como usar modelos de imagem

As imagens podem ser usadas com marcadores de HTML e várias camadas no SDK da Web do Azure Maps:

 - As camadas de símbolo podem renderizar pontos no mapa com um ícone de imagem. Os símbolos também podem ser renderizados ao longo de um caminho de linhas.
 - Camadas de polígono podem ser renderizadas com uma imagem de padrão de preenchimento. 
 - Marcadores de HTML podem renderizar pontos usando imagens e outros elementos HTML.

Para garantir um bom desempenho com camadas, carregue as imagens no recurso mapear imagem Sprite antes de renderizar. O [íconeoptions](/javascript/api/azure-maps-control/atlas.iconoptions), da SymbolLayer, sobrecarrega algumas imagens de marcador em algumas cores na imagem de mapa Sprite, por padrão. Essas imagens de marcador e mais estão disponíveis como modelos SVG. Eles podem ser usados para criar imagens com escalas personalizadas ou usadas como uma cor primária e secundária do cliente. No total, há 42 modelos de imagem fornecidos: 27 ícones de símbolo e 15 padrões de preenchimento de polígono.

Os modelos de imagem podem ser adicionados à imagem do mapa recursos de Sprite usando a `map.imageSprite.createFromTemplate` função. Essa função permite que até cinco parâmetros sejam passados;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

O `id` é um identificador exclusivo que você cria. O `id` é atribuído à imagem quando é adicionado à imagem de mapas Sprite. Use esse identificador nas camadas para especificar qual recurso de imagem renderizar. `templateName`Especifica qual modelo de imagem usar. A `color` opção define a cor primária da imagem e as `secondaryColor` Opções define a cor secundária da imagem. A `scale` opção dimensiona o modelo de imagem antes de aplicá-lo à imagem Sprite. Quando a imagem é aplicada à imagem Sprite, ela é convertida em um PNG. Para garantir a renderização nítida, é melhor escalar verticalmente o modelo de imagem antes de adicioná-lo ao Sprite, do que para dimensioná-lo em uma camada.

Essa função carrega a imagem de forma assíncrona na imagem Sprite. Portanto, ele retorna uma promessa que você pode aguardar até que essa função seja concluída.

O código a seguir mostra como criar uma imagem de um dos modelos internos e usá-lo com uma camada de símbolo.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Usar um modelo de imagem com uma camada de símbolo

Depois que um modelo de imagem é carregado na imagem de mapa Sprite, ele pode ser renderizado como um símbolo em uma camada de símbolo referenciando a ID de recurso de imagem na `image` opção do `iconOptions` .

O exemplo a seguir renderiza uma camada de símbolo usando o `marker-flat` modelo de imagem com uma cor primária azul-petróleo e uma cor secundária branca. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de símbolo com o modelo de ícone interno" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a camada de símbolo de caneta <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>com o modelo de ícone interno</a> pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Usar um modelo de imagem ao longo de um caminho de linhas

Depois que um modelo de imagem é carregado na imagem de mapa Sprite, ele pode ser renderizado ao longo do caminho de uma linha adicionando um LineString a uma fonte de dados e usando uma camada de símbolo com uma `lineSpacing` opção e referenciando a ID do recurso de imagem na `image` opção de th `iconOptions` . 

O exemplo a seguir renderiza uma linha rosa no mapa e usa uma camada de símbolo usando o `car` modelo de imagem com uma cor primária azul-Anil e uma cor secundária branca. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de linha com modelo de ícone interno" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Veja a camada de linha de caneta <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>com o modelo de ícone interno</a> pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Se o modelo de imagem estiver apontando para cima, defina a `rotation` opção icon da camada de símbolo como 90 se desejar que ela aponte para a mesma direção que a linha.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Usar um modelo de imagem com uma camada de polígono

Depois que um modelo de imagem é carregado na imagem de mapa Sprite, ele pode ser renderizado como um padrão de preenchimento em uma camada de polígono referenciando a ID de recurso de imagem na `fillPattern` opção da camada.

O exemplo a seguir renderiza uma camada de polígono usando o `dot` modelo de imagem com uma cor primária vermelha e uma cor secundária transparente.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Preencher polígono com o modelo de ícone interno" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>polígono preenchimento da caneta com o modelo de ícone interno</a> pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Definir a cor secundária dos padrões de preenchimento torna mais fácil ver o mapa subjacente ainda fornecerá o padrão primário. 

## <a name="use-an-image-template-with-an-html-marker"></a>Usar um modelo de imagem com um marcador HTML

Um modelo de imagem pode ser recuperado usando a `altas.getImageTemplate` função e usado como o conteúdo de um marcador HTML. O modelo pode ser passado para a `htmlContent` opção do marcador e, em seguida, personalizado usando `color` as `secondaryColor` Opções, e `text` .

O exemplo a seguir usa o `marker-arrow` modelo com uma cor primária vermelha, uma cor secundária rosa e um valor de texto de "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Marcador HTML com modelo de ícone interno" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>marcador HTML de caneta com o modelo de ícone interno</a> pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Modelos de imagem também podem ser usados fora do mapa. O funcione getimagetemplate retorna uma cadeia de caracteres SVG com espaços reservados; `{color}`, `{secondaryColor}`, `{scale}`, `{text}`. Substitua esses valores de espaço reservado para criar uma cadeia de caracteres SVG válida. Em seguida, você pode adicionar a cadeia de caracteres SVG diretamente ao DOM HTML ou convertê-la em um URI de dados e inseri-la em uma marca de imagem. Por exemplo:
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

## <a name="create-custom-reusable-templates"></a>Criar modelos reutilizáveis personalizados

Se seu aplicativo usar o mesmo ícone com ícones diferentes ou se você estiver criando um módulo que adiciona modelos de imagem adicionais, você poderá adicionar e recuperar facilmente esses ícones do SDK da Web do Azure Maps. Use as seguintes funções estáticas no `atlas` namespace.

| Nome | Tipo de retorno | Descrição | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Adiciona um modelo de imagem SVG personalizado ao namespace do Atlas. |
|  `getImageTemplate(templateName: string, scale?: number)`| string | Recupera um modelo SVG por nome. |
| `getAllImageTemplateNames()` | string[] |  Recupera um modelo SVG por nome. |

Os modelos de imagem SVG dão suporte aos seguintes valores de espaço reservado:

| Espaço reservado | Descrição |
|-|-|
| `{color}` | A cor primária. | 
| `{secondaryColor}` | A cor secundária. | 
| `{scale}` | A imagem SVG é convertida em uma imagem png quando adicionada à imagem de mapa Sprite. Esse espaço reservado pode ser usado para dimensionar um modelo antes que ele seja convertido para garantir que ele seja processado claramente. | 
| `{text}` | O local para renderizar o texto quando usado com um marcador HTML. |

O exemplo a seguir mostra como pegar um modelo SVG e adicioná-lo ao SDK da Web do Azure Maps como um modelo de ícone reutilizável. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicionar modelo de ícone personalizado ao namespace do Atlas" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Adicionar modelo de ícone personalizado ao namespace do Atlas</a> pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Lista de modelos de imagem

Esta tabela lista todos os modelos de imagem disponíveis atualmente no SDK da Web do Azure Maps. O nome do modelo está acima de cada imagem. Por padrão, a cor primária é azul e a cor secundária é branca. Para facilitar a visualização da cor secundária em um plano de fundo branco, as imagens a seguir têm a cor secundária definida como preto.

**Modelos de ícone de símbolo**

:::row:::
   :::column span="":::
      marcador
   :::column-end:::
   :::column span="":::
      marcador-espesso
   :::column-end:::
   :::column span="":::
      marcador-círculo
   :::column-end:::
   :::column span="":::
      marcador-simples
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone de marcador](./media/image-templates/marker.png)
   :::column-end:::
   :::column span="":::
      ![marcador-espesso, ícone](./media/image-templates/marker-thick.png)
   :::column-end:::
   :::column span="":::
      ![marcador-ícone de círculo](./media/image-templates/marker-circle.png)
   :::column-end:::
   :::column span="":::
      ![marcador-ícone simples](./media/image-templates/marker-flat.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      marcador-quadrado
   :::column-end:::
   :::column span="":::
      marcador-quadrado-cluster
   :::column-end:::
   :::column span="":::
      marcador-seta
   :::column-end:::
   :::column span="":::
      marcador-bola-PIN
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![marcador-ícone quadrado](./media/image-templates/marker-square.png)
   :::column-end:::
   :::column span="":::
      ![marcador-ícone de cluster quadrado](./media/image-templates/marker-square-cluster.png)
   :::column-end:::
   :::column span="":::
      ![marcador-ícone de seta](./media/image-templates/marker-arrow.png)
   :::column-end:::
   :::column span="":::
      ![marcador-ícone de pino de bola](./media/image-templates/marker-ball-pin.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      marcador-quadrado arredondado
   :::column-end:::
   :::column span="":::
      marcador-quadrado-arredondado-cluster
   :::column-end:::
   :::column span="":::
      flag
   :::column-end:::
   :::column span="":::
      Flag-triângulo
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![marcador-ícone arredondado quadrado](./media/image-templates/marker-square-rounded.png)
   :::column-end:::
   :::column span="":::
      ![marcador-quadrado-arredondado-ícone de cluster](./media/image-templates/marker-square-rounded-cluster.png)
   :::column-end:::
   :::column span="":::
      ![ícone de sinalizador](./media/image-templates/flag.png)
   :::column-end:::
   :::column span="":::
      ![ícone sinalizador-triângulo](./media/image-templates/flag-triangle.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      triangle
   :::column-end:::
   :::column span="":::
      triângulo espesso
   :::column-end:::
   :::column span="":::
      triângulo – seta para cima
   :::column-end:::
   :::column span="":::
      triângulo – seta para a esquerda
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone de triângulo](./media/image-templates/triangle.png)
   :::column-end:::
   :::column span="":::
      ![ícone de triângulo espesso](./media/image-templates/triangle-thick.png)
   :::column-end:::
   :::column span="":::
      ![triângulo – ícone de seta para cima](./media/image-templates/triangle-arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![triângulo – ícone de seta para a esquerda](./media/image-templates/triangle-arrow-left.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      hexágono
   :::column-end:::
   :::column span="":::
      hexágono-espesso
   :::column-end:::
   :::column span="":::
      hexágono-arredondado
   :::column-end:::
   :::column span="":::
      hexágono-arredondado-espesso
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone de hexágono](./media/image-templates/hexagon.png)
   :::column-end:::
   :::column span="":::
      ![ícone de hexágono-espesso](./media/image-templates/hexagon-thick.png)
   :::column-end:::
   :::column span="":::
      ![hexágono – ícone arredondado](./media/image-templates/hexagon-rounded.png)
   :::column-end:::
   :::column span="":::
      ![hexágono-ícone arredondado espesso](./media/image-templates/hexagon-rounded-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      pin
   :::column-end:::
   :::column span="":::
      pino arredondado
   :::column-end:::
   :::column span="":::
      quadrado arredondado
   :::column-end:::
   :::column span="":::
      arredondado-quadrado espesso
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone fixar](./media/image-templates/pin.png)
   :::column-end:::
   :::column span="":::
      ![ícone de arredondamento de PIN](./media/image-templates/pin-round.png)
   :::column-end:::
   :::column span="":::
      ![ícone de quadrado arredondado](./media/image-templates/rounded-square.png)
   :::column-end:::
   :::column span="":::
      ![ícone arredondado-quadrado espesso](./media/image-templates/rounded-square-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      seta para cima
   :::column-end:::
   :::column span="":::
      seta para cima – fina
   :::column-end:::
   :::column span="":::
      car
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone de seta para cima](./media/image-templates/arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![seta para cima – ícone fino](./media/image-templates/arrow-up-thin.png)
   :::column-end:::
   :::column span="":::
      ![ícone de carro](./media/image-templates/car.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::


**Modelos de padrão de preenchimento de polígono**

:::row:::
   :::column span="":::
      verificador
   :::column-end:::
   :::column span="":::
      verificador-girado
   :::column-end:::
   :::column span="":::
      círculos
   :::column-end:::
   :::column span="":::
      círculos-espaçados
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone do verificador](./media/image-templates/checker.png)
   :::column-end:::
   :::column span="":::
      ![verificador-ícone girado](./media/image-templates/checker-rotated.png)
   :::column-end:::
   :::column span="":::
      ![ícone de círculos](./media/image-templates/circles.png)
   :::column-end:::
   :::column span="":::
      ![círculos – ícone com espaçamento](./media/image-templates/circles-spaced.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      diagonal-linhas para cima
   :::column-end:::
   :::column span="":::
      diagonal-linhas para baixo
   :::column-end:::
   :::column span="":::
      diagonal-listras-verticalmente
   :::column-end:::
   :::column span="":::
      diagonal-listras-verticalmente
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![diagonal – ícone de linhas para cima](./media/image-templates/diagonal-lines-up.png)
   :::column-end:::
   :::column span="":::
      ![diagonal – ícone de linhas para baixo](./media/image-templates/diagonal-lines-down.png)
   :::column-end:::
   :::column span="":::
      ![diagonal – ícone de listras](./media/image-templates/diagonal-stripes-up.png)
   :::column-end:::
   :::column span="":::
      ![diagonal-listras-ícone para baixo](./media/image-templates/diagonal-stripes-down.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      linhas de grade
   :::column-end:::
   :::column span="":::
      girado – linhas de grade
   :::column-end:::
   :::column span="":::
      girado-grade-listras
   :::column-end:::
   :::column span="":::
      x-preenchimento
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone de linhas de grade](./media/image-templates/grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![girado – ícone de linhas de grade](./media/image-templates/rotated-grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![girar-ícone de listras de grade](./media/image-templates/rotated-grid-stripes.png)
   :::column-end:::
   :::column span="":::
      ![ícone de preenchimento x](./media/image-templates/x-fill.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      ziguezague-ziguezague
   :::column-end:::
   :::column span="":::
      ziguezague-ziguezague-vertical
   :::column-end:::
   :::column span="":::
      pontilha
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone de ziguezague-ziguezague](./media/image-templates/zig-zag.png)
   :::column-end:::
   :::column span="":::
      ![ziguezague-ziguezague-ícone vertical](./media/image-templates/zig-zag-vertical.png)
   :::column-end:::
   :::column span="":::
      ![ícone de pontos](./media/image-templates/dots.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
<br>

**Ícones de imagem pré-carregados**

O mapa sobrecarrega um conjunto de ícones na imagem de mapas Sprite usando os `marker` `pin` modelos, e `pin-round` . Esses nomes de ícone e seus valores de cor são listados na tabela a seguir.

| nome do ícone | cor | secondaryColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>Ferramenta experimentar agora

Com a ferramenta a seguir, você pode renderizar os diferentes modelos de imagem internos de várias maneiras e personalizar as cores primárias e secundárias e a escala.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opções de modelo de ícone" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>Opções de modelo de ícone</a> de caneta por mapas do Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [ImageSpriteManager](/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [namespace do Atlas](/javascript/api/azure-maps-control/atlas#functions
)

Consulte os artigos a seguir para obter mais exemplos de código em que modelos de imagem podem ser usados:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar marcadores HTML](map-add-bubble-layer.md)