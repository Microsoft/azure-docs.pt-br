---
title: O que é treinamento e modelo? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: 'Um modelo é o sistema, que fornece tradução para um par de idiomas específico. O resultado de um treinamento bem sucedido é um modelo. Ao treinar um modelo, são necessários três conjuntos de dados mutuamente exclusivos: conjunto de dados de treinamento, conjunto de dados de ajuste e conjunto de dados de teste.'
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: lajanuar
ms.openlocfilehash: 62a444b66d50579c601077751b0700aa954b34c0
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897927"
---
# <a name="what-are-trainings-and-models"></a>Quais são os modelos e treinamentos?

Um modelo é o sistema, que fornece tradução para um par de idiomas específico.
O resultado de um treinamento bem sucedido é um modelo. Ao treinar um modelo, três tipos de documento mutuamente exclusivos são necessários: treinamento, ajuste e teste. O tipo de documento de dicionário também pode ser fornecido. Consulte alinhamento de [frase](./sentence-alignment.md#suggested-minimum-number-of-sentences).

Se apenas os dados de treinamento forem fornecidos ao enfileirar um treinamento, o tradutor personalizado montará automaticamente os dados de ajuste e teste. Ele usará um subconjunto aleatório de frases de seus documentos de treinamento e excluirá essas frases dos próprios dados de treinamento.

## <a name="training-document-type-for-custom-translator"></a>Tipo de documento de treinamento para o tradutor personalizado

Os documentos incluídos no conjunto de treinamento são usados pelo Custom Translator como base para a construção do seu modelo. Durante a execução do treinamento, as frases presentes nesses documentos são alinhadas (ou emparelhadas). Você pode tomar liberdades ao compor seu conjunto de documentos de treinamento. Você pode incluir documentos que você acredita serem de relevância tangencial em um modelo. Novamente, exclua-os em outro para ver o impacto na pontuação [BLEU (Undergraduate Bilingual Evaluation Understudy)](what-is-bleu-score.md). Contanto que você mantenha o conjunto de ajustes e o conjunto de testes constantes, fique à vontade para experimentar a composição do conjunto de treinamento. Essa abordagem é uma maneira eficaz de modificar a qualidade do seu sistema de tradução.

Você pode executar vários treinamentos em um projeto e comparar os [escores BLEU](what-is-bleu-score.md) em todas as execuções de treinamento. Quando você estiver executando vários treinamentos para comparação, certifique-se de que os mesmos dados de ajuste/ teste sejam especificados a cada vez. Além disso, certifique-se de também inspecionar os resultados manualmente na guia ["Teste"](how-to-view-system-test-results.md).

## <a name="tuning-document-type-for-custom-translator"></a>Ajustando o tipo de documento para o tradutor personalizado

Documentos paralelos incluídos neste conjunto são usados pelo Custom Translator para ajustar o sistema de tradução para obter os melhores resultados.

Os dados de ajuste são usados durante o treinamento para ajustar todos os parâmetros e pesos do sistema de tradução para os valores ideais. Escolha seus dados de ajuste com cuidado: os dados de ajuste devem ser representativos do conteúdo dos documentos que você pretende traduzir no futuro. Os dados de ajuste têm uma influência importante sobre a qualidade das traduções produzidas. O ajuste permite que o sistema de tradução forneça traduções mais próximas dos exemplos fornecidos nos dados de ajuste. Você não precisa de mais de 2500 frases em seus dados de ajuste. Para otimizar a qualidade da tradução, recomenda-se selecionar o conjunto de sintonia manualmente escolhendo a seleção mais representativa de frases.

Ao criar seu conjunto de ajustes, escolha frases que sejam um comprimento significativo e representativo das futuras frases que você espera traduzir. Você também deve escolher frases que tenham palavras e frases que você pretende traduzir na distribuição aproximada que você espera em suas futuras traduções. Na prática, um comprimento de frase de 7 a 10 palavras produzirá os melhores resultados, pois essas frases contêm contexto suficiente para mostrar inflexão e fornecer um comprimento de frase que seja significativo, sem ser excessivamente complexa.

Uma boa descrição do tipo de frases para usar no conjunto de ajuste é a prosa: frases fluentes reais. Não células de tabela, não poemas, não listas de coisas, não apenas pontuação, ou números em uma frase - linguagem regular.

Se você selecionar manualmente os dados de ajuste, ele não deverá ter nenhuma das mesmas frases que os dados de treinamento e teste. Os dados de ajuste têm um impacto significativo na qualidade das traduções – escolha as sentenças com cuidado.

Se você não tiver certeza do que escolher para seus dados de ajuste, basta selecionar os dados de treinamento e permitir que o tradutor personalizado selecione os dados de ajuste para você. Quando você permite que o tradutor personalizado escolha os dados de ajuste automaticamente, ele usará um subconjunto aleatório de frases de seus documentos de treinamento bilíngües e excluirá essas frases do próprio material de treinamento.

## <a name="testing-dataset-for-custom-translator"></a>Conjunto de dados de teste para o conversor personalizado

Documentos paralelos incluídos no conjunto de testes são usados para calcular a pontuação do BLEU (Bilingual Evaluation Understudy). Esta pontuação indica a qualidade do seu sistema de tradução. Essa pontuação realmente informa o quanto as traduções feitas pelo sistema de tradução resultante desse treinamento correspondem às sentenças de referência no conjunto de dados de teste.

A pontuação da BLEU é uma medida do delta entre a tradução automática e a tradução de referência. Seu valor varia de 0 a 100. Uma pontuação de 0 indica que nem uma única palavra da referência aparece na tradução. Uma pontuação de 100 indica que a tradução automática corresponde exatamente à referência: a mesma palavra está exatamente na mesma posição. A pontuação que você recebe é a média da Pontuação de BLEU para todas as frases dos dados de teste.

Os dados de teste devem incluir documentos paralelos onde as frases de idioma de destino são as traduções mais desejáveis das sentenças de idioma de origem correspondentes no par de destino de origem. Talvez você queira usar os mesmos critérios usados para compor os dados de ajuste. No entanto, os dados de teste não têm influência sobre a qualidade do sistema de tradução. Ele é usado exclusivamente para gerar a pontuação de BLEU para você.

Você não precisa de mais de 2.500 frases como os dados de teste. Quando você permite que o sistema escolha o conjunto de testes automaticamente, ele usará um subconjunto aleatório de sentenças de seus documentos de treinamento bilíngue e excluirá essas frases do próprio material de treinamento.

Você pode visualizar as traduções personalizadas do conjunto de testes e compará-las às traduções fornecidas em seu conjunto de testes, navegando até a guia teste em um modelo.