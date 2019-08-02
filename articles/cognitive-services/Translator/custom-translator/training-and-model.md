---
title: O que é treinamento e modelo? - Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: 'Um modelo é o sistema, que fornece tradução para um par de idiomas específico. O resultado de um treinamento bem sucedido é um modelo. Ao treinar um modelo, são necessários três conjuntos de dados mutuamente exclusivos: conjunto de dados de treinamento, conjunto de dados de ajuste e conjunto de dados de teste.'
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: af3f795dc5036b23b82562e7af4582bd90b44f47
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595423"
---
# <a name="what-are-trainings-and-models"></a>Quais são os modelos e treinamentos?

Um modelo é o sistema, que fornece tradução para um par de idiomas específico.
O resultado de um treinamento bem sucedido é um modelo. Ao treinar um modelo, são necessários três conjuntos de dados mutuamente exclusivos: conjunto de dados de treinamento, conjunto de dados de ajuste e conjunto de dados de teste. Os dados do dicionário também podem ser fornecidos.

Se apenas dados de treinamento forem fornecidos ao enfileirar um treinamento, o Custom Translator reunirá conjuntos de dados de ajuste e teste automaticamente. Ele excluirá 5.000 sentenças de seus dados de treinamento e usará 2.500 cada para montar um conjunto de testes e ajustes.

## <a name="training-dataset-for-custom-translator"></a>Conjunto de dados de treinamento para o Custom Translator

Os documentos incluídos no conjunto de treinamento são usados pelo Custom Translator como base para a construção do seu modelo. Durante a execução do treinamento, as frases presentes nesses documentos são alinhadas (ou emparelhadas). Você pode tomar liberdades ao compor seu conjunto de documentos de treinamento. Você pode incluir documentos que você acredita serem de relevância tangencial em um modelo. Novamente, exclua-os em outro para ver o impacto na pontuação [BLEU (Undergraduate Bilingual Evaluation Understudy)](what-is-bleu-score.md). Contanto que você mantenha o conjunto de ajustes e o conjunto de testes constantes, fique à vontade para experimentar a composição do conjunto de treinamento. Essa abordagem é uma maneira eficaz de modificar a qualidade do seu sistema de tradução.

Você pode executar vários treinamentos em um projeto e comparar os [escores BLEU](what-is-bleu-score.md) em todas as execuções de treinamento. Quando você estiver executando vários treinamentos para comparação, certifique-se de que os mesmos dados de ajuste/ teste sejam especificados a cada vez. Além disso, certifique-se de também inspecionar os resultados manualmente na guia ["Teste"](how-to-view-system-test-results.md).

## <a name="tuning-dataset-for-custom-translator"></a>Conjunto de dados de ajuste para o Custom Translator

Documentos paralelos incluídos neste conjunto são usados pelo Custom Translator para ajustar o sistema de tradução para obter os melhores resultados.

O conjunto de sintonização é usado durante o treinamento para ajustar todos os parâmetros e pesos do sistema de tradução para os valores ideais. Escolha seu conjunto de ajustes com cuidado: o conjunto de ajustes deve ser representativo do conteúdo dos documentos que você pretende traduzir no futuro. O conjunto de afinação tem uma grande influência na qualidade das traduções produzidas. O ajuste permite que o sistema de tradução forneça traduções mais próximas das amostras fornecidas no conjunto de dados de ajuste. Você não precisa de mais de 2500 sentenças como conjunto de ajuste. Para otimizar a qualidade da tradução, recomenda-se selecionar o conjunto de sintonia manualmente escolhendo a seleção mais representativa de frases.

Ao criar seu conjunto de ajustes, escolha frases que sejam um comprimento significativo e representativo das futuras frases que você espera traduzir. Você também deve escolher frases que tenham palavras e frases que você pretende traduzir na distribuição aproximada que você espera em suas futuras traduções. Na prática, um comprimento de sentença de 8 a 18 palavras produzirá os melhores resultados, porque essas frases contêm contexto suficiente para mostrar inflexão e fornecem um comprimento de frase que é significativo, sem ser excessivamente complexo.

Uma boa descrição do tipo de frases para usar no conjunto de ajuste é a prosa: frases fluentes reais. Não células de tabela, não poemas, não listas de coisas, não apenas pontuação, ou números em uma frase - linguagem regular.

Se você selecionar manualmente seu conjunto de dados de ajuste, ele não deverá ter as mesmas frases que seus dados de treinamento e teste. O conjunto de afinação tem um impacto significativo na qualidade das traduções - escolha cuidadosamente as frases.

Se você não tiver certeza do que escolher para o seu conjunto de ajustes, basta selecionar o conjunto de treinamento e deixar que o Custom Translator selecione seu conjunto de ajustes para você. Quando você deixar o Custom Translator escolher o conjunto de ajustes automaticamente, ele usará um subconjunto aleatório de frases de seus documentos de treinamento bilíngue e excluirá essas frases do próprio material de treinamento.

## <a name="testing-dataset-for-custom-translator"></a>Conjunto de dados de teste para o conversor personalizado

Documentos paralelos incluídos no conjunto de testes são usados para calcular a pontuação do BLEU (Bilingual Evaluation Understudy). Esta pontuação indica a qualidade do seu sistema de tradução. Essa pontuação realmente informa o quanto as traduções feitas pelo sistema de tradução resultante desse treinamento correspondem às sentenças de referência no conjunto de dados de teste.

A pontuação da BLEU é uma medida do delta entre a tradução automática e a tradução de referência. Seu valor varia de 0 a 100. Uma pontuação de 0 indica que nem uma única palavra da referência aparece na tradução. Uma pontuação de 100 indica que a tradução automática corresponde exatamente à referência: a mesma palavra está exatamente na mesma posição. A pontuação que você recebe é a média da pontuação da BLEU para todas as sentenças do conjunto de testes.

O conjunto de testes deve incluir documentos paralelos, em que as frases do idioma de destino são as traduções mais desejáveis das sentenças do idioma de origem correspondentes no par. Você pode usar os mesmos critérios usados para compor o conjunto de ajustes. No entanto, o conjunto de testes não tem influência sobre a qualidade do sistema de tradução. Ele é usado exclusivamente para gerar a pontuação da BLEU para você e para nada mais.

Você não precisa de mais de 2.500 sentenças como o conjunto de testes. Quando você permite que o sistema escolha o conjunto de testes automaticamente, ele usará um subconjunto aleatório de sentenças de seus documentos de treinamento bilíngue e excluirá essas frases do próprio material de treinamento.

Você pode visualizar as traduções personalizadas do conjunto de testes e compará-las às traduções fornecidas em seu conjunto de testes, navegando até a guia teste em um modelo.
