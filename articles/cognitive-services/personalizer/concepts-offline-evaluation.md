---
title: Avaliação offline – Personalizador
titleSuffix: Azure Cognitive Services
description: Crie um loop de comentários neste Início Rápido do C# com o serviço Personalizador.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 5e9e745d73623e03e2530e1712a50e6670ee7ed3
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662858"
---
# <a name="offline-evaluation"></a>Avaliação offline

A avaliação offline é um método que permite testar e avaliar a eficácia do serviço Personalizador sem alterar o código nem afetar a experiência do usuário. A avaliação offline usa os dados passados, enviados de seu aplicativo para a API de Classificação, para comparar o desempenho de diferentes classificações.

A avaliação offline é executada em um intervalo de datas. O intervalo pode terminar na hora atual. O início do intervalo não pode ser maior que o número de dias especificado para a [retenção de dados](how-to-settings.md).

A avaliação offline pode ajudar você a responder às seguintes perguntas:

* Qual é a eficácia das classificações do Personalizador para uma personalização bem-sucedida?
    * Quais são as recompensas médias obtidas pela política de aprendizado de máquina online do Personalizador?
    * Quais as semelhanças entre o Personalizador e a eficácia do que o aplicativo teria feito por padrão?
    * Qual seria a eficácia comparativa de uma escolha aleatória para personalização?
    * Qual seria a eficácia comparativa de diferentes políticas de aprendizado especificadas manualmente?
* Quais recursos do contexto estão contribuindo mais ou menos para uma personalização bem-sucedida?
* Quais recursos das ações estão contribuindo mais ou menos para uma personalização bem-sucedida?

Além disso, a avaliação offline pode ser usada para descobrir políticas de aprendizado mais otimizadas que o Personalizador pode usar para melhorar os resultados no futuro.

As avaliações offline não fornecem diretrizes com relação ao percentual de eventos a ser usado para exploração.

## <a name="prerequisites-for-offline-evaluation"></a>Pré-requisitos para avaliação offline

Os seguintes aspectos são considerações importantes para uma avaliação offline representativa:

* Ter dados suficientes. O mínimo recomendado é, pelo menos, 50.000 eventos.
* Colete dados de períodos com um comportamento do usuário e um tráfego representativos.

## <a name="discovering-the-optimized-learning-policy"></a>Como descobrir a política de aprendizado otimizada

O Personalizador pode usar o processo de avaliação offline para descobrir uma política de aprendizado mais ideal automaticamente.

Depois de executar a avaliação offline, você poderá ver a eficácia comparativa do Personalizador com essa nova política em comparação com a atual política online. Em seguida, você pode aplicar essa política de aprendizado para torná-la eficaz imediatamente no Personalizador, baixando-a e carregando-a no painel modelos e políticas. Você também pode baixá-lo para análise ou uso futuro.

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Como entender a relevância dos resultados da avaliação offline

Quando você executa uma avaliação offline, é muito importante analisar os _limites de confiança_ dos resultados. Se eles são amplos, isso significa que o aplicativo não recebeu dados suficientes para as estimativas de recompensa serem precisas ou significativas. Conforme o sistema acumula mais dados e você executa avaliações offline por períodos mais longos, os intervalos de confiança ficam mais estreitos.

## <a name="how-offline-evaluations-are-done"></a>Como são feitas as avaliações offline

As avaliações offline são feitas usando um método chamado **Avaliação Contrafatual**. 

O Personalizador se baseia na suposição de que o comportamento dos usuários (e, portanto, as recompensas) é impossível de prever retrospectivamente (o Personalizador não pode saber o que teria acontecido se o usuário tivesse visto algo diferente do que viu) e apenas deve aprender com as recompensas medidas. 

Este é o processo conceitual usado para avaliações:

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call
    
        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.
        
    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

A avaliação offline usa apenas o comportamento do usuário observado. Esse processo descarta grandes volumes de dados, especialmente se o aplicativo faz chamadas de Classificação com um grande número de ações.


## <a name="evaluation-of-features"></a>Avaliação de recursos

As avaliações offline podem fornecer informações sobre o peso que recursos específicos contribuem para o contexto ou as ações por recompensas mais altas. As informações são computadas usando a avaliação em relação ao período e aos dados especificados e podem variar com o tempo.

Recomendamos examinar as avaliações de recurso e fazer as seguintes perguntas:

* Quais outros recursos adicionais o aplicativo ou o sistema pode fornecer semelhantes àqueles que são mais eficazes?
* Quais recursos podem ser removidos devido à baixa eficácia? Recursos de baixa eficácia adicionam _ruído_ ao aprendizado de máquina.
* Há algum recurso incluído acidentalmente? Exemplos disso são: PII (informações de identificação pessoal), IDs duplicadas etc.
* Há algum recurso indesejável que não deve ser usado para personalização devido a considerações regulamentares ou referentes a uso responsável? Há algum recurso que pode representar (ou seja, estar correlacionado ou espelhar de maneira aproximada) recursos indesejáveis?


## <a name="next-steps"></a>Próximas etapas

[Configurar o personalizado](how-to-settings.md)
[executar avaliações offline](how-to-offline-evaluation.md) entender [como o personalizador funciona](how-personalizer-works.md)
