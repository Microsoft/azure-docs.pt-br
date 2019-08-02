---
title: Resolução do projeto acústica tortas
titlesuffix: Azure Cognitive Services
description: Esta visão geral conceitual descreve a diferença entre resoluções grosso e bem ao mesmo tempo, trazendo acústica.
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: kylesto
ms.openlocfilehash: c4f4581beb26eb63392644b40b1e5f16dae0481d
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849948"
---
# <a name="project-acoustics-bake-resolution"></a>Resolução do projeto acústica tortas
Esta visão geral conceitual descreve a diferença entre resoluções grosso e bem ao mesmo tempo, trazendo acústica. Você escolher essa configuração durante a etapa de investigações de padaria do fluxo de trabalho.

## <a name="Coarse-vs-Fine-Resolution"></a>Resolução grossa vs fina

A única diferença entre as configurações de resolução grossa e fina é a frequência com que a simulação é executada. A fina usa uma frequência duas vezes mais alta que a grossa. Isso tem várias implicações em acústica simulação:

* O comprimento de onda para a grossa é duas vezes mais longo que a fina e, portanto, os voxels são duas vezes maiores.
* O tempo de simulação está diretamente relacionado ao tamanho do voxel, fazendo um bake grosso aproximadamente 16 vezes mais rápido que um bake fino.
* Portais (por exemplo, portas ou windows) menores que o tamanho de voxel não podem ser simulados. A configuração grosso pode causar alguns um desses portais menores, a não ser simulada; Portanto, eles não passar som por meio de em tempo de execução. Você pode ver se isso está acontecendo, visualizando os voxels.
* A menor frequência de simulação resulta em menos difração ao redor dos cantos e bordas.
* Fontes de som não podem ser localizados dentro de voxels "preenchido" (ou seja, voxels que contêm a geometria). Isso resulta em nenhum som. É mais difícil colocar as fontes de som para que eles não estiverem dentro de voxels maiores de grosso do que ao usar a configuração bem.
* Os voxels maiores se intrometem mais nos portais, como mostrado abaixo. A primeira imagem foi criada usando resolução grossa, enquanto a segunda é a mesma porta usando resolução fina. Como indicado pelas marcas vermelhas, há muito menos intrusão na entrada usando a configuração fina. A linha azul é a porta conforme definida pela geometria, enquanto a linha vermelha é o portal acústico efetivo definido pelo tamanho do voxel. Como essa intrusão ocorre em uma determinada situação depende completamente de como os voxels se alinham com a geometria do portal, que é determinada pelo tamanho e localização de seus objetos na cena.

![Captura de tela de voxels grossos preenchendo uma porta de entrada no Unreal](media/unreal-coarse-bake.png)

![Captura de tela de voxels finos preenchendo uma porta de entrada no Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Próximas etapas

Experimentar por conta própria usando as configurações de resolução aproximada e bem nossos [Unreal](unreal-baking.md) ou [Unity](unity-baking.md) plug-ins.