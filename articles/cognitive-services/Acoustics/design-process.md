---
title: Conceitos de design com simulação de acústica
titlesuffix: Azure Cognitive Services
description: Esta visão geral conceitual explica como o projeto acústica incorpora acústica simulação para o processo de design de som.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 4a1a0b15da091a1c020eb132f6b14b9ee14d334c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335376"
---
# <a name="project-acoustics-design-process-concepts"></a>Conceitos de processo de Design do projeto acústica

Esta visão geral conceitual explica como o projeto acústica incorpora simulação acústica física ao processo de design de som.

## <a name="sound-design-with-audio-dsp-parameters"></a>Design de som com parâmetros DSP de áudio

Títulos interativos 3D atingir seu determinado som usando o sinal de áudio digital blocos (DSP) hospedados em um mecanismo de áudio de processamento. Intervalo esses blocos em complexidade, de uma combinação de simples, reverberation, eco, atraso, equalização, compactação e limitação e outros efeitos. Selecionando, organizando e configurando parâmetros desses efeitos são responsabilidade do designer de som, que cria um gráfico de áudio que atinge as metas estética e jogo da experiência.

Em um título interativo, à medida que os sons e o ouvinte movem em todo o espaço 3D, como esses parâmetros se adaptar às mudanças nas condições? O designer de som geralmente organizará volumes por todo o espaço que são programados para disparar alterações de parâmetro para obter as alterações em efeitos de reverberation, por exemplo, ou para pato sons na combinação de como o ouvinte move de uma parte da cena para outra. Sistemas de acústica também estão disponíveis que podem automatizar alguns desses efeitos.

Títulos 3D usam sistemas de física de iluminação e cinemática que estão motivados a física, mas ajustado de designer para alcançar uma mistura de objetivos de imersão e jogos. Um designer visual não define os valores de pixel individuais, mas em vez disso, ajusta modelos 3D, materiais e sistemas de luz de transporte que são todas fisicamente baseadas para compensar a estética visual e os custos de CPU. Qual seria o processo equivalente para áudio? Projeto acústica é uma primeira etapa a exploração dessa pergunta. Primeiro, nós vamos tocar no que significa acústico energia por meio de um espaço de transporte.

![Cena de captura de tela de AltSpace sobreposta com zonas de reverberação](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Respostas de impulso: Acoustically conecta dois pontos no espaço

Se você estiver familiarizado com o design de áudio, você pode estar familiarizado com as respostas de impulso acústico. Uma resposta de impulso acústico modela o transporte de um som de uma fonte para um ouvinte. Portanto, uma resposta de impulso pode capturar cada efeito interessante de acústica sala como oclusão e reverberation. Respostas de impulso também tem certas propriedades avançadas que permitem que os efeitos de DSP de áudio dimensionar. Somar dois sinais de áudio e de processamento com uma resposta de impulso fornecem o mesmo resultado que aplicar a resposta ao impulso separadamente para cada sinal e adicionar os resultados. Propagação acústica e respostas de impulso também não dependem do áudio que está sendo processada, somente em cena sendo modelada e os locais de origem e de escuta. Em resumo, uma resposta de impulso destila o efeito da cena na propagação de som.

Uma resposta de impulso captura cada efeito de acústico sala interessante e podemos pode aplicá-lo ao áudio com eficiência com um filtro, e podemos obter respostas de impulso de medida ou simulação. Mas e se nós não deseja bastante a acústica para corresponder exatamente a física, mas em vez disso, moldar-o para corresponder as demandas emocionais de uma cena? Mas muito parecido com valores de pixel, uma resposta ao impulso de duração é apenas uma lista de milhares de números, como estamos possivelmente ajustar para atender às necessidades estéticas? E, e se quisermos ter oclusão/obstrução que varia de acordo sem problemas durante a passagem por meio de entradas de acesso ou por trás de obstáculos, quantas respostas de impulso precisamos obter um efeito suave? E se a fonte se move rapidamente? Como podemos interpolar?

Isso parece difícil de usar a simulação e respostas de impulso para alguns aspectos do acústica em títulos interativos. Mas, ainda é possível criar um sistema de transporte de áudio que dá suporte a designer ajustes se podemos conectar nossas respostas de impulso de simulação com nossos parâmetros de efeito DSP áudio familiares.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Conectar-se a simulação de DSP de áudio com parâmetros

Uma resposta de impulso contém cada interessantes (e cada como não interessantes) acústico efeito. Blocos de DSP de áudio, quando seus parâmetros estão definidos corretamente, poderá renderizar efeito acústico interessante. Usando a simulação acústico para orientar um bloco DSP de áudio para automatizar o transporte de áudio em uma cena 3D é apenas uma questão de medir os parâmetros DSP de áudio de uma resposta ao impulso de duração. Essa medida é bem compreendida por determinados efeitos acústico importantes e comuns incluindo oclusão, obstrução, portalling e reverberation.

Mas, se a simulação é conectada diretamente para os parâmetros DSP de áudio, onde é o designer ajuste? O que obtermos? Bem, obtemos uma quantidade significativa de memória por descartando as respostas de impulso e retendo alguns parâmetros DSP. E, para que o designer de alguns energia sobre o resultado final, estamos só precisam encontrar uma maneira de inserir o designer entre a simulação e o DSP de áudio.

![Gráfico com resposta de impulso estilizado com parâmetros sobrepostos](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Design de som, transformando os parâmetros DSP de áudio da simulação

Considere o efeito que seus óculos tem em sua visão de mundo. Em um dia brilhante, os óculos podem reduzir o brilho para algo mais confortável. Em uma sala escura, talvez você não conseguir ver nada em todos os. Os óculos não definir um certo nível de brilho em todas as situações; elas simplesmente fazem tudo o que mais escura.

Se usarmos a simulação para impulsionar nossa DSP de áudio usando parâmetros de oclusão e reverberation, podemos adicionar um filtro após o simulador para ajustar os parâmetros que o DSP 'enxerga'. O filtro não impor um certo nível de oclusão ou reverberação comprimento da parte final, bem como óculos de SOL não faça cada sala o brilho do mesmo. O filtro pode apenas fazer cada occluder occlude menor. Ou occlude muito mais. Adicionando e ajustando um filtro de parâmetro 'escurecimento' oclusão, salas de grandes, abra ainda não teria pouco ou nenhum efeito oclusão, enquanto as entradas de acesso aumentaria de uma mídia como um efeito de oclusão forte, enquanto mantém a suavidade em vigor transições que fornece a simulação.

Esse paradigma, tarefas do designer muda de escolhendo acústicos parâmetros para cada situação, filtros para selecionar e ajustar para aplicar os parâmetros mais importantes de DSP provenientes de simulação. Ele eleva atividades do designer de preocupações estreitas de configuração de transições suaves para as questões mais alta da intensidade dos efeitos de oclusão e reverberation e a presença de fontes na combinação de. É claro que, quando a situação de demanda, um filtro sempre disponível é simplesmente voltar para escolher os parâmetros DSP de uma fonte específica em uma situação específica.

## <a name="sound-design-in-project-acoustics"></a>Design de som no projeto acústica

O pacote do projeto acústica integra-se cada um dos componentes descritos acima: um simulador, um codificador que extrai os parâmetros e cria o ativo acústica, DSP de áudio e uma seleção de filtros. Design de som com projeto acústica envolve escolher parâmetros para os filtros que ajustar os parâmetros de oclusão e reverberation derivado de simulação e aplicado a DSP de áudio, com controles dinâmicos expostos dentro do editor de jogo e o mecanismo de áudio.

## <a name="next-steps"></a>Próximas etapas
* Experimente o paradigma de design usando o [quickstart acústica do projeto para Unity](unity-quickstart.md) ou o [acústica projeto quickstart para Unreal](unreal-quickstart.md)
* Explore a [controles de design acústica do projeto para Unity](unity-workflow.md) ou o [projeto acústica criar controles para Unreal](unreal-workflow.md)

