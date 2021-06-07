---
title: 'Pré-processar texto: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de texto de pré-processamento no designer de Azure Machine Learning para limpar e simplificar o texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/16/2020
ms.openlocfilehash: 366b30df677a5b74bc7d70e1aea60e05b4df0152
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659264"
---
# <a name="preprocess-text"></a>Pré-processar Texto

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use o módulo de **texto pré-processar** para limpar e simplificar o texto. Ele dá suporte a essas operações comuns de processamento de texto:

* Remoção de palavras irrelevantes
* Usando expressões regulares para pesquisar e substituir cadeias de caracteres de destino específicas
* Lematização, que converte várias palavras relacionadas em um único Formulário canônico
* Normalização de caso
* Remoção de determinadas classes de caracteres, como números, caracteres especiais e sequências de caracteres repetidos, como "aaaa"
* Identificação e remoção de emails e URLs

O módulo de **texto de pré-processamento** atualmente só dá suporte ao inglês.

## <a name="configure-text-preprocessing"></a>Configurar o pré-processamento de texto  

1.  Adicione o módulo de **texto de pré-processamento** ao seu pipeline no Azure Machine Learning. Você pode encontrar esse módulo em **análise de texto**.

1. Conecte um conjunto de um DataSet que tenha pelo menos uma coluna contendo texto.

1. Selecione o idioma na lista suspensa **idioma** .

1. **Coluna de texto a ser limpa**: selecione a coluna que você deseja pré-processar.

1. **Remover palavras de parada**: Selecione esta opção se desejar aplicar uma lista de palavra irrelevante predefinida à coluna de texto. 

    As listas de palavra irrelevante são dependentes de idioma e personalizáveis.

1. **Lematização**: Selecione esta opção se quiser que as palavras sejam representadas em sua forma canônica. Essa opção é útil para reduzir o número de ocorrências exclusivas de outros tokens de texto semelhantes.

    O processo lematização é dependente de linguagem altamente.

1. **Detectar sentenças**: Selecione esta opção se desejar que o módulo Insira uma marca de limite de frase ao executar a análise.

    Esse módulo usa uma série de três caracteres de pipe `|||` para representar o terminador de frase.

1. Execute operações de localização e substituição opcionais usando expressões regulares. A expressão regular será processada a princípio, antes de todas as outras opções internas.

    * **Expressão regular personalizada**: defina o texto que você está pesquisando.
    * **Cadeia de substituição personalizada**: defina um único valor de substituição.

1. **Normalizar maiúsculas e minúsculas**: Selecione esta opção se desejar converter caracteres maiúsculos ASCII em formatos minúsculos.

    Se os caracteres não forem normalizados, a mesma palavra em letras maiúsculas e minúsculas será considerada duas palavras diferentes.

1. Você também pode remover os seguintes tipos de caracteres ou sequências de caracteres do texto de saída processado:

    * **Remover números**: Selecione esta opção para remover todos os caracteres numéricos do idioma especificado. Os números de identificação são dependentes de domínio e de idioma. Se os caracteres numéricos forem parte integrante de uma palavra conhecida, o número poderá não ser removido. Saiba mais em [notas técnicas](#technical-notes).
    
    * **Remover caracteres especiais**: Use esta opção para remover quaisquer caracteres especiais não alfanuméricos.
    
    * **Remover caracteres duplicados**: Selecione esta opção para remover caracteres extras em qualquer sequência que se repita para mais de duas vezes. Por exemplo, uma sequência como "AAAAA" seria reduzida para "AA".
    
    * **Remover endereços de email**: Selecione esta opção para remover qualquer sequência do formato `<string>@<string>` .  
    * **Remover URLs**: Selecione esta opção para remover qualquer sequência que inclua os seguintes prefixos de URL: `http` ,, `https` `ftp` , `www`
    
1. **Expandir contratações de verbo**: essa opção se aplica somente a idiomas que usam contratações de verbo; Atualmente, somente em inglês. 

    Por exemplo, ao selecionar essa opção, você pode substituir a frase *"não estaria lá"* com *"não permaneceria lá"*.

1. **Normalizar barras invertidas para barras**: Selecione esta opção para mapear todas as instâncias de `\\` para `/` .

1. **Dividir tokens em caracteres especiais**: Selecione esta opção se desejar quebrar palavras em caracteres como `&` , `-` e assim por diante. Essa opção também pode reduzir os caracteres especiais quando ele se repetir mais de duas vezes. 

    Por exemplo, a cadeia de caracteres `MS---WORD` seria separada em três tokens, `MS` , `-` e `WORD` .

1. Envie o pipeline.

## <a name="technical-notes"></a>Observações técnicas

O módulo de **texto de pré-processamento** no Studio (clássico) e o designer usam modelos de linguagem diferentes. O designer usa um modelo treinado CNN de várias tarefas do [spaCy](https://spacy.io/models/en). Modelos diferentes fornecem marcação de criador e de parte de fala diferentes, o que leva a resultados diferentes.

A seguir estão alguns exemplos:

| Configuração | Resultado da saída |
| --- | --- |
|Com todas as opções selecionadas </br> Explicação: </br> Para os casos como ' 3Test ' em ' WC-3 3Test 4Test ', o designer remove a palavra inteira ' 3Test ', já que, nesse contexto, o marcador de parte da fala especifica esse token ' 3Test ' como numeral e, de acordo com a parte da fala, o módulo o Remove.| :::image type="content" source="./media/module/preprocess-text-all-options-selected.png" alt-text="Com todas as opções selecionadas" border="True"::: |
|Somente com `Removing number` selecionado </br> Explicação: </br> Para os casos como ' 3Test ', ' 4-EC ', o designer criador não dose dividir esses casos e os trata como os tokens inteiros. Portanto, ele não removerá os números dessas palavras.| :::image type="content" source="./media/module/preprocess-text-removing-numbers-selected.png" alt-text="Com apenas ' removendo número ' selecionado" border="True"::: |

Você também pode usar a expressão regular para gerar resultados personalizados:

| Configuração | Resultado da saída |
| --- | --- |
|Com todas as opções selecionadas </br> Expressão regular personalizada: `(\s+)*(-|\d+)(\s+)*` </br> Cadeia de substituição personalizada: `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-all-options-selected.png" alt-text="Com todas as opções selecionadas e expressão regular" border="True"::: |
|Somente com `Removing number` selecionado </br> Expressão regular personalizada: `(\s+)*(-|\d+)(\s+)*` </br> Cadeia de substituição personalizada: `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-removing-numbers-selected.png" alt-text="Com a remoção de números selecionados e expressão regular" border="True"::: |


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 