---
title: Pontuar o modelo Vowpal Wabbit
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo modelo de Pontuação Vowpal Wabbit para gerar pontuações para um conjunto de dados de entrada, usando um modelo Vowpal Wabbit treinado existente.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 23dfee7b78f2606c54525391e1260af69a9b0779
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898400"
---
# <a name="score-vowpal-wabbit-model"></a>Pontuar o modelo Vowpal Wabbit
Este artigo descreve como usar o módulo **modelo de Pontuação Vowpal Wabbit** no designer de Azure Machine Learning, para gerar pontuações para um conjunto de dados de entrada, usando um modelo Vowpal Wabbit treinado existente.  

Este módulo fornece a versão mais recente do Vowpal Wabbit Framework, versão 8.8.1. Use este módulo para pontuar dados usando um modelo treinado salvo no formato VW versão 8.  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>Como configurar o modelo de Pontuação Vowpal Wabbit

1.  Adicione o módulo **modelo de Pontuação Vowpal Wabbit** ao seu experimento.  
  
2.  Adicione um modelo Vowpal Wabbit treinado e conecte-o à porta de entrada à esquerda. Você pode usar um modelo treinado criado no mesmo experimento ou localizar um modelo salvo na categoria conjuntos de **valores** do painel de navegação esquerdo do designer. No entanto, o modelo deve estar disponível no designer de Azure Machine Learning.  
  
    > [!NOTE]
    > Há suporte apenas para modelos Vowpal Wabbit 8.8.1; Você não pode conectar modelos salvos que foram treinados usando outros algoritmos.
  
3.  Adicione o conjunto de dados de teste e conecte-o à porta de entrada à direita. Se Test DataSet for um diretório, que contém o arquivo de dados de teste, especifique o nome do arquivo de dados de teste com o **nome do arquivo de dados de teste**. Se Test DataSet for um único arquivo, deixe **o nome do arquivo de dados de teste** como vazio.

4. Na caixa de texto **argumentos de VW** , digite um conjunto de argumentos de linha de comando válidos para o executável Vowpal Wabbit.  

    Para obter informações sobre quais argumentos Vowpal Wabbit são suportados e não têm suporte no Azure Machine Learning, consulte a seção [notas técnicas](#technical-notes) .  

5.  **Nome do arquivo de dados de teste**: digite o nome do arquivo que contém os dados de entrada. Esse argumento só é usado quando o conjunto de teste é um diretório.

6. **Especificar tipo de arquivo**: indique qual formato usa seus dados de treinamento. Vowpal Wabbit dá suporte a esses dois formatos de arquivo de entrada:  

   - **VW** representa o formato interno usado por Vowpal Wabbit. Consulte a [página wiki do Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) para obter detalhes. 
   - **SVMLight** é um formato usado por outras ferramentas de aprendizado de máquina. 

7. Selecione a opção, **inclua uma coluna extra contendo rótulos**, se você quiser produzir rótulos junto com as pontuações.  

   Normalmente, ao manipular dados de texto, Vowpal Wabbit não exige rótulos e retornará apenas as pontuações para cada linha de dados.  

8. Selecione a opção **incluir uma coluna extra contendo pontuações brutas**, se você quiser gerar pontuações brutas junto com os resultados.  

9. Enviar o pipeline.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para visualizar os resultados, clique com o botão direito do mouse na saída do módulo [modelo de Pontuação Vowpal Wabbit](score-vowpal-wabbit-model.md) . A saída indica uma pontuação de previsão normalizada de 0 a 1. 

+ Para avaliar os resultados, o conjunto de informações de saída deve conter nomes de coluna de Pontuação específicos, que atendem aos requisitos do módulo avaliar modelo.

  + Para a tarefa de regressão, o conjunto de pontos a ser avaliado deve ter uma coluna, denominada `Regression Scored Labels` , que representa rótulos pontuados.
  + Para a tarefa de classificação binária, o conjunto de linhas a ser avaliado deve ter duas colunas, chamadas `Binary Class Scored Labels` , `Binary Class Scored Probabilities` , que representam rótulos pontuados e probabilidades, respectivamente.
  + Para a tarefa de classificação múltipla, o conjunto de pontos a ser avaliado deve ter uma coluna, denominada `Multi Class Scored Labels` , que representa rótulos pontuados.

  Observe que os resultados do módulo modelo de Pontuação Vowpal Wabbit não podem ser avaliados diretamente. Antes da avaliação, o conjunto de um deve ser modificado de acordo com os requisitos acima.

##  <a name="technical-notes"></a>Observações técnicas

Esta seção contém detalhes de implementação, dicas e respostas para perguntas frequentes.

### <a name="parameters"></a>Parâmetros

Vowpal Wabbit tem muitas opções de linha de comando para escolher e ajustar algoritmos. Uma discussão completa sobre essas opções não é possível aqui; Recomendamos que você exiba a [página wiki do Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).  

Os parâmetros a seguir não têm suporte no Azure Machine Learning Studio (clássico).  

-   As opções de entrada/saída especificadas em [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     Essas propriedades já estão configuradas automaticamente pelo módulo.  
  
-   Além disso, qualquer opção que gera várias saídas ou usa várias entradas não é permitida. Isso inclui *`--cbt`* , *`--lda`* e *`--wap`* .  
  
-   Há suporte apenas para algoritmos de aprendizado supervisionados. Isso não permite essas opções: *`–active`* , `--rank` , *`--search`* etc.  

Todos os argumentos que não sejam os descritos acima são permitidos.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 