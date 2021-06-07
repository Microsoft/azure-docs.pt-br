---
title: Câmera de profundidade do Azure Kinect-DK
description: Entenda os princípios operacionais e os principais recursos da câmera de profundidade no Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, câmera de profundidade, TOF, princípios, desempenho, invalidação
ms.openlocfilehash: 22f04b983ed7c6a2ab19a5c1c709621655ee31c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85276347"
---
# <a name="azure-kinect-dk-depth-camera"></a>Câmera de profundidade do Azure Kinect-DK

Esta página aborda como usar a câmera de profundidade em seu Azure Kinect DK. A câmera de profundidade é a segunda das duas câmeras. Conforme abordado nas seções anteriores, a outra câmera é a câmera RGB.  

## <a name="operating-principles"></a>Princípios operacionais

A câmera de profundidade do Azure Kinect DK implementa o princípio ToF (tempo de vôo) de onda modulada contínua (AMCW). A câmera converte a iluminação modulada no espectro do NIR (Near-IR) na cena. Em seguida, ele registra uma medição indireta do tempo que leva a luz para viajar da câmera para a cena e de volta.

Essas medidas são processadas para gerar um mapa de profundidade. Um mapa de profundidade é um conjunto de valores de coordenadas Z para cada pixel da imagem, medido em unidades de milímetros.

Juntamente com um mapa de profundidade, também obtemos uma chamada de leitura de IR de modo claro. O valor de pixels na leitura de IR limpa é proporcional à quantidade de luz retornada da cena. A imagem é semelhante a uma imagem de IR regular. A figura a seguir mostra um exemplo de mapa de profundidade (à esquerda) e uma imagem de IR do modo de limpeza correspondente (direita).

![Profundidade e IR lado a lado](./media/concepts/depth-camera-depth-ir.png)

## <a name="key-features"></a>Principais recursos

As características técnicas da câmera de profundidade incluem:

- chip de imagem ToF de 1 megapixel com tecnologia de pixel avançado, permitindo frequências de modulação e precisão de profundidade mais altas.
- Dois diodos de laser NIR habilitam modos de profundidade de FoV (campo de exibição) Near e Wide.
- O menor pixel de ToF do mundo, 3,5 μm por 3,5 μm.
- Seleção de lucro automática por pixel habilitando um grande intervalo dinâmico, permitindo que objetos próximos e longe sejam capturados corretamente.
- Obturador global que permite um desempenho aprimorado no luz solar.
- Método de cálculo de profundidade de várias fases que permite uma precisão robusta mesmo na presença de chip, laser e variação de fonte de energia.
- Erros sistemáticos e aleatórios baixos.

![Módulo de profundidade](./media/concepts/depth-camera-depth-module.jpg)

A câmera de profundidade transmite imagens de IR moduladas brutas para o PC host. No PC, o software do mecanismo de profundidade acelerada da GPU converte o sinal bruto em mapas de profundidade.A câmera de profundidade dá suporte a vários modos. Os modos **de campo de exibição estreito (FOV)** são ideais para cenas com extensões menores em dimensões X e Y, mas extensões maiores na dimensão Z. Se a cena tiver extensões X e Y grandes, mas intervalos Z menores, os **modos de FOV largos** serão mais adequados.

A câmera de profundidade dá suporte a **modos de compartimentalização 2x2** para estender o intervalo Z em comparação com os **modos de unbinned** correspondentes. O compartimentalização é feito com o custo de reduzir a resolução da imagem. Todos os modos podem ser executados em até 30 quadros por segundo (FPS) com exceção do modo de 1 megapixel (MP) que é executado com uma taxa máxima de quadros de 15 fps. A câmera de profundidade também fornece um **modo ir passivo**. Nesse modo, os iluminadores da câmera não estão ativos e apenas a iluminação ambiental é observada.

## <a name="camera-performance"></a>Desempenho da câmera

O desempenho da câmera é medido como erros sistemáticos e aleatórios.

### <a name="systematic-error"></a>Erro sistemático

O erro sistemático é definido como a diferença entre a profundidade medida após a remoção de ruído e a profundidade correta (verdade). Computamos a média temporal em muitos quadros de uma cena estática para eliminar o ruído de profundidade tanto quanto possível. Mais precisamente, o erro sistemático é definido como:

![Erro sistemático de profundidade](./media/concepts/depth-camera-systematic-error.png)

Onde *d <sub>t</sub>* denota a profundidade da medida no tempo *t*, *N* é o número de quadros usados no procedimento de média e *d <sub>gt</sub>* é a profundidade da verdade.

A especificação de erro sistemática da câmera de profundidade é a exclusão de MPI (interferência de vários caminhos). MPI é quando um pixel de sensor integra a luz refletida por mais de um objeto. A MPI é parcialmente mitigada em nossa câmera de profundidade usando frequências de modulação mais altas, juntamente com a invalidação de profundidade, que apresentaremos mais tarde.

### <a name="random-error"></a>Erro aleatório

Vamos supor que levamos 100 imagens do mesmo objeto sem mover a câmera. A profundidade do objeto será um pouco diferente em cada uma das imagens 100. Essa diferença é causada por ruído de captura. Ruído de captura é o número de fótons que o sensor varia de acordo com um fator aleatório ao longo do tempo. Definimos esse erro aleatório em uma cena estática como o desvio padrão de profundidade ao longo do tempo calculado como:

![Erro aleatório de profundidade](./media/concepts/depth-camera-random-error.png)

Onde *N* denota o número de medidas de profundidade, *d <sub>t</sub>* representa a medida de profundidade no tempo *t* e *d* denota o valor médio calculado em todas as medidas de profundidade *d <sub>t</sub>*.

## <a name="invalidation"></a>Invalidação

Em determinadas situações, a câmera de profundidade pode não fornecer valores corretos para alguns pixels. Nessas situações, os pixels de profundidade são invalidados. Os pixels inválidos são indicados pelo valor de profundidade igual a 0. Os motivos para o mecanismo de profundidade não conseguir produzir os valores corretos incluem:

- Fora da máscara de iluminação de IR ativa
- Sinal de IR saturado
- Sinal IR baixo
- Exceção de filtro
- Interferência de vários caminhos

### <a name="illumination-mask"></a>Máscara de iluminação

Os pixels são invalidados quando estão fora da máscara de iluminação de IR ativa. Não recomendamos o uso do sinal de tais pixels para calcular a profundidade. A figura a seguir mostra o exemplo de invalidação por máscara de iluminação. Os pixels invalidados são os pixels de cor preta fora do círculo nos modos de FoV largos (à esquerda) e o hexágono nos modos FoV estreitos (à direita).

![Invalidação fora da máscara de iluminação](./media/concepts/depth-camera-invalidation-illumination-mask.png)

### <a name="signal-strength"></a>Intensidade do sinal

Os pixels são invalidados quando contêm um sinal IR saturado. Quando os pixels estão saturados, as informações da fase são perdidas. A imagem abaixo mostra o exemplo de invalidação por um sinal IR saturado. Confira setas apontadas para os pixels de exemplo nas imagens de profundidade e de IR.

![Saturação de invalidação](./media/concepts/depth-camera-invalidation-saturation.png)

A invalidação também pode ocorrer quando o sinal IR não é forte o suficiente para gerar profundidade. A figura abaixo mostra o exemplo de invalidação por um sinal de IR baixo. Consulte as setas apontadas para os pixels de exemplo nas imagens de profundidade e de IR.

![Sinal baixo de invalidação](./media/concepts/depth-camera-invalidation-low-signal.png)

### <a name="ambiguous-depth"></a>Profundidade ambígua

Os pixels também poderão ser invalidados se receberem sinais de mais de um objeto na cena. Um caso comum em que esse tipo de invalidação pode ser visto é nos cantos.  Devido à geometria da cena, a luz de IR da câmera se reflete em uma parede e na outra. Essa luz refletida causa ambigüidade na profundidade medida do pixel. Os filtros no algoritmo de profundidade detectam esses sinais ambíguos e invalidam os pixels.

As figuras a seguir mostram exemplos de invalidação por detecção de vários caminhos. Você também pode ver como a mesma área de superfície que foi invalidada de uma exibição de câmera (linha superior) pode aparecer novamente de uma exibição de câmera diferente (linha inferior). Essa imagem demonstra que as superfícies invalidadas de uma perspectiva podem estar visíveis de outra.

![Canto de vários caminhos de invalidação](./media/concepts/depth-camera-invalidation-multipath.png)

Outro caso comum de vários caminhos são os pixels que contêm o sinal misto do primeiro plano e do plano de fundo (por exemplo, bordas de objeto). Durante o movimento rápido, você poderá ver mais pixels invalidados em volta das bordas. Os pixels invalidados adicionais são devido ao intervalo de exposição da captura de profundidade bruta,

![Vários caminhos de invalidação – bordas](./media/concepts/depth-camera-invalidation-edge.png)

## <a name="next-steps"></a>Próximas etapas

[Sistemas de coordenadas](coordinate-systems.md)
