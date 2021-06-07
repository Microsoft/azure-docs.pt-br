---
title: Usar o módulo mapas em modo interno do Azure Maps com os serviços Microsoft Creator (versão prévia)
description: Saiba como usar o módulo Mapas do Microsoft Azure Mapas Interno para renderizar mapas inserindo as bibliotecas JavaScript do módulo.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: e527cf5fa6a7caaeaf56ea19d684dd0830d5ca8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708672"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>Usar o módulo Mapas do Azure Mapas Interno

> [!IMPORTANT]
> Os serviços do Criador do Azure Mapas estão em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O SDK da Web do Azure Mapas inclui o módulo *Mapas do Interior do Azure Mapas*. O módulo do  *Azure Maps interno* permite que você processe mapas de interno criados nos serviços do Azure Maps Creator (versão prévia) 

## <a name="prerequisites"></a>Pré-requisitos

1. [Fazer uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Criar um recurso de Criador (versão prévia)](how-to-manage-creator.md)
3. [Obter uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura.
4. Obtenha um `tilesetId` e um `statesetId` concluindo o [tutorial para a criação de mapas do interior](tutorial-creator-indoor-maps.md).
 Você precisará usar esses identificadores para renderizar mapas do interior com o módulo Mapas do Azure Mapas Interno.

## <a name="embed-the-indoor-maps-module"></a>Inserir o módulo de Mapas do Interior

Você pode instalar e inserir o módulo *Mapas do Interior do Azure Mapas* de duas maneiras.

Para usar a versão de Rede de Distribuição de Conteúdo do Azure hospedada globalmente do módulo *Mapas do Azure Mapas Interno*, referencie as seguintes referências de JavaScript e da folha de estilo no elemento `<head>` do arquivo HTML:

```html
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
<script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
```

 Ou então, você pode baixar o módulo de *Mapas do Interior do Azure Mapas*. O módulo de *Mapas do Interior do Azure Mapas* contém uma biblioteca de clientes para acessar os serviços do Azure Mapas. Siga as etapas abaixo para instalar e carregar o módulo de *Mapas do Interior* em seu aplicativo Web.  
  
  1. Instale o [pacote Azure-Maps-interno](https://www.npmjs.com/package/azure-maps-indoor).
  
      ```powershell
      >npm install azure-maps-indoor
      ```

  2. Faça referência ao módulo de *Mapas do Interior do Azure Mapas* de JavaScript e da folha de estilo do elemento `<head>` do arquivo HTML:

      ```html
      <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.css" type="text/css" />
      <script src="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.js"></script>
      ```

## <a name="instantiate-the-map-object"></a>Instanciar o objeto Mapa

Primeiro, crie um *objeto Mapa*. O *objeto Mapa* será usado na próxima etapa para instanciar o objeto *Gerenciador de Mapas do Interior*.  O código a seguir mostra como instanciar o objeto *Mapa*:

```javascript
const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";

const map = new atlas.Map("map-id", {
  //use your facility's location
  center: [-122.13203, 47.63645],
  //or, you can use bounds: [# west, # south, # east, # north] and replace # with your map's bounds
  style: "blank",
  view: 'Auto',
  authOptions: {
      authType: 'subscriptionKey',
      subscriptionKey: subscriptionKey
  },
  zoom: 19,
});
```

## <a name="instantiate-the-indoor-manager"></a>Instanciar o Gerenciador de Mapas do Interior

Para carregar os conjuntos de peças dos mapas do interior e o estilo do mapa das peças, você deve criar uma instância do *Gerenciador de Mapas do Interior*. Crie uma instância do *Gerenciador de Mapas do Interior* fornecendo o *objeto Mapa* e o `tilesetId`correspondente. Se desejar dar suporte a [estilos de mapa dinâmicos](indoor-map-dynamic-styling.md), você deverá passar o `statesetId`. O nome da variável `statesetId` diferencia maiúsculas de minúsculas. Seu código deve ser semelhante ao JavaScript abaixo.

```javascript
const tilesetId = "<tilesetId>";
const statesetId = "<statesetId>";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: tilesetId,
    statesetId: statesetId // Optional
});
```

Para habilitar a sondagem de dados de estado fornecidos, você deve fornecer o `statesetId` e chamar `indoorManager.setDynamicStyling(true)`. A sondagem de dados de estado permite atualizar dinamicamente o estado das propriedades dinâmicas ou *estados*. Por exemplo, um recurso como sala pode ter uma propriedade dinâmica (*estado*) chamada `occupancy`. Seu aplicativo pode desejar sondar quaisquer alterações de *estado* para refletir a alteração dentro do mapa Visual. O código abaixo mostra como habilitar a sondagem de estado:

```javascript
const tilesetId = "<tilesetId>";
const statesetId = "<statesetId>";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: tilesetId,
    statesetId: statesetId // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}
```

## <a name="indoor-level-picker-control"></a>Controle de Seletor de piso de Mapas do Interior

 O controle *Seletor de piso de Mapas do Interior* permite alterar o piso do mapa renderizado. Opcionalmente, você pode inicializar o *Seletor de piso de Mapas do Interior* através do *Gerenciador de Mapas do Interior*. Aqui está o código para inicializar o seletor de controle de piso:

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>Eventos de Mapas do Interior

 O módulo *Mapas do Interior do Azure Mapas* em uso oferece suporte a eventos do *objeto Mapa*. Os ouvintes do *objeto Mapa* são invocados quando um piso ou uma instalação são alterados. Se você quiser executar o código quando um piso ou uma instalação tiverem sido alterados, coloque o seu código dentro do ouvinte de eventos. O código a seguir mostra como os ouvintes de evento podem ser adicionados ao *objeto Mapa*.

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

  //code that you want to run after a level has been changed
  console.log("The level has changed: ", eventData);
});

map.events.add("facilitychanged", indoorManager, (eventData) => {

  //code that you want to run after a facility has been changed
  console.log("The facility has changed: ", eventData);
});
```

A variável `eventData` contém informações sobre o piso ou instalação que invocou o evento `levelchanged` ou `facilitychanged`, respectivamente. Quando um piso é alterado, o objeto `eventData` conterá o `facilityId`, o novo `levelNumber` e outros metadados. Quando uma instalação é alterada, o objeto `eventData` conterá o novo `facilityId`, o novo `levelNumber` e outros metadados.

## <a name="example-use-the-indoor-maps-module"></a>Exemplo: Usar o módulo do Azure Mapas Interno

Este exemplo mostra como usar o módulo *Mapas do Azure Mapas Interno* em seu aplicativo Web. Embora o exemplo seja limitado em escopo, ele aborda as noções básicas do que você precisa para começar a usar o módulo *Mapas do Interior do Azure Mapas*. O código HTML completo está abaixo dessas etapas.

1. Use a [opção](#embed-the-indoor-maps-module) de Rede de Distribuição de Conteúdo do Azure para instalar o módulo *Mapas do Azure Mapas Interno*.

2. Criar um novo arquivo HTML

3. No cabeçalho do HTML, faça referência aos estilos de JavaScript e folha de estilo do módulo *Mapas do Interior do Azure Mapas*.

4. Inicialize um *objeto Mapa*. O *objeto Mapa* oferece suporte para as seguintes opções:
    - `Subscription key` é a sua chave de assinatura primária do Azure Mapas.
    - `center` define uma latitude e longitude para o centro do mapa do interior. Forneça um valor para `center` se você não quiser fornecer um valor para `bounds`. O formato deve ser exibido como `center`: [-122.13315, 47.63637].
    - `bounds` é a menor forma retangular que inclui os dados de mapa do conjunto de peças. Defina um valor para `bounds` se você não quiser definir um valor para `center`. Você pode encontrar os limites do mapa chamando a [API de lista de conjunto de peças](/rest/api/maps/tileset/listpreview). A API de lista de conjunto de peças retorna o `bbox`, que você pode analisar e atribuir a `bounds`. O formato deve aparecer como `bounds` : [# West, # Sul, # East, # Norte].
    - `style` permite definir a cor da tela de fundo. Para exibir uma tela de fundo branca, defina `style` como “em branco”.
    - `zoom` permite especificar os níveis de zoom mínimo e máximo para o mapa.

5. Em seguida, crie o módulo *Gerenciador de Mapas do Interior*. Atribua o `tilesetId` do módulo *Mapas do Interior do Azure Mapas* e, opcionalmente, adicione o `statesetId`.

6. Crie uma instância para o controle *Seletor de piso do Mapa do Interior*.

7. Adicione ouvintes do evento do *objeto Mapa*.  

Seu arquivo agora deve ter aparência semelhante à do HTML abaixo.

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
      
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>

      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [# west, # south, # east, # north] and replace # with your Map bounds
          style: "blank",
          view: 'Auto',
          authOptions: { 
              authType: 'subscriptionKey',
              subscriptionKey: subscriptionKey
          },
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl: levelControl, //level picker
          tilesetId: tilesetId,
          statesetId: statesetId // Optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

Para ver seu mapa do interior, carregue-o em um navegador da Web. Ele deve aparecer como a imagem abaixo. Se você clicar no recurso escada, o *seletor de piso* será exibido no canto superior direito.

  ![imagem de mapa do interior](media/how-to-use-indoor-module/indoor-map-graphic.png)

[Consulte a demonstração ao vivo](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>Próximas etapas

Leia sobre as APIs relacionadas ao módulo de *Mapas do Interior do Azure Mapas*:

> [!div class="nextstepaction"]
> [Requisitos do pacote de desenho](drawing-requirements.md)

>[!div class="nextstepaction"]
> [Criador (visualização) para mapas de interno](creator-indoor-maps.md)

Saiba mais sobre como adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Estilos dinâmicos para mapas internos](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Exemplos de código](/samples/browse/?products=azure-maps)