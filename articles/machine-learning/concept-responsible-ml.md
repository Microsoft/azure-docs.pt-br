---
title: O que é Machine Learning responsável (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba o que é o Machine Learning responsável e como usá-lo com Azure Machine Learning para entender modelos, proteger dados e controlar o ciclo de vida do modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 02/25/2021
ms.custom: responsible-ml
ms.openlocfilehash: f849968c16d2c3d8940a71da0e0cda536882d9c8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692055"
---
# <a name="what-is-responsible-machine-learning-preview"></a>O que é o Machine Learning responsável? (versão prévia)

Neste artigo, você aprenderá o que é o aprendizado de máquina (ML) responsável e as maneiras em que você pode colocá-lo em prática com Azure Machine Learning.

## <a name="responsible-machine-learning-principles"></a>Princípios de aprendizado de máquina responsáveis

Ao longo de todo o desenvolvimento e uso de sistemas de IA, a confiança deve ser o centro de tudo. Confie na plataforma, no processo e nos modelos. Na Microsoft, o Machine Learning responsável abrange os seguintes valores e princípios:

- Entender modelos de machine learning
  - Interpretar e explicar o comportamento do modelo
  - Avaliar e mitigar a imparcialidade do modelo
- Proteger pessoas e seus dados
  - Evitar a exposição de dados com privacidade diferencial
  - Trabalhar com dados criptografados usando a criptografia homomórficas
- Controlar o processo de machine learning de ponta a ponta
  - Documentar o ciclo de vida de machine learning com folhas de dados

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Pilares de ML responsáveis-interpretação, privacidade diferencial, criptografia homomórficas, trilha de auditoria-Azure Machine Learning":::

Conforme a inteligência artificial e os sistemas autônomos integram-se cada vez mais na sociedade, é importante fazer um esforço proativo para antecipar e mitigar as consequências indesejadas dessas tecnologias.

## <a name="interpret-and-explain-model-behavior"></a>Interpretar e explicar o comportamento do modelo

Os sistemas de caixa alta ou opaca podem ser problemáticos porque dificulta para os participantes como desenvolvedores de sistemas, reguladores, usuários e tomadores de decisões de negócios entenderem por que os sistemas tomam determinadas decisões. Alguns sistemas de IA são mais explicativos do que outros e, às vezes, há uma compensação entre um sistema com maior precisão e um que é mais explicativo.

Para criar sistemas de IA interpretáveis, use [InterpretML](https://github.com/interpretml/interpret), um pacote de software livre criado pela Microsoft. O pacote InterpretML dá suporte a uma ampla variedade de técnicas de interpretação, como explicações aditivas SHapley (SHAP), importância de imitação e prioridade de recurso de permutação (PFI).  [O InterpretML pode ser usado dentro do Azure Machine Learning](how-to-machine-learning-interpretability.md) para [interpretar e explicar seus modelos de machine learning](how-to-machine-learning-interpretability-aml.md), incluindo [modelos de machine learning automatizado](how-to-machine-learning-interpretability-automl.md).

## <a name="mitigate-fairness-in-machine-learning-models"></a>Mitigar a imparcialidade em modelos de aprendizado de máquina

Conforme os sistemas de IA ficam mais envolvidos na tomada de decisões diárias da sociedade, é de extrema importância que esses sistemas funcionam bem para fornecer resultados justos para todos.

A parcialidade nos sistemas de IA pode resultar nas seguintes consequências indesejadas:

- Alguns indivíduos não recebem oportunidades, recursos ou informações.
- Tendenciosidade e estereótipos são reforçados.

Muitos aspectos da imparcialidade não podem ser capturados ou representados por métricas. Há ferramentas e práticas que podem melhorar a imparcialidade no design e no desenvolvimento de sistemas de IA.

Duas etapas principais na redução da parcialidade em sistemas de IA são a avaliação e a mitigação. Recomendamos [FairLearn](https://github.com/fairlearn/fairlearn), um pacote de software livre que pode avaliar e mitigar a possível parcialidade de sistemas de IA. Para saber mais sobre a imparcialidade e o pacote FairLearn, consulte o [artigo de Imparcialidade em ML](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>Evitar a exposição de dados com privacidade diferencial

Quando os dados são usados para análise, é importante que os dados permaneçam privados e confidenciais durante todo o uso. A privacidade diferencial é um conjunto de sistemas e práticas que ajuda a manter os dados de indivíduos seguros e privados.

Em cenários tradicionais, os dados brutos são armazenados em arquivos e bancos de dados. Quando os usuários analisam dados, eles normalmente usam os dados brutos. Essa é uma preocupação, pois pode infringir a privacidade de um indivíduo. A privacidade diferencial tenta lidar com esse problema adicionando “ruído” ou aleatoriedade aos dados, de modo que os usuários não possam identificar pontos de dados individuais.

É difícil implementar sistemas com privacidade diferencial. O [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core) é um projeto de software livre que contém diferentes componentes para a criação de sistemas privados globais de forma diferencial. Para saber mais sobre a privacidade diferencial e o projeto SmartNoise, consulte o artigo [preservar a privacidade de dados usando a privacidade diferencial e o SmartNoise](./concept-differential-privacy.md) .

## <a name="work-on-encrypted-data-with-homomorphic-encryption"></a>Trabalhar em dados criptografados com criptografia homomórficas

Em soluções de computação e armazenamento em nuvem tradicionais, a nuvem precisa ter acesso não criptografado aos dados do cliente para computar sobre ele. Esse acesso expõe os dados aos operadores de nuvem. A privacidade de dados depende das políticas de controle de acesso implementadas pela nuvem e de confiança do cliente.

A criptografia homomórfica permite que as computações sejam feitas em dados criptografados sem necessidade de acesso a uma chave secreta (descriptografia). Os resultados dos cálculos são criptografados e podem ser revelados somente pelo proprietário da chave secreta. Usando a criptografia homomórficas, os operadores de nuvem nunca terão acesso não criptografado aos dados que eles estão armazenando e computando. Os cálculos são executados diretamente em dados criptografados. A privacidade de dados depende da criptografia de ponta e o proprietário dos dados controla todas as versões de informações. Para obter mais informações sobre a criptografia homomórficas na Microsoft, consulte [Microsoft Research](https://www.microsoft.com/research/project/homomorphic-encryption/).

Para começar a usar a criptografia homomórficas no Azure Machine Learning, use as associações do Python de [inferência criptografada](https://pypi.org/project/encrypted-inference/) para [o selo da Microsoft](https://github.com/microsoft/SEAL). O selo da Microsoft é uma biblioteca de criptografia homomórficas de software livre que permite que adições e multiplicações sejam executadas em números reais ou inteiros criptografados. Para saber mais sobre o selo da Microsoft, consulte o [centro de arquitetura do Azure](/azure/architecture/solution-ideas/articles/homomorphic-encryption-seal) ou a [página do projeto do Microsoft Research](https://www.microsoft.com/research/project/microsoft-seal/).

Consulte o exemplo a seguir para saber [como implantar um serviço Web inferência criptografado no Azure Machine Learning](how-to-homomorphic-encryption-seal.md).

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>Documentar o ciclo de vida de machine learning com folhas de dados

Documentar as informações certas no processo de machine learning é fundamental para tomar decisões responsáveis em cada estágio. As folhas de dados são uma maneira de documentar os ativos de machine learning que são usados e criados como parte do ciclo de vida do machine learning.

Os modelos tendem a ser considerados como "caixas opacas" e, muitas vezes, há poucas informações sobre eles. Como os sistemas de machine learning estão se tornando mais difundidos e são usados para tomada de decisões, o uso de folhas de dados é uma etapa para o desenvolvimento de sistemas de machine learning mais responsáveis.

Algumas informações do modelo que você pode querer documentar como parte de uma folha de dados:

- Uso pretendido
- Arquitetura do modelo
- Dados de treinamento usados
- Dados de avaliação usados
- Métricas de desempenho do modelo de treinamento
- Informações de imparcialidade.

Consulte o exemplo a seguir para saber como usar o SDK do Azure Machine Learning para implementar [folhas de dados para modelos](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb).

## <a name="additional-resources"></a>Recursos adicionais

- Para obter mais informações, consulte o [Kit de ferramentas de inovação responsável](/azure/architecture/guide/responsible-innovation/) para saber mais sobre as práticas recomendadas.
- Saiba mais sobre o conjunto de diretrizes em [Sobre o ML](https://www.partnershiponai.org/about-ml/) para ver a documentação do sistema de machine learning.