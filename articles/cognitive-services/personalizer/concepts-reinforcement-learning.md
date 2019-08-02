---
title: Aprendizado de reforço – Personalizador
titleSuffix: Azure Cognitive Services
description: O Personalizador usa informações sobre ações e o contexto atual para oferecer melhores sugestões de classificação. As informações sobre essas ações e o contexto são atributos ou propriedades que são conhecidos como recursos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 36071cdee25cfa99fc54b0e5c0c0aa822cb5fe2f
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662830"
---
# <a name="what-is-reinforcement-learning"></a>O que é o aprendizado de reforço?

O aprendizado de reforço é uma abordagem do aprendizado de máquina que aprende comportamentos obtendo comentários de seu uso.
 
O aprendizado de reforço funciona por meio do seguinte:

* Fornecendo uma oportunidade ou um grau de liberdade para representar um comportamento – como tomar decisões ou fazer escolhas.
* Fornecendo informações contextuais sobre o ambiente e as opções.
* Fornecendo comentários sobre se o comportamento atinge determinada meta de maneira satisfatória.

Embora haja muitos subtipos e estilos de aprendizado de reforço, é assim que o conceito funciona no Personalizador:

* Seu aplicativo fornece a oportunidade de mostrar uma parte do conteúdo de uma lista de alternativas.
* Seu aplicativo fornece informações sobre cada alternativa e o contexto do usuário.
* Seu aplicativo computa uma _pontuação de recompensa_.

Ao contrário de algumas abordagens do aprendizado de reforço, o Personalizador não exige uma simulação para funcionar. Seus algoritmos de aprendizado foram projetados para responder a um mundo exterior (em vez de controlá-lo) e aprender com cada ponto de dados com um entendimento de que ele é uma oportunidade única que custa tempo e dinheiro para ser criada e que há um arrependimento diferente de zero (perda de possível recompensa) caso ocorra um desempenho abaixo do ideal.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Quais tipos de algoritmos de aprendizado de reforço o Personalizador usa?

A versão atual do Personalizador usa **bandidos contextuais**, uma abordagem do aprendizado de reforço que é estruturada em torno da tomada de decisões ou da realização de escolhas entre ações discretas, em determinado contexto.

A _memória de decisões_, o modelo que foi treinado para capturar a melhor decisão possível, em um dado contexto, usa um conjunto de modelos lineares. Eles mostraram resultados de negócios repetidamente e são uma abordagem comprovada, parcialmente, pois podem aprender com o mundo real muito rapidamente sem a necessidade de treinamento em vários passos e, parcialmente, porque podem complementar modelos de aprendizado supervisionado e modelos de rede neural profunda.

A alocação do tráfego de investigação/exploração é feita aleatoriamente seguindo o percentual definido para a exploração, e o algoritmo padrão para a exploração é o epsílon Greedy.

### <a name="history-of-contextual-bandits"></a>Histórico de bandidos contextuais

John Langford cunhou o nome Bandidos Contextuais (Langford e Zhang [2007]) para descrever um subconjunto manejável de aprendizado de reforço e trabalhou em meia dúzia de artigos que melhoram nossa compreensão de como aprender nesse paradigma:

* Beygelzimer et al. [2011]
* Dudík et al. [2011a,b]
* Agarwal et al. [2014, 2012]
* Beygelzimer e Langford [2009]
* Li et al. [2010]

John também forneceu vários tutoriais anteriormente sobre tópicos como Previsão Conjunta (ICML 2015), Teoria de Bandidos Contextuais (NIPS 2013), Aprendizado Ativo (ICML 2009) e Limites da Complexidade de Amostras (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Quais estruturas de aprendizado de máquina o Personalizador usa?

Atualmente, o Personalizador usa o [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) como a base para o aprendizado de máquina. Essa estrutura permite a taxa de transferência máxima e a menor latência ao fazer classificações de personalização e treinar o modelo com todos os eventos.

## <a name="references"></a>Referências

* [Como tomar decisões contextuais com uma dívida técnica baixa](https://arxiv.org/abs/1606.03966)
* [Uma abordagem de reduções para uma classificação justa](https://arxiv.org/abs/1803.02453)
* [Bandidos contextuais eficientes em mundos dinâmicos](https://arxiv.org/abs/1708.01799)
* [Previsão de perda residual: Reforço: aprendizado sem comentários incrementais](https://openreview.net/pdf?id=HJNMYceCW)
* [Como mapear instruções e observações visuais para ações com o aprendizado de reforço](https://arxiv.org/abs/1704.08795)
* [Como aprender a pesquisar melhor do que o seu professor](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Próximas etapas

[Avaliação offline](concepts-offline-evaluation.md) 