---
title: Aprendizado profundo versus aprendizado de máquina
titleSuffix: Azure Machine Learning
description: Saiba como o aprendizado profundo está relacionado ao aprendizado de máquina e ao ia. Em Azure Machine Learning, use modelos de aprendizado profundo para detecção de fraudes, detecção de objetos e muito mais.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 01/14/2020
ms.custom: contperf-fy21q1,contperfq1
ms.openlocfilehash: 48de06d28442b4d05cd3a7ab287732c0999e434c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659691"
---
# <a name="deep-learning-vs-machine-learning-in-azure-machine-learning"></a>Aprendizado profundo versus aprendizado de máquina em Azure Machine Learning

Este artigo explica o aprendizado profundo versus o aprendizado de máquina e como eles se encaixam na categoria mais ampla de inteligência artificial. Saiba mais sobre soluções de aprendizado profundo que você pode criar em Azure Machine Learning, como detecção de fraudes, reconhecimento de voz e facial, análise de sentimentos e previsão de séries temporais.

Para obter orientação sobre como escolher algoritmos para suas soluções, consulte a folha de consulta do [algoritmo de Machine Learning](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri).

## <a name="deep-learning-machine-learning-and-ai"></a>Aprendizado profundo, aprendizado de máquina e ia

![Diagrama de relações: ia versus aprendizado de máquina versus aprendizado profundo](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Considere as seguintes definições para entender o aprendizado profundo versus Machine Learning versus ia:

- O **aprendizado profundo** é um subconjunto de aprendizado de máquina baseado em redes neurais artificiais. O _processo de aprendizado_ é _profundo_ porque a estrutura de redes neurais artificiais consiste em várias camadas de entrada, saída e ocultas. Cada camada contém unidades que transformam os dados de entrada em informações que a próxima camada pode usar para executar uma determinada tarefa preditiva. Graças a essa estrutura, um computador pode aprender por meio de seu próprio processamento de dados.

- O **Machine Learning** é um subconjunto de inteligência artificial que usa técnicas (como aprendizado profundo) que permitem que os computadores usem a experiência para melhorar as tarefas. O _processo de aprendizado_ é baseado nas seguintes etapas:

   1. Alimentar dados em um algoritmo. (Nesta etapa, você pode fornecer informações adicionais para o modelo, por exemplo, executando extração de recursos.)
   1. Usar esses dados para treinar um modelo.
   1. Testar e implantar o modelo.
   1. Consuma o modelo implantado para realizar uma tarefa de previsão automatizada. (Em outras palavras, chame e use o modelo implantado para receber as previsões retornadas pelo modelo.)

- A **inteligência artificial (IA)** é uma técnica que permite aos computadores imitar a inteligência humana. Ela inclui o machine learning. 
 
Usando as técnicas de aprendizado de máquina e de aprendizado profundo, você pode criar sistemas de computador e aplicativos que fazem tarefas que são normalmente associadas à inteligência humana. Essas tarefas incluem reconhecimento de imagem, reconhecimento de fala e tradução de idioma.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Técnicas de aprendizado profundo versus aprendizado de máquina 

Agora que você tem a visão geral do aprendizado de máquina versus aprendizado profundo, vamos comparar as duas técnicas. No Machine Learning, o algoritmo precisa ser informado sobre como fazer uma previsão precisa, consumindo mais informações (por exemplo, executando extração de recursos). No aprendizado profundo, o algoritmo pode aprender a fazer uma previsão precisa por meio de seu próprio processamento de dados, graças à estrutura de rede neural artificial.

A tabela a seguir compara as duas técnicas mais detalhadamente:

| |Todos os aprendizado de máquina |Somente aprendizado profundo|
|---|---|---|
|  **Número de pontos de dados** | Pode usar pequenas quantidades de dados para fazer previsões. | Precisa usar grandes quantidades de dados de treinamento para fazer previsões. |
|  **Dependências de hardware** | Pode funcionar em máquinas de low-end. Ele não precisa de uma grande quantidade de poder computacional. | Depende de computadores avançados. Ele faz inerentemente um grande número de operações de multiplicação de matriz. Uma GPU pode otimizar essas operações com eficiência. |
|  **Processo de personalização** | Requer que os recursos sejam identificados e criados com precisão pelos usuários. | Aprende recursos de alto nível de dados e cria novos recursos por si só. |
|  **Abordagem de aprendizagem** | Divide o processo de aprendizado em etapas menores. Em seguida, ele combina os resultados de cada etapa em uma saída. | Percorre o processo de aprendizado resolvendo o problema de ponta a ponta. |
|  **Tempo de execução** | Leva um tempo comparativamente pequeno para ser treinado, variando de alguns segundos a algumas horas. | Geralmente leva muito tempo para treinar porque um algoritmo de aprendizado profundo envolve muitas camadas. |
|  **Saída** | A saída geralmente é um valor numérico, como uma pontuação ou uma classificação. | A saída pode ter vários formatos, como um texto, uma pontuação ou um som. |

## <a name="what-is-transfer-learning"></a>O que é o aprendizado de transferência

O treinamento de modelos de aprendizado profundo geralmente requer grandes quantidades de dados de treinamento, recursos de computação de alto nível (GPU, TPU) e um tempo de treinamento mais longo. Em cenários em que não há nenhum deles disponível para você, você pode fazer um atalho para o processo de treinamento usando uma técnica conhecida como *aprendizado de transferência.*

O aprendizado de transferência é uma técnica que aplica o conhecimento obtido da solução de um problema para um problema diferente, mas relacionado.

Devido à estrutura de redes neurais, o primeiro conjunto de camadas geralmente contém recursos de nível inferior, enquanto o conjunto final de camadas contém um recurso de nível mais alto que está mais próximo do domínio em questão. Ao redirecionar as camadas finais para uso em um novo domínio ou problema, você pode reduzir significativamente a quantidade de tempo, os dados e os recursos de computação necessários para treinar o novo modelo. Por exemplo, se você já tiver um modelo que reconheça carros, poderá redirecionar esse modelo usando o aprendizado de transferência para também reconhecer caminhões, motocicletas e outros tipos de veículos.

Saiba como aplicar o aprendizado de transferência para classificação de imagem usando uma estrutura de código-fonte aberto no Azure Machine Learning: [treinar um modelo de PyTorch de aprendizado profundo usando o aprendizado de transferência](./how-to-train-pytorch.md?WT.mc_id=docs-article-lazzeri).

## <a name="deep-learning-use-cases"></a>Casos de uso de aprendizado profundo

Devido à estrutura de rede neural artificial, o profundo aprendizado de excels na identificação de padrões em dados não estruturados, como imagens, som, vídeo e texto. Por esse motivo, o aprendizado profundo está transformando rapidamente muitas indústrias, incluindo saúde, energia, finanças e transporte. Esses setores agora estão relembrando os processos de negócios tradicionais. 

Alguns dos aplicativos mais comuns para aprendizado profundo são descritos nos parágrafos a seguir. No Azure Machine Learning, você pode usar um modelo de sua compilação de uma estrutura de código-fonte aberto ou criar o modelo usando as ferramentas fornecidas.

### <a name="named-entity-recognition"></a>Reconhecimento de entidade nomeada

O reconhecimento de entidade nomeada é um método de aprendizado profundo que leva um pedaço de texto como entrada e o transforma em uma classe predeterminada. Essas novas informações podem ser um CEP, uma data, uma ID de produto. As informações podem então ser armazenadas em um esquema estruturado para criar uma lista de endereços ou servir como um parâmetro de comparação para um mecanismo de validação de identidade.

### <a name="object-detection"></a>Detecção de objetos

O aprendizado profundo foi aplicado em muitos casos de uso de detecção de objetos. A detecção de objeto é composta por duas partes: classificação de imagem e localização de imagem. A _classificação_ de imagem identifica os objetos da imagem, como carros ou pessoas. A _localização_ da imagem fornece o local específico desses objetos. 

A detecção de objetos já é usada em setores como jogos, varejo, turismo e carros de autoatendimento.

### <a name="image-caption-generation"></a>Geração de legenda de imagem

Como o reconhecimento de imagem, em legendas de imagens, para uma determinada imagem, o sistema deve gerar uma legenda que descreve o conteúdo da imagem. Quando você pode detectar e rotular objetos em fotografias, a próxima etapa é transformar esses rótulos em frases descritivas. 

Normalmente, os aplicativos de legenda de imagem usam redes neurais de revolução para identificar objetos em uma imagem e, em seguida, usam uma rede neural recorrente para transformar os rótulos em sentenças consistentes.

### <a name="machine-translation"></a>Tradução automática

A tradução automática usa palavras ou frases de uma linguagem e as converte automaticamente em outro idioma. A tradução automática já existe há muito tempo, mas o aprendizado profundo atinge resultados impressionantes em duas áreas específicas: tradução automática de texto (e conversão de fala em texto) e tradução automático de imagens.

Com a transformação de dados apropriada, uma rede neural pode entender o texto, áudio e sinais visuais. A tradução automática pode ser usada para identificar trechos de som em arquivos de áudio maiores e transcrever a palavra ou imagem falada como texto.

### <a name="text-analytics"></a>Análises de texto

A análise de texto baseada em métodos de aprendizado profundo envolve a análise de grandes quantidades de dados de texto (por exemplo, documentos médicos ou recebimentos de despesas), reconhecimento de padrões e criação de informações organizadas e concisas.

As empresas usam o aprendizado profundo para executar a análise de texto para detectar o Insider Trading e a conformidade com as normas governamentais. Outro exemplo comum é a fraude de seguro: a análise de texto geralmente foi usada para analisar grandes quantidades de documentos para reconhecer as chances de uma declaração de seguro ser fraude. 

## <a name="artificial-neural-networks"></a>Redes neurais artificial

Redes neurais artificial são formadas por camadas de nós conectados. Os modelos de aprendizado profundo usam redes neurais que têm um grande número de camadas. 

As seções a seguir exploram a typologies de rede neural artificial mais popular.

### <a name="feedforward-neural-network"></a>Rede neural Feedforward

A rede neural Feedforward é o tipo mais simples de rede neural artificial. Em uma rede Feedforward, as informações se movem em apenas uma direção da camada de entrada para a camada de saída. As redes neurais Feedforward transformam uma entrada colocando-a em uma série de camadas ocultas. Cada camada é composta de um conjunto de neurônios, e cada camada é totalmente conectada a todos os neurônios na camada antes. A última camada totalmente conectada (a camada de saída) representa as previsões geradas.

### <a name="recurrent-neural-network"></a>Rede neural recorrente

As redes neurais recorrentes são uma rede neural artificial amplamente usada. Essas redes salvam a saída de uma camada e as alimentam de volta à camada de entrada para ajudar a prever o resultado da camada. As redes neurais recorrentes têm ótimos recursos de aprendizado. Eles são amplamente usados para tarefas complexas, como Previsão de série temporal, aprendizado de manuscrito e linguagem de reconhecimento.

### <a name="convolutional-neural-network"></a>Rede neural de convolução

Uma rede neural de revolução é uma rede neural artificialmente eficaz e apresenta uma arquitetura exclusiva. As camadas são organizadas em três dimensões: largura, altura e profundidade. Os neurônios em uma camada conectam-se não a todos os neurônios na próxima camada, mas apenas a uma pequena região dos neurônios da camada. A saída final é reduzida a um único vetor de pontuações de probabilidade, organizadas ao longo da dimensão de profundidade. 

As redes neurais de revolução foram usadas em áreas como reconhecimento de vídeo, reconhecimento de imagem e sistemas de recomendação.

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir mostram mais opções para usar modelos de aprendizado aprofundado de software livre no [Azure Machine Learning](./index.yml?WT.mc_id=docs-article-lazzeri):


- [Classificar dígitos manuscritos usando um modelo TensorFlow](./how-to-train-tensorflow.md?WT.mc_id=docs-article-lazzeri) 

- [Classificar dígitos manuscritos usando um estimador TensorFlow e Keras](./how-to-train-keras.md?WT.mc_id=docs-article-lazzeri)

- [Classificar dígitos manuscritos usando um modelo de encadeamento](./how-to-set-up-training-targets.md?WT.mc_id=docs-article-lazzeri)
