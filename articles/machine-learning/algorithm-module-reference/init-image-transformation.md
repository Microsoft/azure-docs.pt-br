---
title: Transformação imagem Transformationply de imagem inicial
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de transformação init Image no designer de Azure Machine Learning para inicializar a transformação de imagem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fc0eb196ed24e413c35d64f0571ff29dc3725032
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421270"
---
# <a name="init-image-transformation"></a>Transformação da Imagem de Inicialização

Este artigo descreve como usar o módulo de **transformação init Image** no Azure Machine Learning designer, para inicializar a transformação de imagem para especificar como você deseja que a imagem seja transformada.

## <a name="how-to-configure-init-image-transformation"></a>Como configurar a transformação de imagem de inicialização

1.  Adicione o módulo de **transformação init Image** ao seu pipeline no designer. 

2.  Para **redimensionar**, especifique se a imagem Pil de entrada deve ser redimensionada para o tamanho especificado. Se você escolher ' verdadeiro ', poderá especificar o tamanho da imagem de saída desejado em **tamanho**, por padrão, 256. 

3.  Para **corte no centro**, especifique se deseja cortar a imagem Pil determinada no centro. Se você escolher ' verdadeiro ', poderá especificar o tamanho da imagem de saída desejado do corte no **tamanho do corte**, por padrão, 224.  

4.  Para o **pad**, especifique se deseja preencher a imagem Pil determinada em todos os lados com o valor de pad 0. Se você escolher ' true ', poderá especificar o preenchimento (quantos pixels adicionar) em cada borda no **preenchimento**.

5.  Para a **tremulação de cor**, especifique se deseja alterar aleatoriamente o brilho, o contraste e a saturação de uma imagem.

6.  Para **escala de cinza**, especifique se a imagem deve ser convertida em escala de cinza.

7.  Para **corte aleatório redimensionado**, especifique se deseja cortar a imagem Pil determinada para o tamanho aleatório e a taxa de proporção. É feito um corte de tamanho aleatório (intervalo de 0, 8 a 1,0) do tamanho original e uma taxa de proporção aleatória (intervalo de 3/4 a 4/3) da taxa de proporção original. Esse corte é finalmente redimensionado para o tamanho fornecido.
    Isso é normalmente usado no treinamento das redes de início. Se você escolher ' verdadeiro ', poderá especificar o tamanho de saída esperado de cada borda em **tamanho aleatório**, por padrão, 256.

8.  Para **corte aleatório**, especifique se deseja cortar a imagem Pil determinada em um local aleatório. Se você escolher ' verdadeiro ', poderá especificar o tamanho de saída desejado do corte no **tamanho de corte aleatório**, por padrão, 224.

9.  Para **inverter horizontal aleatória**, especifique se deseja girar horizontalmente a imagem Pil determinada aleatoriamente com a probabilidade 0,5.

10.  Para **inverter vertical aleatoriamente**, especifique se deseja inverter verticalmente a imagem Pil determinada aleatoriamente com a probabilidade 0,5.

11.  Para **rotação aleatória**, especifique se deseja girar a imagem por ângulo. Se você escolher ' true ', poderá especificar em intervalo de graus definindo graus de **rotação aleatória**, o que significa (-graus, + graus), por padrão, 0.

12.  Para **afinidade aleatória**, especifique se deseja afinidade aleatória da transformação do centro de manutenção de imagem de forma invariável. Se você escolher ' true ', poderá especificar em um intervalo de graus para selecionar em **graus de afinidade aleatória**, o que significa (-graus, + graus), por padrão, 0.

13.  Para **escala de cinza aleatória**, especifique se deseja converter aleatoriamente a imagem em escala de cinza com a probabilidade 0,1.

14.  Para uma **perspectiva aleatória**, especifique se a transformação de perspectiva da imagem Pil determinada será executada aleatoriamente com a probabilidade 0,5.


16.  Conecte-se ao módulo [aplicar transformação de imagem](apply-image-transformation.md) para aplicar a transformação especificada acima ao conjunto de dados de imagem de entrada.

17. Enviar o pipeline.

## <a name="results"></a>Resultados

Após a conclusão da transformação, você pode encontrar imagens transformadas na saída do módulo [aplicar transformação de imagem](apply-image-transformation.md) .


## <a name="technical-notes"></a>Observações técnicas  

Consulte [https://pytorch.org/docs/stable/torchvision/transforms.html](https://pytorch.org/docs/stable/torchvision/transforms.html) para obter mais informações sobre transformação de imagem.

###  <a name="module-parameters"></a>Parâmetros do módulo  

| Nome                    | Intervalo   | Type    | Padrão | Descrição                              |
| ----------------------- | ------- | ------- | ------- | ---------------------------------------- |
| Redimensionar                  | Qualquer     | Booliano | verdadeiro    | Redimensionar a imagem PIL de entrada para o tamanho determinado |
| Tamanho                    | >= 1     | Inteiro | 256     | Especificar o tamanho de saída desejado          |
| Cortar central             | Qualquer     | Booliano | verdadeiro    | Corta a imagem PIL determinada no centro  |
| Tamanho do corte               | >= 1     | Inteiro | 224     | Especificar o tamanho de saída desejado do corte |
| Pad                     | Qualquer     | Booliano | Falso   | Preencha a imagem PIL determinada em todos os lados com o valor de "pad" fornecido |
| Preenchimento                 | >= 0     | Inteiro | 0       | Preenchimento em cada borda                   |
| Tremulação de cor            | Qualquer     | Booliano | Falso   | Alterar aleatoriamente o brilho, o contraste e a saturação de uma imagem |
| Escala de cinza               | Qualquer     | Booliano | Falso   | Converter imagem em escala de cinza               |
| Corte redimensionado aleatório     | Qualquer     | Booliano | Falso   | Cortar a imagem PIL específica para o tamanho aleatório e a taxa de proporção |
| Tamanho aleatório             | >= 1     | Inteiro | 256     | Tamanho de saída esperado de cada borda        |
| Corte aleatório             | Qualquer     | Booliano | Falso   | Cortar a imagem PIL determinada em um local aleatório |
| Tamanho de corte aleatório        | >= 1     | Inteiro | 224     | Tamanho de saída desejado do corte          |
| Inverter horizontal aleatório  | Qualquer     | Booliano | verdadeiro    | Inverta horizontalmente a imagem PIL determinada aleatoriamente com uma determinada probabilidade |
| Inverter vertical aleatório    | Qualquer     | Booliano | Falso   | Virar verticalmente a imagem PIL determinada aleatoriamente com uma determinada probabilidade |
| Rotação aleatória         | Qualquer     | Booliano | Falso   | Girar a imagem por ângulo                |
| Graus de rotação aleatória | [0180] | Inteiro | 0       | Intervalo de graus do qual selecionar          |
| Afinidade aleatória           | Qualquer     | Booliano | Falso   | Transformação afim aleatória da invariável do centro de manutenção de imagem |
| Graus de afinidade aleatória   | [0180] | Inteiro | 0       | Intervalo de graus do qual selecionar          |
| Escala de cinza aleatória        | Qualquer     | Booliano | Falso   | Converter aleatoriamente a imagem em escala de cinza com a probabilidade 0,1 |
| Perspectiva aleatória      | Qualquer     | Booliano | Falso   | Executa a transformação em perspectiva da imagem PIL determinada aleatoriamente com a probabilidade 0,5 |
| Apagamento aleatório          | Qualquer     | Booliano | Falso   | Seleciona aleatoriamente uma região de retângulo em uma imagem e apaga seus pixels com a probabilidade 0,5 |

###  <a name="output"></a>Saída  

| Nome                        | Type                    | Descrição                              |
| --------------------------- | ----------------------- | ---------------------------------------- |
| Transformação de imagem de saída | TransformationDirectory | Transformação imagem de saída que pode ser conectada ao módulo **aplicar transformação de imagem** . |

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 