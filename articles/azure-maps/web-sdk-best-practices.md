---
title: Práticas recomendadas do SDK da Web do Azure Maps | Mapas do Microsoft Azure
description: Aprenda dicas & truques para otimizar o uso do SDK para Web do Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 3/22/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: ba351053c876c31d945ec7e4127a5caebd6a71ce
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877733"
---
# <a name="azure-maps-web-sdk-best-practices"></a>Práticas recomendadas do SDK para Web do Azure Maps

Este documento se concentra nas práticas recomendadas para o SDK da Web do Azure Maps, no entanto, muitas das práticas recomendadas e otimizações descritas podem ser aplicadas a todos os outros SDKs do Azure Maps.

O SDK da Web do Azure Maps fornece uma tela avançada para processar grandes conjuntos de dados espaciais de várias maneiras diferentes. Em alguns casos, há várias maneiras de renderizar dados da mesma maneira, mas dependendo do tamanho do conjunto de dados e da funcionalidade desejada, um método pode ser melhor executado do que outros. Este artigo destaca as práticas recomendadas e dicas e truques para maximizar o desempenho e criar uma experiência de usuário tranqüila.

Em geral, ao tentar melhorar o desempenho do mapa, procure maneiras de reduzir o número de camadas e fontes e a complexidade dos conjuntos de dados e dos estilos de renderização usados.

## <a name="security-basics"></a>Noções básicas de segurança

A parte mais importante do seu aplicativo é sua segurança. Se o seu aplicativo não for seguro, um hacker poderá arruinar qualquer aplicativo, independentemente da qualidade do usuário. Veja a seguir algumas dicas para manter o aplicativo do Azure Maps seguro. Ao usar o Azure, familiarize-se com as ferramentas de segurança disponíveis para você. Consulte este documento para obter uma [introdução à segurança do Azure](https://docs.microsoft.com/azure/security/fundamentals/overview).

> [!IMPORTANT]
> O mapas do Azure fornece dois métodos de autenticação.
> * Autenticação baseada em chave de assinatura
> * Azure Active Directory uso de autenticação Azure Active Directory em todos os aplicativos de produção.
> A autenticação baseada em chave de assinatura é simples e o que a maioria das plataformas de mapeamento usa como um método leve para medir seu uso da plataforma para fins de cobrança. No entanto, essa não é uma forma segura de autenticação e só deve ser usada localmente durante o desenvolvimento de aplicativos. Algumas plataformas fornecem a capacidade de restringir quais endereços IP e/ou referenciador HTTP está em solicitações, no entanto, essas informações podem ser facilmente falsificadas. Se você usar chaves de assinatura, não deixe de [girá-las regularmente](how-to-manage-authentication.md#manage-and-rotate-shared-keys).
> O Azure Active Directory é um serviço de identidade empresarial que tem uma grande seleção de recursos de segurança e configurações para todos os tipos de cenários de aplicativos. A Microsoft recomenda que todos os aplicativos de produção usando mapas do Azure usem Azure Active Directory para autenticação.
> Saiba mais sobre como [gerenciar a autenticação no Azure Maps](how-to-manage-authentication.md) neste documento.

### <a name="secure-your-private-data"></a>Proteja seus dados privados

Quando os dados são adicionados aos SDKs do mapa interativo do Azure Maps, eles são renderizados localmente no dispositivo do usuário final e nunca são enviados de volta à Internet por qualquer motivo.

Se seu aplicativo estiver carregando dados que não devem ser acessíveis publicamente, verifique se os dados estão armazenados em um local seguro, se são acessados de maneira segura e se o próprio aplicativo está bloqueado e disponível somente para os usuários desejados. Se qualquer uma dessas etapas for ignorada, uma pessoa não autorizada terá o potencial de acessar esses dados. Azure Active Directory pode ajudá-lo a bloquear isso.

Consulte este tutorial sobre como [Adicionar autenticação ao seu aplicativo Web em execução no serviço Azure app](https://docs.microsoft.com/azure/app-service/scenario-secure-app-authentication-app-service)

### <a name="use-the-latest-versions-of-azure-maps"></a>Usar as versões mais recentes do Azure Maps

Os SDKs do mapas do Azure passam por testes de segurança regulares, juntamente com as bibliotecas de dependências externas que podem ser usadas pelos SDKs. Qualquer problema de segurança conhecido é corrigido em tempo hábil e liberado para produção. Se o seu aplicativo apontar para a versão principal mais recente da versão hospedada do SDK da Web do Azure Maps, ele receberá automaticamente todas as atualizações de versão secundária que incluirão correções relacionadas à segurança.

Se hospedar internamente o SDK da Web do Azure Maps por meio do módulo NPM, certifique-se de usar o símbolo de cursor (^) em combinação com o número de versão do pacote NPM do Azure Maps em seu `package.json` arquivo para que ele sempre aponte para a versão secundária mais recente.

```json
"dependencies": {
  "azure-maps-control": "^2.0.30"
}
```

## <a name="optimize-initial-map-load"></a>Otimizar a carga inicial do mapa

Quando uma página da Web está carregando, uma das primeiras coisas que você deseja fazer é começar a renderizar algo assim que possível para que o usuário não fique em uma tela em branco.

### <a name="watch-the-maps-ready-event"></a>Assista ao evento de mapas prontos

Da mesma forma, quando o mapa inicialmente é carregado com frequência, é desejado carregar dados nele o mais rápido possível, portanto, o usuário não está olhando para um mapa vazio. Como o mapa carrega recursos de forma assíncrona, você precisa esperar até que o mapa esteja pronto para ser interagindo antes de tentar renderizar seus próprios dados nele. Há dois eventos que você pode esperar, um `load` evento e um `ready` evento. O evento de carga será acionado depois que o mapa terminar de carregar completamente a exibição do mapa inicial e todos os blocos do mapa tiverem sido carregados. O evento Ready será acionado quando os recursos de mapa mínimos necessários para começar a interagir com o mapa. O evento Ready geralmente pode ser acionado na metade do tempo do evento Load e, portanto, permite que você comece a carregar seus dados no mapa mais cedo.

### <a name="lazy-load-the-azure-maps-web-sdk"></a>Carga lenta do SDK da Web do Azure Maps

Se o mapa não for necessário imediatamente, carregue a carga lenta do SDK da Web do Azure Maps até que seja necessário. Isso atrasará o carregamento dos arquivos JavaScript e CSS usados pelo SDK da Web do Azure Maps até que seja necessário. Um cenário comum em que isso ocorre é quando o mapa é carregado em uma guia ou painel flutuante que não é exibido no carregamento da página.
O exemplo de código a seguir mostra como atrasar o carregamento do SDK da Web do Azure Maps até que um botão seja pressionado.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Carregamento lento do mapa" src="https://codepen.io/azuremaps/embed/vYEeyOv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a caneta <a href='https://codepen.io/azuremaps/pen/vYEeyOv'>lenta carregar o mapa</a> pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-a-placeholder-for-the-map"></a>Adicionar um espaço reservado para o mapa

Se o mapa demorar um pouco para ser carregado devido a limitações de rede ou outras prioridades em seu aplicativo, considere adicionar uma pequena imagem de fundo ao mapa `div` como um espaço reservado para o mapa. Isso preencherá a void do mapa `div` durante o carregamento.

### <a name="set-initial-map-style-and-camera-options-on-initialization"></a>Definir as opções de estilo de mapa inicial e câmera na inicialização

Geralmente, os aplicativos desejam carregar o mapa para um local ou estilo específico. Às vezes, os desenvolvedores aguardarão até que o mapa tenha sido carregado (ou aguarde o `ready` evento) e, em seguida, use as `setCemer` `setStyle` funções ou do mapa. Isso geralmente levará mais tempo para chegar ao modo de exibição de mapa inicial desejado, pois muitos recursos terminam sendo carregados por padrão antes que os recursos necessários para o modo de exibição de mapa desejado sejam carregados. Uma abordagem melhor é passar a câmera de mapa desejada e as opções de estilo para o mapa ao inicializá-lo.

## <a name="optimize-data-sources"></a>Otimizar fontes de dados

O SDK da Web tem duas fontes de dados,

* **Origem geojson**: conhecida como `DataSource` classe, gerencia dados de local brutos no formato geojson localmente. Bom para conjuntos de dados pequenos a médios (com versões de centenas de milhares de recursos).
* **Origem do bloco de vetor**: conhecido na `VectorTileSource` classe, carrega dados formatados como blocos de vetor para a exibição de mapa atual, com base no sistema de blocos de divisão de mapas. Ideal para conjuntos de dados grandes a grandes (milhões ou bilhões de recursos).

### <a name="use-tile-based-solutions-for-large-datasets"></a>Usar soluções baseadas em blocos para grandes conjuntos de altos

Se estiver trabalhando com conjuntos de dados maiores contendo milhões de recursos, a maneira recomendada para alcançar o desempenho ideal é expor os dados usando uma solução do lado do servidor, como o serviço de bloco de imagem vetorial ou rasterizada.  
Blocos de vetor são otimizados para carregar somente os dados que estão no modo de exibição com as geometrias recortadas na área de foco do bloco e são generalizados para corresponder à resolução do mapa para o nível de zoom do bloco.

A [plataforma Azure Maps Creator](creator-indoor-maps.md) fornece a capacidade de recuperar dados no formato de bloco de vetor. Outros formatos de dados podem usar ferramentas como [Tippecanoe](https://github.com/mapbox/tippecanoe) ou uma das várias listas de [recursos nesta página](https://github.com/mapbox/awesome-vector-tiles).

Também é possível criar um serviço personalizado que processe os conjuntos de dados como blocos de imagem rasterizada no lado do servidor e carregue-os usando a classe TileLayer no SDK do mapa. Isso fornece desempenho excepcional, pois o mapa só precisa carregar e gerenciar algumas dúzias de imagens no máximo. No entanto, há algumas limitações no uso de blocos rasterizados, pois os dados brutos não estão disponíveis localmente. Um serviço secundário geralmente é necessário para alimentar qualquer tipo de experiência de interação, por exemplo, descobrir em que forma um usuário clicou. Além disso, o tamanho do arquivo de um bloco de varredura é geralmente maior que um bloco de vetor compactado que contém geometrias generalizadas e otimizadas no nível de zoom.

Saiba mais sobre fontes de dados no documento [criar uma fonte de dados](create-data-source-web-sdk.md) .

### <a name="combine-multiple-datasets-into-a-single-vector-tile-source"></a>Combinar vários conjuntos de gráficos em uma única fonte de bloco de vetor

Quanto menos fontes de dados o mapa precisa gerenciar, mais rápido ele pode processar todos os recursos a serem exibidos. Em particular, quando se trata de fontes de bloco, a combinação de duas fontes de bloco de vetor corta o número de solicitações HTTP para recuperar os blocos pela metade e a quantidade total de dados seria ligeiramente menor, já que há apenas um cabeçalho de arquivo.

A combinação de vários conjuntos de dados em uma única fonte de bloco de vetor pode ser obtida usando uma ferramenta como [Tippecanoe](https://github.com/mapbox/tippecanoe). Os conjuntos de dados podem ser combinados em uma única coleção de recursos ou separados em camadas separadas dentro do bloco de vetor conhecido como camadas de origem. Ao conectar uma origem de bloco de vetor a uma camada de renderização, você especificaria a camada de origem que contém os dados que deseja renderizar com a camada.

### <a name="reduce-the-number-of-canvas-refreshes-due-to-data-updates"></a>Reduzir o número de atualizações de tela devido a atualização de dados

Há várias maneiras pelas quais os dados em uma `DataSource` classe podem ser adicionados ou atualizados. Abaixo estão listados os diferentes métodos e algumas considerações para garantir um bom desempenho.

* A função de fontes de dados `add` pode ser usada para adicionar um ou mais recursos a uma fonte de dados. Cada vez que essa função for chamada, ele disparará uma atualização da tela do mapa. Se adicionar muitos recursos, combine-os em uma coleção de matrizes ou recursos e passá-los para essa função uma vez, em vez de fazer um loop em um conjunto de dados e chamar essa função para cada recurso.
* A função de fontes de dados `setShapes` pode ser usada para substituir todas as formas em uma fonte de dados. Nos bastidores, ele combina as fontes de dados `clear` e as `add` funções juntas e faz uma única atualização de tela de mapa em vez de duas, o que é muito mais rápido. Certifique-se de usar isso quando desejar atualizar todos os dados em uma fonte de dados.
* A função de fontes de dados `importDataFromUrl` pode ser usada para carregar um arquivo geojson por meio de uma URL em uma fonte de dados. Depois que os dados são baixados, eles são passados para a função de fontes de dados `add` . Se o arquivo geojson estiver hospedado em um domínio diferente, certifique-se de que o outro domínio dê suporte a solicitações entre domínios (CORs). Se não considerar copiar os dados para um arquivo local em seu domínio ou criar um serviço de proxy que tenha o CORs habilitado. Se o arquivo for grande, considere a possibilidade de convertê-lo em uma fonte de bloco vetorial.
* Se os recursos forem encapsulados com a `Shape` classe, as `addProperty` `setCoordinates` funções, e `setProperties` da forma irão disparar uma atualização na fonte de dados e uma atualização da tela do mapa. Todos os recursos retornados pelas fontes de dados `getShapes` e `getShapeById` funções são automaticamente encapsulados com a `Shape` classe. Se você quiser atualizar várias formas, será mais rápido convertê-las em JSON usando a função fontes de dados `toJson` , editando o geojson e, em seguida, passando esses dados para a função fontes de dados `setShapes` .

### <a name="avoid-calling-the-data-sources-clear-function-unnecessarily"></a>Evite chamar a função limpar fontes de dados desnecessariamente

Chamar a função Clear da `DataSource` classe causa uma atualização da tela do mapa. Se a `clear` função for chamada várias vezes em uma linha, pode ocorrer um atraso enquanto o mapa aguarda a ocorrência de cada atualização.

Um cenário comum em que isso geralmente aparece em aplicativos é quando um aplicativo limpa a fonte de dados, baixa novos dados, limpa a fonte de dados novamente e adiciona os novos dados à fonte de dados. Dependendo da experiência do usuário desejada, as alternativas a seguir seriam melhores.

* Limpe os dados antes de baixar os novos dados e, em seguida, passe os novos dados para as fontes de dados `add` ou a `setShapes` função. Se esse for o único conjunto de dados no mapa, o mapa estará vazio enquanto os novos dados estiverem sendo baixados.
* Baixe os novos dados e, em seguida, passe-os para a função fontes de dados `setShapes` . Isso substituirá todos os dados no mapa.

### <a name="remove-unused-features-and-properties"></a>Remover recursos e propriedades não utilizados

Se seu conjunto de aplicativos contiver recursos que não serão usados em seu aplicativo, remova-os. Da mesma forma, remova todas as propriedades dos recursos que não são necessários. Isso tem várias vantagens:

* Reduz a quantidade de dados a serem baixados.
* Reduz o número de recursos que precisam ser efetuados em loop ao renderizar os dados.
* Às vezes, pode ajudar a simplificar ou remover filtros e expressões controladas por dados, o que significa menos processamento necessário no momento da renderização.

Quando os recursos têm muitas propriedades ou conteúdo, é muito mais eficaz limitar o que é adicionado à fonte de dados apenas àqueles necessários para renderização e ter um método ou serviço separado para recuperar a propriedade ou o conteúdo adicional quando necessário. Por exemplo, se você tiver um mapa simples exibindo locais em um mapa quando clicado em uma série de conteúdo detalhado, será exibido. Se você quiser usar o estilo controlado por dados para personalizar como os locais são renderizados no mapa, carregue apenas as propriedades necessárias para a fonte de dados. Quando desejar exibir o conteúdo detalhado, use a ID do recurso para recuperar o conteúdo adicional separadamente. Se o conteúdo estiver armazenado no lado do servidor, um serviço poderá ser usado para recuperá-lo de forma assíncrona, o que reduziria drasticamente a quantidade de dados que precisam ser baixados quando o mapa for inicialmente carregado.

Além disso, a redução do número de dígitos significativos nas coordenadas dos recursos também pode reduzir significativamente o tamanho dos dados. Não é incomum que as coordenadas contenham 12 ou mais casas decimais; no entanto, seis casas decimais têm uma precisão de cerca de 0,1 medidor, que geralmente é mais preciso do que a localização representada pela coordenada (seis casas decimais são recomendadas ao trabalhar com dados de local pequenos, como layouts de construção em interno). Ter mais de seis casas decimais provavelmente não fará nenhuma diferença na forma como os dados são renderizados e exigirá que o usuário Baixe mais dados sem nenhum benefício adicional.

Aqui está uma lista de [ferramentas úteis para trabalhar com dados geojson](https://github.com/tmcw/awesome-geojson).

### <a name="use-a-separate-data-source-for-rapidly-changing-data"></a>Use uma fonte de dados separada para alterar rapidamente os dados

Às vezes, há a necessidade de atualizar rapidamente os dados no mapa para coisas como mostrar atualizações dinâmicas de dados de streaming ou recursos de animação. Quando uma fonte de dados for atualizada, o mecanismo de renderização executará um loop e renderizará todos os recursos na fonte de dados. Separar dados estáticos de dados de alteração rápida em diferentes fontes de dados pode reduzir significativamente o número de recursos que são renderizados novamente em cada atualização para a fonte de dados e melhorar o desempenho geral.

Se você estiver usando blocos de vetor com dados dinâmicos, uma maneira fácil de dar suporte a atualizações é usar o `expires` cabeçalho de resposta. Por padrão, qualquer camada de bloco de vetor ou de bloco de rasterização recarregará os blocos automaticamente quando a `expires` Data. Os blocos de fluxo de tráfego e de incidentes no mapa usam esse recurso para garantir que os dados de tráfego em tempo real sejam exibidos no mapa. Esse recurso pode ser desabilitado definindo a `refreshExpiredTiles` opção de serviço Maps como `false` .

### <a name="adjust-the-buffer-and-tolerance-options-in-geojson-data-sources"></a>Ajustar as opções de buffer e tolerância em fontes de dados geojson

A `DataSource` classe converte dados brutos de local em blocos de vetor locais para renderização imediata. Esses blocos de vetores locais cortam os dados brutos para os limites da área de bloco com um pouco de buffer para garantir a renderização suave entre blocos. Quanto menor a `buffer` opção, menos dados sobrepostos são armazenados nos blocos de vetor locais e o melhor desempenho, no entanto, quanto maior a alteração dos artefatos de renderização ocorrendo. Tente ajustar essa opção para obter a combinação certa de desempenho com artefatos de renderização mínima.

A `DataSource` classe também tem uma `tolerance` opção que é usada com o algoritmo de simplificação de Douglas-Peucker ao reduzir a resolução de geometrias para fins de renderização. Aumentar esse valor de tolerância reduzirá a resolução de geometrias e, por sua vez, melhorará o desempenho. Ajuste essa opção para obter a combinação certa de resolução e desempenho de geometria para seu conjunto de dados.

### <a name="set-the-max-zoom-option-of-geojson-data-sources"></a>Definir a opção de zoom máximo de fontes de dados geojson

A `DataSource` classe converte dados brutos de local em blocos de vetor locais para renderização imediata. Por padrão, ele fará isso até o nível de zoom 18, ponto em que, quando for aplicado um zoom mais próximo, ele obterá dados de exemplo dos blocos gerados para o nível de zoom 18. Isso funciona bem para a maioria dos conjuntos de dados que precisam ter alta resolução quando são ampliados nesses níveis. No entanto, ao trabalhar com conjuntos de dados com maior probabilidade de serem exibidos quando mais zoom, como ao exibir polígonos de estado ou província, definir a `minZoom` opção da fonte de dados como um valor menor, como `12` reduzirá a quantidade de computação, a geração de blocos locais que ocorre e a memória usada pela fonte de dados e aumentará o desempenho.

### <a name="minimize-geojson-response"></a>Minimizar resposta geojson

Ao carregar dados geojson de um servidor por meio de um serviço ou carregando um arquivo simples, certifique-se de ter os dados minimizados para remover caracteres de espaço desnecessários que tornam o tamanho do download maior do que o necessário.

### <a name="access-raw-geojson-using-a-url"></a>Acessar geojson bruto usando uma URL

É possível armazenar objetos geojson embutidos dentro do JavaScript, no entanto, isso usará muita memória, pois as cópias dele serão armazenadas na variável que você criou para esse objeto e a instância de fonte de dados, que o gerencia em um Web Worker separado. Expor o geojson ao seu aplicativo usando uma URL, e a fonte de dados carregará uma única cópia de dados diretamente no Web Worker das fontes de dados.  

## <a name="optimize-rendering-layers"></a>Otimizar camadas de renderização

O mapas do Azure fornece várias camadas diferentes para renderizar dados em um mapa. Há muitas otimizações que você pode aproveitar para adaptar essas camadas ao seu cenário, o que aumenta o desempenho e a experiência geral do usuário.

### <a name="create-layers-once-and-reuse-them"></a>Criar camadas uma vez e reutilizá-las

O SDK da Web do Azure Maps é decidido ser controlado por dados. Os dados entram em fontes de dados, que são então conectadas às camadas de renderização. Se você quiser alterar os dados no mapa, atualize os dados na fonte de dados ou altere as opções de estilo em uma camada. Isso geralmente é muito mais rápido do que remover e recriar camadas sempre que houver uma alteração.

### <a name="consider-bubble-layer-over-symbol-layer"></a>Considerar camada de bolha sobre camada de símbolo

A camada de bolha renderiza pontos como círculos no mapa e pode facilmente ter seu raio e cor com estilo usando uma expressão controlada por dados. Como o círculo é uma forma simples de WebGL desenhar, o mecanismo de renderização será capaz de renderizar isso muito mais rápido do que uma camada de símbolo, que tem que carregar e renderizar uma imagem. A diferença de desempenho dessas duas camadas de renderização é perceptível ao renderizar dezenas de milhares de pontos.

### <a name="use-html-markers-and-popups-sparingly"></a>Usar marcadores HTML e pop-up com moderação

Ao contrário da maioria das camadas no controle da Web do Azure Maps que usam WebGL para renderização, marcadores de HTML e pop-ups usam elementos DOM tradicionais para renderização. Assim, quanto mais marcadores HTML e pop-ups adicionaram uma página, mais elementos DOM existem. O desempenho pode diminuir depois de adicionar algumas centenas de marcadores HTML ou pop-ups. Para conjuntos de dados maiores, considere o clustering dos dados ou o uso de um símbolo ou camada de bolha. Para pop-ups, uma estratégia comum é criar um único pop-up e reutilizá-lo atualizando seu conteúdo e posição, conforme mostrado no exemplo abaixo:

<br/>

<iframe height='500' scrolling='no' title='Reutilizar pop-up com vários marcadores' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reutilizar pop-up com vários marcadores</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

Dito isso, se você tiver apenas alguns pontos para renderizar no mapa, a simplicidade dos marcadores de HTML poderá ser preferida. Além disso, os marcadores de HTML podem facilmente se tornar arrastáveis, se necessário.

### <a name="combine-layers"></a>Combinar camadas

O mapa é capaz de renderizar centenas de camadas. no entanto, quanto mais camadas houver, mais tempo levará para renderizar uma cena. Uma estratégia para reduzir o número de camadas é combinar camadas que têm estilos semelhantes ou que podem ser estilizadas usando [estilos controlados por dados](data-driven-style-expressions-web-sdk.md).

Por exemplo, considere um conjunto de dados em que todos os recursos têm uma `isHealthy` propriedade que pode ter um valor de `true` ou `false` . Ao criar uma camada de bolha que renderiza bolhas coloridas diferentes com base nessa propriedade, há várias maneiras de fazer isso, conforme listado abaixo, do menos eficaz para o melhor desempenho.

* Divida os dados em duas fontes de dados com base no `isHealthy` valor e anexe uma camada de bolha com uma opção de cor embutida em código para cada fonte de dados.
* Coloque todos os dados em uma única fonte de dados e crie duas camadas de bolha com uma opção de cor embutida em código e um filtro com base na `isHealthy` propriedade. 
* Coloque todos os dados em uma única fonte de dados, crie uma camada de bolha única com uma `case` expressão de estilo para a opção de cor com base na `isHealthy` propriedade. Veja um exemplo de código que demonstra isso.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Get the 'isHealthy' property from the feature.
        ['get', 'isHealthy'],

        //If true, make the color 'green'. 
        'green',

        //If false, make the color red.
        'red'
    ]
});
```

### <a name="create-smooth-symbol-layer-animations"></a>Criar animações de camada de símbolo suave

As camadas de símbolo têm a detecção de colisão habilitada por padrão. Essa detecção de colisão visa garantir que dois símbolos não se sobreponham. As opções de ícone e texto de uma camada de símbolo têm duas opções,

* `allowOverlap` -Especifica se o símbolo ficará visível se ele colidir com outros símbolos.
* `ignorePlacement` -Especifica se os outros símbolos têm permissão para colidir com o símbolo.

Essas duas opções são definidas como `false` por padrão. Ao animar um símbolo, os cálculos de detecção de colisão serão executados em cada quadro da animação, o que pode retardar a animação e fazer com que pareça menos suave. Para suavizar a animação, defina essas opções como `true` .

O exemplo de código a seguir é uma maneira simples de animar uma camada de símbolo.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Animação da camada de símbolo" src="https://codepen.io/azuremaps/embed/oNgGzRd?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a <a href='https://codepen.io/azuremaps/pen/oNgGzRd'>animação da camada de símbolo</a> de caneta pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="specify-zoom-level-range"></a>Especificar intervalo de nível de zoom

Se seus dados atenderem a um dos critérios a seguir, certifique-se de especificar o nível de zoom mínimo e máximo da camada para que o mecanismo de renderização possa ignorá-lo quando estiver fora do intervalo de nível de zoom.

* Se os dados forem provenientes de uma fonte de bloco vetorial, muitas vezes as camadas de origem para tipos de dados diferentes só estarão disponíveis por meio de um intervalo de níveis de zoom.
* Se estiver usando uma camada de bloco que não tem blocos para todos os níveis de zoom de 0 a 24, e você deseja que ele seja renderizado apenas nos níveis em que ele tem blocos e não tente preencher blocos ausentes com blocos de outros níveis de zoom.
* Se você quiser apenas renderizar uma camada em determinados níveis de zoom.
Todas as camadas têm `minZoom` uma `maxZoom` opção and, em que a camada será renderizada quando entre esses níveis de zoom com base nessa lógica ` maxZoom > zoom >= minZoom` .

**Exemplo**

```javascript
//Only render this layer between zoom levels 1 and 9. 
var layer = new atlas.layer.BubbleLayer(dataSource, null, {
    minZoom: 1,
    maxZoom: 10
});
```

### <a name="specify-tile-layer-bounds-and-source-zoom-range"></a>Especificar limites de camada de bloco e intervalo de zoom de origem

Por padrão, as camadas de bloco carregarão blocos em todo o globo. No entanto, se o serviço de bloco tiver apenas blocos para uma determinada área, o mapa tentará carregar blocos quando estiver fora desta área. Quando isso acontece, uma solicitação para cada bloco será feita e aguardará uma resposta que pode bloquear outras solicitações sendo feitas pelo mapa e, portanto, reduzir a renderização de outras camadas. A especificação dos limites de uma camada de peça resultará no mapa apenas solicitando blocos que estejam dentro dessa caixa delimitadora. Além disso, se a camada de peça estiver disponível apenas entre determinados níveis de zoom, especifique o mínimo e o máximo de zoom de origem para o mesmo motivo.

**Exemplo**

```javascript
var tileLayer = new atlas.layer.TileLayer({
    tileUrl: 'myTileServer/{z}/{y}/{x}.png',
    bounds: [-101.065, 14.01, -80.538, 35.176],
    minSourceZoom: 1,
    maxSourceZoom: 10
});
```

### <a name="use-a-blank-map-style-when-base-map-not-visible"></a>Usar um estilo de mapa em branco quando o mapa base não estiver visível

Se uma camada estiver sendo sobreposta no mapa que abrangerá completamente o mapa base, considere definir o estilo de mapa como `blank` ou `blank_accessible` para que o mapa base não seja renderizado. Um cenário comum para fazer isso é quando se está sobrepondo um bloco de globo completo em não tem opacidade ou área transparente acima do mapa base.

### <a name="smoothly-animate-image-or-tile-layers"></a>Animar de forma suave camadas de imagem ou bloco

Se você quiser animar por meio de uma série de camadas de imagem ou bloco no mapa. Geralmente, é mais rápido criar uma camada para cada imagem ou camada de bloco e alterar a opacidade do que atualizar a origem de uma única camada em cada quadro de animação. Ocultar uma camada definindo a opacidade como zero e mostrando uma nova camada definindo sua opacidade como um valor maior que zero é muito mais rápido do que atualizar a origem na camada. Como alternativa, a visibilidade das camadas pode ser alternada, mas certifique-se de definir a duração de esmaecimento da camada como zero, caso contrário, ela animará a camada ao exibi-la, o que causará um efeito de cintilação, pois a camada anterior estaria oculta antes que a nova camada seja visível.

### <a name="tweak-symbol-layer-collision-detection-logic"></a>Ajustar a lógica de detecção de colisão da camada de símbolo

A camada de símbolo tem duas opções que existem para o ícone e o texto chamados `allowOverlap` e `ignorePlacement` . Essas duas opções especificam se o ícone ou o texto de um símbolo pode se sobrepor ou ser sobreposto. Quando elas são definidas como `false` , a camada de símbolo fará cálculos ao renderizar cada ponto para ver se ele colidi com qualquer outro símbolo já processado na camada e, se tiver, não processará o símbolo de colisão. Isso é bom para reduzir a desordem no mapa e reduzir o número de objetos renderizados. Ao definir essas opções como `false` , essa lógica de detecção de colisão será ignorada e todos os símbolos serão renderizados no mapa. Ajuste essa opção para obter a melhor combinação de desempenho e experiência do usuário.

### <a name="cluster-large-point-data-sets"></a>Conjuntos de dados de ponto grande do cluster

Ao trabalhar com grandes conjuntos de pontos de dados, você pode achar que, quando renderizado em determinados níveis de zoom, muitos dos pontos se sobrepõem e só ficam visíveis parciais, se houver. O clustering é um processo de pontos de agrupamento que estão próximos juntos e representando-os como um único ponto clusterizado. À medida que o usuário ampliar o mapa no, os clusters serão separados em seus pontos individuais. Isso pode reduzir significativamente a quantidade de dados que precisam ser processados, fazer com que o mapa fique menos confuso e melhorar o desempenho. A `DataSource` classe tem opções para clusterizar dados localmente. Além disso, muitas ferramentas que geram blocos vetoriais também têm opções de clustering.

Além disso, aumente o tamanho do raio do cluster para melhorar o desempenho. Quanto maior o raio do cluster, menos pontos clusterizados existem para acompanhar e renderizar.
Saiba mais no [documento de dados do ponto de cluster](clustering-point-data-web-sdk.md)

### <a name="use-weighted-clustered-heat-maps"></a>Usar mapas de calor em cluster ponderados

A camada do mapa de calor pode renderizar dezenas de milhares de pontos de dados com facilidade. Para conjuntos de dados maiores, considere habilitar o clustering na fonte de dados e usar um pequeno RADIUS de cluster e use a propriedade clusters `point_count` como um peso para o mapa de altura. Quando o raio do cluster tiver apenas alguns pixels de tamanho, haverá pouca diferença visual no mapa de calor renderizado. Usar um raio de cluster maior melhorará o desempenho mais, mas pode reduzir a resolução do mapa de calor renderizado.

```javascript
var layer = new atlas.layer.HeatMapLayer(source, null, {
   weight: ['get', 'point_count']
});
```

Saiba mais nos [mapas de cluster e de calor deste documento](clustering-point-data-web-sdk.md #clustering-and-the-heat-maps-layer)

### <a name="keep-image-resources-small"></a>Manter os recursos de imagem pequenos

As imagens podem ser adicionadas ao Sprite da imagem de mapas para processar ícones em uma camada de símbolo ou padrões em uma camada de polígono. Mantenha essas imagens pequenas para minimizar a quantidade de dados que precisam ser baixados e a quantidade de espaço que eles ocupam na imagem de mapas Sprite. Ao usar uma camada de símbolo que dimensiona o ícone usando a `size` opção, use uma imagem que seja o tamanho máximo que seu plano deve exibir no mapa e não maior. Isso garantirá que o ícone seja renderizado com alta resolução, minimizando os recursos que ele usa. Além disso, o SVG também pode ser usado como um formato de arquivo menor para imagens de ícone simples.

## <a name="optimize-expressions"></a>Otimizar expressões

As [expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md) fornecem muita flexibilidade e potência para filtrar e estilizar dados no mapa. Há muitas maneiras pelas quais as expressões podem ser otimizadas. Aqui estão algumas dicas.

### <a name="reduce-the-complexity-of-filters"></a>Reduzir a complexidade dos filtros

Filtra o loop de todos os dados em uma fonte de dados e verifica se cada filtro corresponde à lógica no filtro. Se os filtros se tornarem complexos, isso poderá causar problemas de desempenho. Algumas estratégias possíveis para resolver isso incluem o seguinte.

* Se você estiver usando blocos de vetor, divida os dados em diferentes camadas de origem.
* Se estiver usando a `DataSource` classe, divida esses dados em fontes de dados separadas. Tente balancear o número de fontes de dados com a complexidade do filtro. Muitas fontes de dados também podem causar problemas de desempenho, portanto, talvez seja necessário fazer alguns testes para descobrir o que funciona melhor para seu cenário.
* Ao usar um filtro complexo em uma camada, considere usar várias camadas com expressões de estilo para reduzir a complexidade do filtro. Evite criar várias camadas com estilos codificados quando expressões de estilo podem ser usadas como um grande número de camadas também podem causar problemas de desempenho.

### <a name="make-sure-expressions-dont-produce-errors"></a>Certifique-se de que as expressões não produzam erros

As expressões geralmente são usadas para gerar código para executar cálculos ou operações lógicas no momento da renderização. Assim como o código no restante do seu aplicativo, certifique-se de que os cálculos e a detecção lógica e não sejam propensos a erros. Erros em expressões causarão problemas na avaliação da expressão, o que pode resultar em problemas de desempenho e renderização reduzidos.

Um erro comum de ser cuidadoso é ter uma expressão que dependa de uma propriedade de recurso que pode não existir em todos os recursos. Por exemplo, o código a seguir usa uma expressão para definir a Propriedade Color de uma camada Bubble para a `myColor` propriedade de um recurso.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: ['get', 'myColor']
});
```

O código acima funcionará bem se todos os recursos na fonte de dados tiverem uma `myColor` propriedade, e o valor dessa propriedade for uma cor. Isso pode não ser um problema se você tiver controle total dos dados na fonte de dados e se souber que todos os recursos terão uma cor válida em uma `myColor` propriedade. Dito isso, para tornar esse código seguro contra erros, uma `case` expressão pode ser usada com a `has` expressão para verificar se o recurso tem a `myColor` propriedade. Se tiver, a `to-color` expressão de tipo poderá ser usada para tentar converter o valor dessa propriedade em uma cor. Se a cor for inválida, uma cor de fallback poderá ser usada. O código a seguir demonstra como fazer isso e define a cor de fallback como verde.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Check to see if the feature has a 'myColor' property.
        ['has', 'myColor'],

        //If true, try validating that 'myColor' value is a color, or fallback to 'green'. 
        ['to-color', ['get', 'myColor'], 'green'],

        //If false, return a fallback value.
        'green'
    ]
});
```

### <a name="order-boolean-expressions-from-most-specific-to-least-specific"></a>Ordenar expressões booleanas da mais específica para a menos específica

Ao usar expressões boolianas que contêm vários testes condicionais, ordene os testes condicionais do mais específico para o menos específico. Ao fazer isso, a primeira condição deve reduzir a quantidade de dados em que a segunda condição precisa ser testada, reduzindo assim o número total de testes condicionais que precisam ser executados.

### <a name="simplify-expressions"></a>Simplificar expressões

As expressões podem ser poderosas e, às vezes, complexas. Quanto mais simples for uma expressão, mais rápida ela será avaliada. Por exemplo, se uma comparação simples for necessária, uma expressão como `['==', ['get', 'category'], 'restaurant']` seria melhor do que usar uma expressão de correspondência como `['match', ['get', 'category'], 'restaurant', true, false]` . Nesse caso, se a propriedade que está sendo verificada for um valor booliano, uma `get` expressão seria ainda mais simples `['get','isRestaurant']` .

## <a name="web-sdk-troubleshooting"></a>Solução de problemas do SDK da Web

Veja a seguir algumas dicas para depurar alguns dos problemas comuns encontrados ao desenvolver com o SDK da Web do Azure Maps.

**Por que o mapa não é exibido quando eu carrego o controle da Web?**

Faça o seguinte:

* Certifique-se de ter adicionado as opções de autenticação adicionadas ao mapa. Se isso não for adicionado, o mapa será carregado com uma tela em branco, pois não poderá acessar os dados do mapa base sem autenticação e os erros 401 aparecerão na guia rede das ferramentas de desenvolvedor do navegador.
* Verifique se você tem uma conexão com a Internet.
* Verifique se há erros nas ferramentas de desenvolvedor do navegador no console. Alguns erros podem fazer com que o mapa não seja renderizado. Depure o aplicativo.
* Verifique se você está usando um [navegador com suporte](supported-browsers.md).

**Todos os meus dados estão aparecendo no outro lado do mundo, o que está acontecendo?**
As coordenadas, também chamadas de posições, nos SDKs do mapas do Azure se alinham com o formato geoespacial padrão do setor do `[longitude, latitude]` . Esse mesmo formato também é como as coordenadas são definidas no esquema geojson; os dados principais formatados usados nos SDKs do mapas do Azure. Se seus dados estiverem aparecendo no lado oposto do mundo, isso provavelmente ocorrerá devido aos valores de longitude e latitude sendo revertidos nas informações de coordenada/posição.

**Por que os marcadores HTML aparecem no local errado no controle da Web?**

Verifique a:

* Se estiver usando conteúdo personalizado para o marcador, verifique se as `anchor` `pixelOffset` Opções e estão corretas. Por padrão, o centro inferior do conteúdo é alinhado com a posição no mapa.
* Verifique se o arquivo CSS do Azure Maps foi carregado.
* Inspecione o elemento DOM do marcador HTML para ver se qualquer CSS do seu aplicativo foi anexado ao marcador e está afetando sua posição.

**Por que os ícones ou o texto na camada de símbolos aparecem no local errado?**
Verifique se as `anchor` Opções e `offset` estão configuradas corretamente para se alinhar com a parte da imagem ou do texto que você deseja alinhar com a coordenada no mapa.
Se o símbolo estiver apenas fora de lugar quando o mapa for girado, marque a `rotationAlignment` opção. Por padrão, os símbolos giraremos com o visor do Maps para que eles pareçam verticais para o usuário. No entanto, dependendo do seu cenário, pode ser desejável bloquear o símbolo para a orientação do mapa. Defina a `rotationAlignment` opção como `’map’` para fazer isso.
Se o símbolo estiver somente fora de lugar quando o mapa estiver esmaecido/inclinado, marque a `pitchAlignment` opção. Por padrão, os símbolos permanecerão verticais com o visor do Maps à medida que o mapa for arestado ou inclinado. No entanto, dependendo do seu cenário, pode ser desejável bloquear o símbolo para o tom do mapa. Defina a `pitchAlignment` opção como `’map’` para fazer isso.

**Por que nenhum dos meus dados aparecem no mapa?**

Verifique a:

* Verifique se há erros no console nas ferramentas de desenvolvedor do navegador.
* Verifique se uma fonte de dados foi criada e adicionada ao mapa e se a fonte de dados foi conectada a uma camada de renderização que também foi adicionada ao mapa.
* Adicione pontos de interrupção em seu código e percorra-o para garantir que os dados estejam sendo adicionados à fonte de dados e que a fonte de dados e as camadas estejam sendo adicionadas ao mapa sem que ocorram erros.
* Tente remover as expressões controladas por dados da camada de renderização. É possível que um deles possa ter um erro que está causando o problema.

**Posso usar o SDK da Web do Azure Maps em um iframe protegido?**

Sim. Observe que o [Safari tem um bug](https://bugs.webkit.org/show_bug.cgi?id=170075) que impede que os Iframes em área restrita executem Web Workers, o que é o requisito do SDK para Web do Azure Maps. A solução é adicionar a `"allow-same-origin"` marca à propriedade sandbox do iframe.

## <a name="get-support"></a>Obtenha suporte

A seguir estão as diferentes maneiras de obter suporte para mapas do Azure, dependendo do seu problema.

**Como fazer relatar um problema de dados ou um problema com um endereço?**

O mapas do Azure tem uma ferramenta de comentários de dados em que os problemas de dados podem ser relatados e rastreados. [https://feedback.azuremaps.com/](https://feedback.azuremaps.com/) Cada problema enviado gera uma URL exclusiva que você pode usar para acompanhar o progresso do problema de dados. O tempo necessário para resolver um problema de dados varia de acordo com o tipo de problema e como é fácil verificar se a alteração está correta. Depois de corrigido, o serviço de renderização verá a atualização na atualização semanal, enquanto outros serviços, como geocodificação e roteamento verão a atualização na atualização mensal. Instruções detalhadas sobre como relatar um problema de dados são fornecidas neste [documento](how-to-use-feedback-tool.md).

**Como fazer relatar um bug em um serviço ou API?**

https://azure.com/support

**Onde obtenho ajuda técnica para o Azure Maps?**

Se estiver relacionado ao Visual do Azure Maps no Power BI: https://powerbi.microsoft.com/support/ para todos os outros serviços do Azure Maps: https://azure.com/support ou os fóruns de desenvolvedor: https://docs.microsoft.com/answers/topics/azure-maps.html

**Como fazer fazer uma solicitação de recurso?**

Faça uma solicitação de recurso em nosso site de voz do usuário: https://feedback.azure.com/forums/909172-azure-maps

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para obter mais dicas sobre como melhorar a experiência do usuário em seu aplicativo.

> [!div class="nextstepaction"]
> [Tornar seu aplicativo acessível](map-accessibility.md)

Saiba mais sobre a terminologia usada pelo Azure Maps e pela indústria geoespacial.

> [!div class="nextstepaction"]
> [Glossário do Azure Mapas](glossary.md)
