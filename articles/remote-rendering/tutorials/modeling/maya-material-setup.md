---
title: Configurar materiais de renderização baseada fisicamente no Maya
description: Explica como configurar materiais de renderização baseada fisicamente no Maya e exportá-los para o formato FBX.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 56aa0d91372ac2d21a20f28b1044f0811c716b0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358025"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-maya"></a>Tutorial: Configurar materiais de renderização baseada fisicamente no Maya

## <a name="overview"></a>Visão geral
Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Atribuir materiais com iluminação avançada aos objetos na cena.
> * Processar a instanciação de objetos e materiais.
> * Exportar uma cena para o formato FBX e selecionar opções importantes.

A criação de [materiais de PBR (renderização baseada fisicamente)](../../overview/features/pbr-materials.md) no Maya é uma tarefa relativamente simples. Ela é semelhante, de muitas maneiras, à configuração de PBR em outros aplicativos de criação de conteúdo, como o 3DS Max. Este tutorial é um guia para a instalação básica do sombreador de PBR e a exportação do FBX para projetos do Azure Remote Rendering. 

A cena de exemplo deste tutorial contém vários objetos de caixa poligonais. Diferentes materiais, como madeira, metal, metal pintado, plástico e borracha são atribuídos a eles. Em geral, cada material contém todas ou a maioria das seguintes texturas:

* **Albedo**, que é o mapa de cores do material e também é chamado de **Difuso** ou **BaseColor**.
* **Metalicidade**, que determina se um material é metálico e quais partes dele são metálicas. 
* **Rugosidade**, que determina quão áspera ou lisa é uma superfície e afeta a nitidez ou o desfoque dos reflexos e dos realces em uma superfície.
* **Normal**, que adiciona detalhes a uma superfície sem precisar adicionar mais polígonos. Exemplos de detalhes podem ser furos e entalhes em uma superfície metálica ou textura em madeira.
* **Oclusão de Ambiente**, que é usada para adicionar um sombreamento suave e sombras de contato a um modelo. Trata-se de um mapa em escala de cinza que indica quais áreas de um modelo recebem iluminação total (branco) ou sombreamento total (preto). 

## <a name="prerequisites"></a>Pré-requisitos
* Autodesk Maya 2017 ou mais recente

## <a name="set-up-materials-in-the-scene"></a>Configurar materiais na cena
Veja como configurar um material de PBR no Maya.

Como você verá na cena de exemplo, criamos diversos objetos de caixa. Cada objeto representa um tipo diferente de material. Conforme mostrado na imagem, cada um desses objetos recebeu um nome adequado.

O Azure Remote Rendering usa metros para a medição e a direção para cima é o eixo Y. Antes de começar a criar ativos, recomendamos que você defina as unidades de cena no Maya como metros. Para exportar, defina as unidades como metros nas configurações de exportação do FBX.

> [!TIP]
> Dê aos ativos do seu modelo nomes adequados, com base na parte relevante ou no tipo de material. Nomes significativos facilitam a navegação em cenas com muitos objetos.

![Nomes de objetos](media/object-names.jpg)

Depois de criar ou adquirir algumas texturas, você também pode criar texturas exclusivas. Você pode usar aplicativos de texturização, como o Quixel Suite, o PhotoShop ou o Substance Suite, ou pode obter texturas de blocos genéricas de outras fontes.

Para aplicar texturas ao modelo:

1. No visor da cena, selecione o modelo ou a geometria e clique nela com o botão direito do mouse. No menu que aparece, selecione **Atribuir Novo Material**.
1. Na caixa de diálogo **Atribuir Novo Material**, vá até **Maya** > **Stingray PBS**. Esta ação atribui um material de PBR ao seu modelo. 

No Maya 2020, diversos sombreadores de PBR estão disponíveis. Eles incluem o **Maya Standard Surface**, o **Arnold Standard Surface** e o **Stingray PBR**. O **Maya Standard Surface Shader** ainda não pode ser exportado por meio do plug-in do FBX 2020. O **Arnold Standard Surface Shader** pode ser exportado com arquivos FBX. Na maioria dos outros aspectos, ele é idêntico ao **Maya Standard Surface Shader**. Ele é análogo ao **Material Físico** no 3D Studio Max.

O **Stingray PBR Shader** é compatível com muitos outros aplicativos e corresponde de maneira mais próxima aos requisitos do Azure Remote Rendering. Ele tem suporte desde o Maya 2017. Quando esse tipo de material é visualizado no visor, ele é semelhante ao que é visualizado posteriormente no Azure Remote Rendering.

![Material Stingray](media/stingray-material.jpg)

Com o material atribuído ao seu ativo e com um nome adequado, agora você pode atribuir suas diferentes texturas. As imagens a seguir mostram onde cada tipo de textura se encaixa no material de PBR. O material Stingray PBR permite que você selecione quais atributos pode ativar. Antes de conectar seus mapas de textura, você precisa ativar os atributos relevantes.

![Configuração de material](media/material-setup.jpg)

Dê um nome adequado a seus materiais levando em conta seu uso ou tipo. Um material que é usado em um bloco exclusivo pode ser nomeado de acordo com esse bloco. Um material que é usado em uma gama maior de áreas pode ser nomeado de acordo com suas propriedades ou seu tipo.

Atribua as texturas conforme mostrado na imagem.

![Configuração de texturas](media/texture-setup.jpg)

Com os materiais de PBR criados e configurados, considere [criar uma instância dos objetos](../../how-tos/conversion/configure-model-conversion.md#instancing) na cena. Criar uma instância dos objetos semelhantes na cena, como porcas, parafusos, pregos e arruelas gera uma economia significativa no tamanho do arquivo. As instâncias de um objeto mestre podem ter a própria escala, rotação e transformações, de maneira que possam ser posicionadas conforme necessário na cena. 

No Maya, o processo de criação de instância é simples.

1. No menu **Editar**, vá para **Duplicar Especial** para abrir as opções.
1. Na caixa de diálogo **Opções para Duplicar Especial**, para **Tipo de geometria** selecione a opção **Instância**. 
1. Selecione **Duplicar Especial**.

   ![A captura de tela mostra uma janela do Maya com a caixa de diálogo Duplicar Opções Especiais aberta e a opção Duplicar Especial selecionada.](media/instancing.jpg)

Essa ação cria uma instância de seu objeto. Você pode movê-lo, girá-lo ou dimensioná-lo de maneira independente do objeto pai e das outras instâncias desse pai. 

As alterações feitas na instância enquanto ela está no modo de componente são transmitidas para todas as instâncias do objeto. Por exemplo, você pode trabalhar com os componentes de um objeto em instância, como os vértices as faces de um polígono. Certifique-se de que você deseja que as alterações feitas afetem todas essas instâncias. 

Na cena de exemplo, cada objeto de caixa individual foi separado em uma instância. Essa ação será relevante na exportação da cena para o formato FBX.

![Visão geral da cena](media/scene-overview.jpg)

> [!TIP]
> Crie instâncias na cena à medida que você prosseguir. Substituir cópias por objetos separados em instâncias mais tarde é extremamente difícil. 

## <a name="fbx-export-process"></a>Processo de exportação do FBX

Vamos passar para a exportação do FBX de sua cena ou de ativos da cena. Quando você exporta ativos, faz sentido selecionar somente os objetos ou ativos da cena que deseja exportar. Por exemplo, você pode ter 100 objetos em uma cena. Se você quer usar apenas 30 deles, não faz sentido exportar toda a cena. 

Para fazer a seleção:

1. Vá até **Arquivo** > **Exportar Seleção** para abrir a caixa de diálogo **Exportar Seleção**.
1. Na caixa **Arquivos do tipo**, selecione **Exportação do FBX** para exibir as configurações de exportação do FBX. As configurações primárias para exportação do FBX estão realçadas em vermelho na imagem.

   ![Exportação do FBX](media/FBX-exporting.jpg)

Dependendo de seus requisitos, por exemplo, talvez você queira enviar um ativo para um cliente. Talvez você não queira enviar um grande número de arquivos de textura com o ativo. Você pode optar por inserir as texturas no arquivo FBX exportado. Com essa opção, você tem apenas um arquivo para empacotar, mas o tamanho do ativo FBX aumentará significativamente. Você pode habilitar a opção de inserir texturas selecionando a opção **Inserir Mídia** conforme mostrado abaixo.

> [!TIP]
> Neste exemplo, o arquivo foi nomeado de maneira a refletir essa condição. Esse estilo de nomenclatura é uma boa maneira de manter o controle de seus ativos. 

Depois de definir a configuração para exportação, selecione **Exportar Seleção** no canto inferior direito.

![Como inserir a mídia](media/embedding-media.jpg)

## <a name="conclusion"></a>Conclusão

Em geral, esse tipo de material parece mais realista, pois se baseia na física da luz do mundo real. Ele cria um efeito de imersão adicional, em que a cena parece existir no mundo real.

## <a name="next-steps"></a>Próximas etapas

Agora você sabe como configurar materiais com iluminação avançada para objetos em uma cena. Você também sabe como exportar os objetos para o formato FBX compatível com o Azure Remote Rendering. A próxima etapa será converter o arquivo FBX e visualizá-lo no Azure Remote Rendering.

> [!div class="nextstepaction"]
> [Início Rápido: Converter um modelo para renderização](../../quickstarts\convert-model.md)