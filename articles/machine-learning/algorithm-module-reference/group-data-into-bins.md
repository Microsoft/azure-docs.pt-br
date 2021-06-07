---
title: 'Agrupar dados em compartimentos: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo Agrupar dados em compartimentos para agrupar números ou alterar a distribuição de dados contínuos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/13/2020
ms.openlocfilehash: 392cb9b4c2ded1b98b79ce8dcd780ac59e96b78a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91998503"
---
# <a name="group-data-into-bins-module"></a>Agrupar dados em um módulo de compartimentos

Este artigo descreve como usar os dados de grupo em um módulo de compartimentos no Azure Machine Learning designer, para agrupar números ou alterar a distribuição de dados contínuos.

O módulo Agrupar dados em compartimentos dá suporte a várias opções para dados compartimentalização. Você pode personalizar como as bordas de compartimento são definidas e como os valores são particionados nos compartimentos. Por exemplo, você pode:  

+ Digite manualmente uma série de valores para servir como limites de compartimento.  
+ Atribua valores aos compartimentos usando *quantis* ou classificações de percentil.  
+ Force uma distribuição uniforme de valores nos compartimentos.  

## <a name="more-about-binning-and-grouping"></a>Mais sobre compartimentalização e agrupamento

O *compartimentalização* ou o agrupamento de dados (às vezes chamado de *quantização*) é uma ferramenta importante para preparar dados numéricos para o aprendizado de máquina. Ele é útil em cenários como estes:

+ Uma coluna de números contínuos tem muitos valores exclusivos para modelar com eficiência. Portanto, você atribui automaticamente ou manualmente os valores a grupos, para criar um conjunto menor de intervalos discretos.

+ Você deseja substituir uma coluna de números por valores categóricos que representem intervalos específicos.

    Por exemplo, você pode querer agrupar valores em uma coluna de idade especificando intervalos personalizados como 1 a 15, 16 a 22, 23 a 30 e assim por diante para os dados demográficos do usuário.

+ Um conjunto de dados tem alguns valores extremos, tudo bem fora do intervalo esperado, e esses valores têm uma influência muito grande no modelo treinado. Para reduzir a tendência no modelo, você pode transformar os dados em uma distribuição uniforme usando o método quantis.

    Com esse método, o módulo Agrupar dados em compartimentos determina os locais de compartimentos e as larguras de compartimento ideais para garantir que aproximadamente o mesmo número de amostras se enquadrem em cada compartimento. Em seguida, dependendo do método de normalização que você escolher, os valores nos compartimentos serão transformados em percentuais ou mapeados para um número de compartimento.

### <a name="examples-of-binning"></a>Exemplos de compartimentalização

O diagrama a seguir mostra a distribuição de valores numéricos antes e depois da compartimentalização com o método *quantis*. Observe que, em comparação com os dados brutos à esquerda, os dados foram compartimentalizados e transformados em uma escala normal de unidade.  

> [!div class="mx-imgBorder"]
> ![Visualização de resultado](media/module/group-data-into-bins-result-example.png)

Como há muitas maneiras de agrupar dados, tudo personalizável, recomendamos que você teste com diferentes métodos e valores. 

## <a name="how-to-configure-group-data-into-bins"></a>Como configurar Agrupar dados em compartimentos

1. Adicione os dados do grupo ao módulo de **compartimentos** ao seu pipeline no designer. Você pode encontrar esse módulo na categoria **Transformação de Dados**.

2. Conecte o conjunto de dados que tem os valores numéricos ao compartimento. A quantificação só pode ser aplicada a colunas que contêm dados numéricos. 

    Se o conjunto de dados contiver colunas não numéricas, use o módulo [Selecionar Colunas no Conjunto de Dados](select-columns-in-dataset.md) para selecionar um subconjunto de colunas com o qual trabalhar.

3. Especifique o modo de compartimentalização. O modo compartimentalização determina outros parâmetros, portanto, certifique-se de selecionar a opção de **modo compartimentalização** primeiro. Há suporte para os seguintes tipos de compartimentalização:

    - **Quantis**: O método quantile atribui valores aos compartimentos com base nas classificações percentuais. Esse método também é conhecido como compartimentalização de altura igual.

    - **Largura igual**: com essa opção, você deve especificar o número total de compartimentos. Os valores da coluna de dados são colocados em compartimentos, de modo que cada compartimento tenha o mesmo intervalo entre os valores inicial e final. Como resultado, alguns compartimentos podem ter mais valores se os dados estiverem concentrados em torno de um determinado ponto.

    - **Bordas personalizadas**: Você pode especificar os valores que iniciam cada compartimento. O valor de borda sempre é o limite inferior do compartimento. 
    
      Por exemplo, suponha que você deseja agrupar valores em dois compartimentos. Um terá valores maiores que 0 e um terá valores menores ou iguais a 0. Nesse caso, para bordas de compartimento, você insere **0** na **lista separada por vírgulas de bordas de compartimento**. A saída do módulo será 1 e 2, indicando o índice de compartimento para cada valor de linha. Observe que a lista de valores separados por vírgulas deve estar em ordem crescente, como 1, 3, 5, 7.
    
    > [!Note]
    > O modo de *MDL de entropia* é definido no estúdio (clássico) e não há nenhum pacote de código-fonte aberto correspondente que possa ser utilizado para dar suporte no designer ainda.        

4. Se você estiver usando os modos de compartimentalização **quantis** e de **largura igual** , use a opção **número de compartimentos** para especificar quantos compartimentos ou *quantis* deseja criar.

5. Para **colunas para o compartimento**, use o seletor de coluna para escolher as colunas que têm os valores que você deseja bandeja. As colunas devem ser um tipo de dados numérico.

    A mesma regra compartimentalização é aplicada a todas as colunas aplicáveis que você escolher. Se você precisar desagrupar algumas colunas usando um método diferente, use uma instância separada dos dados do grupo em um módulo de compartimentos para cada conjunto de colunas.

    > [!WARNING]
    > Se você escolher uma coluna que não seja um tipo permitido, um erro de tempo de execução será gerado. O módulo retorna um erro assim que encontra qualquer coluna de um tipo não permitido. Se você receber um erro, examine todas as colunas selecionadas. O erro não lista todas as colunas inválidas.

6. Para o **modo de saída**, indique como você deseja gerar os valores quantificados:

    + **Append**: cria uma nova coluna com os valores de compartimentalizados e acrescenta isso à tabela de entrada.

    + **Inplace**: Substitui os valores originais com os novos valores no conjunto de dados.

    + **ResultOnly**: Retorna apenas as colunas de resultados.

7. Se você selecionar o modo **quantis** compartimentalização, use a opção de **normalização de Quantil** para determinar como os valores são normalizados antes da classificação em quantis. Observe que a normalização de valores transforma os valores, mas não afeta o número final de compartimentos.

    Os seguintes tipos de normalização são compatíveis:

    + **Porcentagem**: os valores são normalizados dentro do intervalo [0100].

    + **PQuantile**: os valores são normalizados dentro do intervalo [0, 1].

    + **QuantileIndex**: os valores são normalizados dentro do intervalo [1, número de compartimentos].

8. Se você escolher a opção **bordas personalizadas** , insira uma lista separada por vírgulas de números para usar como *bordas de compartimento* na caixa de texto **lista separada por vírgulas de bordas do compartimento** . 

    Os valores marcam o ponto que divide os compartimentos. Por exemplo, se você inserir um valor de borda de compartimento, serão gerados dois compartimentos. Se você inserir dois valores de borda de compartimento, três compartimentos serão gerados.

    Os valores devem ser classificados na ordem em que os compartimentos são criados, do mais baixo para o mais alto.

10. Selecione as **colunas de marca como opção categórica** para indicar que as colunas quantificadas devem ser tratadas como variáveis categóricas.

11. Enviar o pipeline.

## <a name="results"></a>Resultados

O módulo Agrupar dados em compartimentos retorna um conjunto de dados em que cada elemento foi guardado de acordo com o modo especificado. 

Ele também retorna uma *transformação compartimentalização*. Essa função pode ser passada para o módulo [aplicar transformação](apply-transformation.md) para novos exemplos de dados do compartimento usando o mesmo modo de compartimentalização e parâmetros.  

> [!TIP]
> Se você usar o compartimentalização em seus dados de treinamento, deverá usar o mesmo método compartimentalização nos dados que você usa para teste e previsão. Você também deve usar os mesmos locais de compartimento e larguras de compartimento. 
> 
> Para garantir que os dados sempre sejam transformados usando o mesmo método compartimentalização, recomendamos que você salve as transformações de dados úteis. Em seguida, aplique-os a outros conjuntos de valores usando o módulo [aplicar transformação](apply-transformation.md) .

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
