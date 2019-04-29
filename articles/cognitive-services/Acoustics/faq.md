---
title: Perguntas frequentes sobre o projeto acústica
titlesuffix: Azure Cognitive Services
description: Esta página fornece respostas a perguntas frequentes sobre o Project Acoustics, incluindo instruções de download e processo de cozedura.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 3426681aad19bbe01c0f7e88ca16e79c0b490c36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335323"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Perguntas frequentes sobre o projeto acústica

## <a name="what-is-project-acoustics"></a>O que é Projeto Acústico?

O pacote do projeto acústica de plug-ins é um sistema de acústica que calcula o comportamento de som wave antes do tempo de execução, algo semelhante a iluminação estático. A nuvem faz o trabalho pesado dos cálculos de física de onda, portanto, o custo da CPU de tempo de execução é baixo.  

## <a name="where-can-i-download-the-plugin"></a>Onde posso baixar o plug-in?

Você pode baixar o [plug-in do projeto acústica Unity](https://www.microsoft.com/download/details.aspx?id=57346) ou o [plug-in do projeto acústica Unreal](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-x-platform"></a>Oferece suporte a projeto acústica <x> plataforma?

Suporte à plataforma do projeto acústica evolui com base nas necessidades do cliente. Entre em contato conosco sobre o [fóruns do projeto acústica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics) para saber mais sobre o suporte para outras plataformas.

## <a name="is-azure-used-at-runtime"></a>O Azure é usado em tempo de execução?

Não, a integração na nuvem é usada apenas durante o estágio de pré-computação que faz parte da configuração da cena.
 
## <a name="what-is-simulation-input"></a>O que é a entrada de simulação? 

A entrada da simulação é sua cena 3D, ambiente virtual ou nível do jogo. O Project Acoustics realiza simulações de ondas volumétricas em 3D que modelam a física do som de perto, incluindo a oclusão e o espalhamento suaves.
 
## <a name="what-is-the-runtime-cost"></a>O que é o custo de tempo de execução?

Acústica leva aproximadamente 0,01% da CPU por origem por quadro. Uso de RAM depende do tamanho da cena e pode variar de 10 a 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Preciso simplificar a geometria do nível? Contagem do triângulo de controle? Verifique as malhas watertight?

Não. O sistema ingerirá geometria de nível detalhada diretamente. Será voxelizado para processamento interno.
 
## <a name="whats-in-the-runtime-lookup-table"></a>O que há na tabela de consulta de tempo de execução?

Inclui o arquivo ACE é uma tabela de parâmetros acústicos entre diversas fonte e pares de local do ouvinte, bem como geometria da cena voxelized usado para a interpolação de parâmetro.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Projeto acústica pode lidar com a movimentação de fontes?

Sim, o projeto acústica consulta a tabela de pesquisa e atualiza o DSP de áudio em todas as marcas, para que ele possa manipular movendo fontes e o ouvinte.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Projeto acústica pode lidar com a geometria dinâmica? Fechando portas? Paredes surpreso?

Não. Os parâmetros acústicos são pré-computados com base no estado estático de um nível de jogo. Sugerimos deixando a geometria de porta fora acústica e, em seguida, aplicando oclusão adicional com base no estado de destrutível e objetos do jogo móveis usando estabelecida técnicas.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>O projeto acústica usa materiais acústicos?

Sim. Os materiais são escolhidos a partir dos nomes dos materiais físicos em seu nível, impulsionando a absorção.
 
## <a name="what-do-the-probes-represent"></a>O que representam os "testes"?

As sondas são uma amostra dos possíveis locais dos jogadores. Cada sonda representa uma simulação de onda separada da cena originada no local da sonda. Em tempo de execução, os parâmetros acústicos para o local do ouvinte são interpolados a partir dos locais de sonda próximos.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Por que gastar muito computação na nuvem? O que isso me compra?

O Project Acoustics fornece parâmetros acústicos precisos e confiáveis, mesmo para ambientes virtuais ultra-complexos, levando em consideração todos os aspectos arquitetônicos. Ele fornece oclusão suave e obstrução e variação de reverberação dinâmico sem o trabalho manual de volumes de desenho. Tudo isso enquanto o restante de luz na CPU durante o tempo de execução.

## <a name="what-exactly-happens-during-baking"></a>Exatamente, o que acontece durante "trazendo"?

Uma tortas consiste em simulações acústico onda de regiões de simulação cuboid centralizados na investigação cada ouvinte.

## <a name="next-steps"></a>Próximas etapas
* Experimente o [conteúdo de exemplo de projeto acústica Unity](unity-quickstart.md) ou [conteúdo de exemplo Unreal](unreal-quickstart.md)

