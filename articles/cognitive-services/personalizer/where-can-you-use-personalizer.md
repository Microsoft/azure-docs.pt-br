---
title: Avaliação de cenário – Personalizador
titleSuffix: Azure Cognitive Services
description: O Personalizador pode ser aplicado em qualquer situação em que seu aplicativo pode selecionar o item, a ação ou o produto correto a ser exibido – visando aprimorar a experiência, alcançar melhores resultados de negócios ou melhorar a produtividade.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 465fba3a466aceaf9ef7b71e4b1957bfdbcad766
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663653"
---
# <a name="where-can-you-use-personalizer"></a>Em que situações o Personalizador pode ser usado?

Use o Personalizador em qualquer situação em que seu aplicativo precisar selecionar o item, a ação ou o produto correto a ser exibido – visando aprimorar a experiência, alcançar melhores resultados de negócios ou melhorar a produtividade. 

O Personalizador usa o aprendizado de máquina para selecionar qual ação será mostrada ao usuário. A seleção pode variar drasticamente, dependendo da quantidade, da qualidade e da distribuição dos dados enviados ao serviço.

### <a name="checklist-for-applying-personalizer"></a>Lista de verificação para aplicação do Personalizador


Você pode aplicar o Personalizador em situações em que:

* Você tem uma meta de negócios ou de usabilidade para seu aplicativo.
* Você tem um lugar em seu aplicativo, em que a tomada de uma decisão contextual do que mostrar aos usuários melhorará essa meta.
* A melhor opção pode e deve ser aprendida com o comportamento coletivo dos usuários e a pontuação total das recompensas.
* O uso do aprendizado de máquina para personalização segue [diretrizes de uso responsável](ethics-responsible-use.md) e as opções escolhidas por você.
* A decisão contextual pode ser expressa como a classificação da melhor opção (ação) de um conjunto limitado de opções.
* Saber se a opção classificada funcionou bem para seu aplicativo pode ser determinado com a avaliação de alguns aspectos do comportamento do usuário e a expressão disso em uma _pontuação de recompensa_. Esse é um número entre -1 e 1.
* A pontuação de recompensa não traz muitos fatores de confusão ou externos. A duração do teste é curta o suficiente para que a pontuação de recompensa possa ser computada enquanto ela ainda é relevante.
* Você pode expressar o contexto da classificação como uma lista de, pelo menos, 5 [recursos](concepts-features.md) que achar que ajudariam a fazer a escolha certa e que não incluam informações de identificação pessoal. (PII).
* Você tem informações sobre cada opção de conteúdo, _ação_, como uma lista de, pelo menos, 5 [recursos](concepts-features.md) que achar que ajudarão o Personalizador a fazer a escolha certa.
* Seu aplicativo pode reter dados por tempo suficiente para acumular um histórico de, pelo menos, 100.000 interações.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Considerações sobre o aprendizado de máquina para aplicação do Personalizador

O Personalizador é baseado no aprendizado de reforço, uma abordagem do aprendizado de máquina que é ensinada pelos comentários que você fornece a ele. 

O Personalizador aprenderá melhor em situações em que:

* Não há eventos suficientes para permanecer acima da personalização ideal se o problema se desloca com o tempo (por exemplo, preferências de notícias ou moda). O Personalizador se adaptará a alterações contínuas no mundo real, mas os resultados não serão ideais se não houver eventos e dados suficientes para aprendizado, a fim de descobrir e estabelecer novos padrões. Você deve escolher um caso de uso que ocorre com uma frequência suficiente. Considere buscar casos de uso que ocorram, pelo menos, 500 vezes por dia.
* O contexto e as ações têm [recursos](concepts-features.md) suficientes para facilitar o aprendizado.
* Há menos de 50 ações a serem classificadas por chamada.
* Suas configurações de retenção de dados permitem ao Personalizador coletar dados suficientes para realizar avaliações offline e a otimização de política. Normalmente, isso representa, pelo menos, 50.000 pontos de dados.

## <a name="monitor-effectiveness-of-personalizer"></a>Monitorar a eficácia do Personalizador

Monitore a eficácia do Personalizador periodicamente realizando [avaliações offline](concepts-offline-evaluation.md).

## <a name="use-personalizer-with-recommendation-engines"></a>Usar o Personalizador com mecanismos de recomendação

Muitas empresas usam mecanismos de recomendação, ferramentas de marketing e campanhas, segmentação e clustering de público-alvo, filtragem colaborativa e outros meios para recomendar produtos de um extenso catálogo aos clientes.

O [Repositório Microsoft Recommenders do GitHub](https://github.com/Microsoft/Recommenders) fornece exemplos e as práticas recomendadas para criação de sistemas de recomendação, fornecidos como Jupyter notebooks. Ele fornece exemplos funcionais de preparação de dados, criação de modelos, avaliação, ajuste e operacionalização de mecanismos de recomendação para muitas abordagens comuns, incluindo xDeepFM, SAR, ALS, RBM e DKN.

O Personalizador pode funcionar com um mecanismo de recomendação quando ele está presente.

* Os mecanismos de recomendação usam grandes quantidades de itens (por exemplo, 500.000) e recomendam um subconjunto (como os 20 principais) de centenas ou milhares de opções.
* O Personalizador usa um pequeno número de ações com muitas informações sobre elas e classifica-as em tempo real para determinado contexto sofisticado, embora a maioria dos mecanismos de recomendação use apenas alguns atributos sobre usuários, produtos e suas interações.
* O Personalizador foi projetado para explorar com autonomia as preferências do usuário o tempo todo, o que produzirá melhores resultados quando o conteúdo for alterado rapidamente, como notícias, eventos ao vivo, conteúdo ao vivo da comunidade, conteúdo com atualizações diárias ou conteúdo sazonal.

Um uso comum é selecionar a saída de um mecanismo de recomendação (por exemplo, os 20 principais produtos para determinado cliente) e usar isso como as ações de entrada para o Personalizador.

## <a name="next-steps"></a>Próximas etapas

[Ética e uso responsável](ethics-responsible-use.md).