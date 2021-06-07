---
title: Dividir Diretório de Imagens
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo dividir imagem do diretório no designer para dividir as imagens de um diretório de imagem em dois conjuntos distintos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 3ee4dd9b2e344ecb3e1a6424ce7310270e7cd076
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421185"
---
# <a name="split-image-directory"></a>Dividir Diretório de Imagens

Este tópico descreve como usar o módulo dividir imagem de imagens no designer de Azure Machine Learning, para dividir as imagens de um diretório de imagem em dois conjuntos distintos.

Esse módulo é particularmente útil quando você precisa separar dados de imagem em conjuntos de treinamento e teste. 

## <a name="how-to-configure-split-image-directory"></a>Como configurar o diretório de imagem dividida

1. Adicione o módulo **dividir imagem do diretório** ao seu pipeline. Você pode encontrar esse módulo na categoria ' Pesquisa Visual Computacional/transformação de dados de imagem '.

2. Conecte-o ao módulo do qual a saída é o diretório de imagens.

3. **Fração de entrada de imagens na primeira saída** para especificar a porcentagem de dados a serem colocados na divisão esquerda, por padrão, 0,9. Se o resultado da fração não for inteiro, o módulo usará o menor inteiro próximo.


## <a name="technical-notes"></a>Observações técnicas

### <a name="expected-inputs"></a>Entradas esperadas

| Nome                  | Type           | Descrição              |
| --------------------- | -------------- | ------------------------ |
| Diretório da imagem de entrada | ImageDirectory | Diretório de imagens a ser dividido |

### <a name="module-parameters"></a>Parâmetros do módulo

| Nome                                   | Type  | Intervalo | Opcional | Descrição                            | Padrão |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Fração de imagens na primeira saída | Float | 0-1   | Obrigatório | Fração de imagens na primeira saída | 0,9     |

### <a name="outputs"></a>Saídas

| Nome                    | Type           | Descrição                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Directory1 da imagem de saída | ImageDirectory | Diretório de imagens que contém imagens selecionadas |
| Directory2 da imagem de saída | ImageDirectory | Diretório de imagem que contém todas as outras imagens |

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 

