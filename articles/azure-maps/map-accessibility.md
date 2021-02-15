---
title: Criar um aplicativo de mapas com o Azure Mapas | Microsoft Azure Mapas
description: Saiba mais sobre as considerações de acessibilidade no Azure Maps. Veja quais recursos estão disponíveis para tornar os aplicativos de mapa acessíveis e exibir dicas de acessibilidade.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: ec88437a0fad3a6bd94a67a5ef5c75b3e506f9e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88006206"
---
# <a name="building-an-accessible-application"></a>Criando um aplicativo acessível

Mais de 20% dos usuários de Internet precisam de aplicativos Web acessíveis. Assim, é importante certificar-se de que o seu aplicativo seja desenvolvido de forma que qualquer usuário possa usá-lo facilmente. Em vez de pensar na acessibilidade como um conjunto de tarefas a serem concluídas, pense nela como parte de sua experiência geral do usuário. Quanto mais acessível seu aplicativo, mais pessoas poderão usá-lo. 

Quando se trata de conteúdo interativo avançado como um mapa, algumas considerações comuns de acessibilidade são:
- Ofereça suporte ao leitor de tela para usuários que têm dificuldade em visualizar o aplicativo Web.
- Tenha vários métodos para interagir e navegar pelo aplicativo Web, como mouse, toque e teclado.
- Verifique se o contraste de cores é que as cores não se misturam e se tornam difíceis de distinguir umas das outras. 

O SDK da Web do Azure Maps vem predefinido com muitos recursos de acessibilidade, como:
- Descrições do leitor de tela quando o mapa se move e quando o usuário se concentra em um controle ou pop-up.
- Suporte para mouse, toque e teclado.
- Suporte acessível ao contraste de cores no estilo de mapa rodoviário.
- Suporte de alto contraste.

Detalhes completos de conformidade da acessibilidade para todos os produtos da Microsoft podem ser encontrados [aqui](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/). Pesquise "Web do Azure Mapas" para encontrar o documento especificamente para o SDK da Web do Azure Mapas. 

## <a name="navigating-the-map"></a>Navegar no mapa

Há várias maneiras diferentes de ampliar, deslocar, girar e inclinar o mapa. A seguir, são detalhadas todas as diferentes maneiras de navegar no mapa.

**Ampliar o mapa**

- Com o mouse, clique duas vezes no mapa para ampliar um nível.
- Com o mouse, role a roda para ampliar o mapa.
- Com a tela touch, toque no mapa com dois dedos e junte-os para reduzir, ou afaste-os para ampliar.
- Com a tela touch, toque duas vezes no mapa para ampliar um nível.
- Com o mapa focalizado, use o sinal de mais (`+`) ou o de igual (`=`) para ampliar um nível.
- Com o mapa focalizado, use o sinal de menos, hífen (`-`) ou sublinhado (`_`) para reduzir um nível.
- Com o controle de ampliação usando o mouse, o toque ou as teclas Tab e Enter do teclado.
- Pressione e segure o botão `Shift` e pressione o botão esquerdo do mouse no mapa e arraste para delimitar uma área para ampliar o mapa.
- Usando alguns pads multitoque, arrastando dois dedos para ampliar ou reduzir para ampliar.

**Deslocar o mapa**

- Com o mouse, pressione o botão esquerdo do mouse no mapa e arraste em qualquer direção.
- Com a tela touch, toque no mapa e arraste em qualquer direção.
- Com o mapa focalizado, use as teclas de direção para mover o mapa.

**Girar o mapa**

- Com o mouse, pressione o botão direito do mouse no mapa e arraste para a esquerda ou para a direita. 
- Com a tela touch, toque no mapa com dois dedos e gire.
- Com o mapa focalizado, use a tecla Shift e as teclas de direção para a esquerda ou para a direita.
- Com o controle de rotação usando o mouse, o toque ou as teclas Tab e Enter do teclado.

**Inclinar o mapa**

- Com o mouse, pressione o botão direito do mouse no mapa e arraste para cima ou para baixo. 
- Com a tela de toque, toque no mapa com dois dedos e arraste-os para cima ou para baixo juntos.
- Com o mapa focalizado, use a tecla Shift e as teclas de direção para cima ou para baixo. 
- Com o controle de inclinação usando o mouse, o toque ou as teclas Tab e Enter do teclado.

## <a name="change-the-map-style"></a>Alterar o estilo do mapa

Nem todos os desenvolvedores desejam que todos os estilos de mapa possíveis estejam disponíveis em seus aplicativos. Se o desenvolvedor exibir o controle do seletor de estilos no mapa, o usuário poderá alterar o estilo do mapa usando o mouse, toque ou teclado com a tecla Tab ou Enter. O desenvolvedor pode especificar quais estilos de mapa deseja disponibilizar no controle do seletor de estilos de mapa. Além disso, o desenvolvedor pode definir e alterar o estilo de mapa de forma programática.

**Usar alto contraste**

- Quando o controle de mapa é carregado, ele verifica se o alto contraste está ativado e o navegador é compatível.
- O controle de mapa não monitora o modo de alto contraste do dispositivo. Se o modo do dispositivo for alterado, o mapa não será. Assim, o usuário precisará recarregar o mapa atualizando a página.
- Quando o alto contraste for detectado, o estilo do mapa mudará automaticamente para alto contraste e todos os controles internos usarão um estilo de alto contraste. Por exemplo, ZoomControl, PitchControl, CompassControl, StyleControl e outros controles internos, usarão um estilo de alto contraste.
- Há dois tipos de alto contraste, claro e escuro. Se o tipo de alto contraste puder ser detectado pelos controles de mapa, o comportamento do mapa será ajustado de acordo. Se claro, o estilo de mapa grayscale_light será carregado. Se o tipo não puder ser detectado ou for escuro, o estilo de high_contrast_dark será carregado.
- Ao criar controles personalizados, é útil saber se os controles internos estão usando um estilo de alto contraste. Os desenvolvedores podem adicionar uma classe css na div do contêiner de mapa para verificar. As classes css que seriam adicionadas são `high-contrast-dark` e `high-contrast-light`. Para verificar usando o JavaScript, use:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

ou use:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Atalhos do teclado

O mapa tem vários atalhos de teclado internos que facilitam o uso do mapa. Esses atalhos de teclado funcionam quando o mapa está focalizado.

| Chave      | Ação                            |
|----------|-----------------------------------|
| `Tab` | Navegar pelos controles e pop-ups no mapa. |
| `ESC` | Mover o foco de qualquer elemento no mapa para o elemento de mapa de nível superior. |
| `Ctrl` + `Shift` + `D` | Alternar nível de detalhe do leitor de tela.  |
| Tecla de seta para a esquerda | Deslocar o mapa 100 pixels para a esquerda |
| Tecla de seta para a direita | Deslocar o mapa 100 pixels para a direita |
| Seta para baixo | Deslocar o mapa 100 pixels para baixo |
| Tecla de seta para cima | Deslocar o mapa 100 pixels para cima |
| `Shift` + seta para cima | Aumentar a inclinação do mapa em 10 graus |
| `Shift` + seta para baixo | Diminuir a inclinação do mapa em 10 graus |
| `Shift` + seta para a direita | Girar o mapa 15 graus no sentido horário |
| `Shift` + seta para a esquerda | Girar o mapa 15 graus no sentido anti-horário |
| Sinal de mais (`+`) ou <sup>*</sup>sinal de igual (`=`) | Ampliar |
| Sinal de menos, hífen (`-`) ou <sup>*</sup>sublinhado (`_`) | Reduzir | 
| `Shift` + arrastar com o mouse sobre o mapa para delinear a área | Ampliar área |

<sup>*</sup> Esses atalhos-chave geralmente compartilham a mesma tecla em um teclado. Esses atalhos foram adicionados para melhorar a experiência do usuário. Também não importa se o usuário usa a tecla Shift ou não para esses atalhos.

## <a name="screen-reader-support"></a>Suporte ao leitor de tela

Os usuários podem navegar o mapa usando o teclado. Se um leitor de tela estiver em execução, o mapa notificará o usuário de alterações em seu estado. Por exemplo, os usuários serão notificados das alterações do mapa quando o mapa é deslocado ou ampliado. Por padrão, o mapa fornece descrições simplificadas que excluem o nível de ampliação e as coordenadas do centro do mapa. O usuário pode alternar o nível de detalhe dessas descrições usando o atalho do teclado `Ctrl` + `Shift` + `D`.

Qualquer informação adicional é colocada no mapa de base deve ter informações textuais correspondentes para os usuários de leitores de tela. Adicione [Aplicativos ARIA (Access Rich Internet Applications)](https://www.w3.org/WAI/standards-guidelines/aria/), alt e title, onde apropriado. 

## <a name="make-popups-keyboard-accessible"></a>Tornar o teclado de pop-ups acessível

Um marcador ou símbolo geralmente é usado para representar um local no mapa. Informações adicionais sobre o local geralmente são exibidas em um pop-up quando o usuário interage com o marcador. Na maioria dos aplicativos, os pop-ups aparecem quando um usuário clica ou toca em um marcador. No entanto, clicar e tocar requer que o usuário use um mouse e uma tela touch, respectivamente. Uma boa prática é tornar os pop-ups acessíveis ao usar um teclado. Essa funcionalidade pode ser obtida criando um pop-up para cada ponto de dados e adicionando-o ao mapa. 

O exemplo a seguir carrega pontos de interesse no mapa usando uma camada de símbolo e adiciona um pop-up ao mapa para cada ponto de interesse. Uma referência para cada pop-up é armazenada nas propriedades de cada ponto de dados. Ela também pode ser recuperada para um marcador, como quando um marcador é clicado. Com o mapa focalizado, pressionar a tecla Tab permitirá ao usuário percorrer cada pop-up no mapa.

<br/>

<iframe height='500' scrolling='no' title='Criar um aplicativo acessível' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Criar um aplicativo acessível</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Dicas adicionais de acessibilidade

Aqui estão algumas dicas adicionais para tornar seu aplicativo de mapeamento da Web mais acessível.

- Se estiver exibindo muitos dados de pontos interativos no mapa, considere reduzir a desordem e usar o clustering. 
- Certifique-se de que a taxa de contraste de cores entre texto/símbolos e cores de tela de fundo seja de 4,5:1 ou mais.
- Mantenha as mensagens do leitor de tela (atributos ARIA, alt e title) curtas, descritivas e significativas. Evite jargões e acrônimos desnecessários.
- Tente otimizar as mensagens enviadas ao leitor de tela para fornecer informações curtas e significativas, fáceis de digerir pelo usuário. Por exemplo, se você quiser atualizar o leitor de tela em uma alta frequência, como quando o mapa está sendo movido, considere os seguintes pontos:
    - Aguarde até que o mapa termine de se deslocar para atualizar o leitor de tela.
    - Limite as atualizações para uma vez a cada poucos segundos. 
    - Combine mensagens de maneira lógica. 
- Evite usar cores como a única forma de transmitir informações. Use texto, ícones ou padrões para complementar ou substituir as cores. Algumas considerações:
    - Se estiver usando uma camada de bolhas para mostrar o valor relativo entre os pontos de dados, considere dimensionar o raio de cada bolha, colorir a bolha ou ambos. 
    - Considere usar uma camada de símbolos com ícones diferentes para diferentes categorias de métricas, como triângulos, estrelas e quadrados. A camada de símbolos também oferece suporte ao dimensionamento do tamanho do ícone. Um rótulo de texto também pode ser exibido.
    - Ao exibir dados de linha, a largura pode ser usada para representar peso ou tamanho. Um padrão de matriz de traços pode ser usado para representar diferentes categorias de linhas. Uma camada de símbolos pode ser usada em combinação com uma linha para sobrepor ícones ao longo da linha. Usar um ícone de seta é útil para mostrar o fluxo ou a direção da linha.
    - No caso de dados de polígono, um padrão, como listras, pode ser usado como uma alternativa à cor. 
- Algumas visualizações, como mapas de calor, camadas de bloco e imagem, não são acessíveis para usuários com deficiências visuais. Algumas considerações:
    - Inclua no leitor de tela a descrição do que a camada está exibindo quando adicionada ao mapa. Por exemplo, se uma camada de bloco de radar meteorológico for exibida, inclua a descrição "Os dados do radar meteorológico estão sobrepostos no mapa" no leitor de tela.
- Limite a quantidade de funcionalidades que exigem o foco do mouse. Essas funcionalidades ficarão inacessíveis aos usuários que estão usando um teclado ou dispositivo de toque para interagir com o aplicativo. Observe que ainda é uma boa prática ter um estilo de foco instantâneo para conteúdo interativo, como ícones, links e botões clicáveis.
- Tente navegar pelo aplicativo usando o teclado. Verifique se a ordem de tabulação é lógica.
- Se estiver criando atalhos de teclado, tente limitá-los a duas teclas ou menos. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a acessibilidade nos módulos do SDK para Web.

> [!div class="nextstepaction"]
> [Acessibilidade das ferramentas de desenho](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre como desenvolver aplicativos acessíveis com o Microsoft Learn:

> [!div class="nextstepaction"]
> [Roteiro de aprendizagem de notificação digital de acessibilidade em ação](https://ready.azurewebsites.net/learning/track/2940)

Confira estas ferramentas úteis de acessibilidade:
> [!div class="nextstepaction"]
> [Desenvolvimento de aplicativos acessíveis](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [Visão geral do WAI-ARIA](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Ferramenta de Avaliação de Acessibilidade na Web (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [Verificador de contraste de cores WebAim](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Simulador de Visão No Coffee](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
