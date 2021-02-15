---
title: 'Seleção de recursos baseada em filtro: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo seleção de recursos baseada em filtro no Azure Machine Learning para identificar os recursos em um conjunto de informações com o maior poder de previsão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f4a7f5581703ae6932f3b40e62085fed76f5e6f2
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945695"
---
# <a name="filter-based-feature-selection"></a>Seleção de recursos baseada em filtro

Este artigo descreve como usar o módulo seleção de recursos baseada em filtro no Azure Machine Learning designer. Este módulo ajuda a identificar as colunas em seu conjunto de dados de entrada que têm a maior capacidade de previsão. 

Em geral, a *seleção de recursos* refere-se ao processo de aplicação de testes estatísticos a entradas, dado uma saída especificada. O objetivo é determinar quais colunas são mais previsíveis na saída. O módulo seleção de recursos baseada em filtro fornece vários algoritmos de seleção de recursos para escolher. O módulo inclui métodos de correlação, como os valores de correlação Pearson e qui-quadrado. 

Ao usar o módulo seleção de recursos baseada em filtro, você fornece um conjunto de um DataSet e identifica a coluna que contém o rótulo ou a variável dependente. Em seguida, especifique um único método a ser usado para medir a importância do recurso.

O módulo gera um conjunto de resultados que contém as melhores colunas de recursos, conforme classificado por energia preditiva. Ele também gera os nomes dos recursos e suas pontuações da métrica selecionada.  

## <a name="what-filter-based-feature-selection-is"></a>Qual seleção de recursos baseada em filtro é  

Esse módulo para seleção de recursos é chamado de "baseado em filtro" porque você usa a métrica selecionada para localizar atributos irrelevantes. Em seguida, você filtra colunas redundantes do seu modelo. Você escolhe uma medida estatística única que atenda aos seus dados e o módulo calcula uma pontuação para cada coluna de recurso. As colunas são retornadas classificadas por suas pontuações de recursos. 

Escolhendo os recursos corretos, você melhorará muito a precisão e a eficiência da classificação. 

Normalmente, você usa apenas as colunas com as melhores pontuações para criar seu modelo de previsão. Colunas com pontuações de seleção de recursos insatisfatórios podem ser deixadas no conjunto de e ignoradas quando você cria um modelo.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Como escolher uma métrica de seleção de recursos

O módulo seleção de recursos Filter-Based fornece uma variedade de métricas para avaliar o valor das informações em cada coluna. Esta seção fornece uma descrição geral de cada métrica e como ela é aplicada. Você pode encontrar requisitos adicionais para usar cada métrica nas [notas técnicas](#technical-notes) e nas [instruções](#how-to-configure-filter-based-feature-selection) para configurar cada módulo.

-   **Correlação de Pearson**  

    A estatística de correlação de Pearson ou o coeficiente de correlação de Pearson também é conhecido em modelos estatísticos como o `r` valor. Para duas variáveis quaisquer, ele retorna um valor que indica a intensidade da correlação.

    O coeficiente de correlação de Pearson é calculado usando a covariância de duas variáveis e dividindo-se pelo produto de seus desvios padrão. As alterações de escala nas duas variáveis não afetam o coeficiente.  

-   **Qui-quadrado**  

    O teste qui-quadrado bidirecional é um método estatístico que mede o quão distante os valores esperados estão dos resultados reais. O método pressupõe que variáveis são aleatórias e desenha a partir de um exemplo adequado das variáveis independentes. A estatística qui-quadrada resultante indica o quão longe os resultados estão do resultado esperado (aleatório).  


> [!TIP]
> Se você precisar de uma opção diferente para o método de seleção de recurso personalizado, use o módulo [Executar script R](execute-r-script.md) . 

## <a name="how-to-configure-filter-based-feature-selection"></a>Como configurar Filter-Based seleção de recursos

Você escolhe uma métrica estatística padrão. O módulo computa a correlação entre um par de colunas: a coluna de rótulo e uma coluna de recurso.

1.  Adicione o módulo seleção de recursos Filter-Based ao seu pipeline. Você pode encontrá-lo na categoria **seleção de recursos** no designer.

2. Conecte um conjunto de dados de entrada que contenha pelo menos duas colunas que são recursos potenciais.  

    Para garantir que uma coluna seja analisada e uma pontuação de recurso seja gerada, use o módulo [Editar metadados](edit-metadata.md) para definir o atributo **isfeature** . 

    > [!IMPORTANT]
    > Verifique se as colunas que você está fornecendo como entrada são recursos potenciais. Por exemplo, uma coluna que contém um único valor não tem nenhum valor de informação.
    >
    > Se você souber que algumas colunas tornaram recursos inválidos, você poderá removê-las da seleção de coluna. Você também pode usar o módulo [Editar metadados](edit-metadata.md) para sinalizá-los como **categóricos**. 
3.  Para o **método de Pontuação de recurso**, escolha um dos métodos estatísticos estabelecidos a seguir para usar no cálculo de pontuações.  

    | Método              | Requisitos                             |
    | ------------------- | ---------------------------------------- |
    | Correlação de Pearson | O rótulo pode ser texto ou numérico. Os recursos devem ser numéricos. |
    Qui-quadrado| Os rótulos e os recursos podem ser texto ou numéricos. Use esse método para calcular a importância do recurso para duas colunas categóricas.|

    > [!TIP]
    > Se você alterar a métrica selecionada, todas as outras seleções serão redefinidas. Então, certifique-se de definir essa opção primeiro.
4.  Selecione a opção **operar apenas em colunas de recurso** para gerar uma pontuação somente para colunas que foram previamente marcadas como recursos. 

    Se você desmarcar essa opção, o módulo criará uma pontuação para qualquer coluna que atenda aos critérios, até o número de colunas especificado no **número de recursos desejados**.  

5.  Para **coluna de destino**, selecione **Iniciar seletor de coluna** para escolher a coluna de rótulo por nome ou por seu índice. (Os índices são baseados em um.)  
    Uma coluna de rótulo é necessária para todos os métodos que envolvem correlação estatística. O módulo retornará um erro de tempo de design se você não escolher nenhuma coluna de rótulo ou várias colunas de rótulo. 

6.  Para o **número de recursos desejados**, insira o número de colunas de recursos que você deseja retornar como resultado:  

    - O número mínimo de recursos que você pode especificar é um, mas recomendamos que você aumente esse valor.  

    - Se o número de recursos desejados especificado for maior que o número de colunas no conjunto de dados, todos os recursos serão retornados. Até mesmo recursos com zero pontuações são retornados.  

    - Se você especificar menos colunas de resultado do que as colunas de recurso, os recursos serão classificados por Pontuação decrescente. Somente os recursos principais são retornados. 

7.  Envie o pipeline.

> [!IMPORTANT]
> Se você pretende usar a **seleção de recursos baseada em filtro** na inferência, será necessário usar a [transformação selecionar colunas](./select-columns-transform.md) para armazenar o resultado selecionado e [aplicar a transformação](./apply-transformation.md) para aplicar a transformação de recurso selecionado ao conjunto de texto de pontuação.
>
> Consulte a captura de tela a seguir para criar seu pipeline, para garantir que as seleções de coluna sejam as mesmas para o processo de pontuação.
> [!div class="mx-imgBorder"]
> ![Amostra do pipeline](media/module/filter-based-feature-selection-score.png)

## <a name="results"></a>Resultados

Após a conclusão do processamento:

+ Para ver uma lista completa das colunas de recursos analisadas e suas pontuações, clique com o botão direito do mouse no módulo e selecione **Visualizar**.  

+ Para exibir o conjunto de módulos com base em seus critérios de seleção de recursos, clique com o botão direito do mouse no módulo e selecione **Visualizar**. 

Se o conjunto de um contiver menos colunas do que o esperado, verifique as configurações do módulo. Verifique também os tipos de dados das colunas fornecidas como entrada. Por exemplo, se você definir o **número de recursos desejados** como 1, o conjunto de resultados de saída conterá apenas duas colunas: a coluna de rótulo e a coluna de recurso com mais alta classificação.


##  <a name="technical-notes"></a>Observações técnicas  

### <a name="implementation-details"></a>Detalhes de implementação

Se você usar a correlação de Pearson em um recurso numérico e um rótulo categórico, a Pontuação do recurso será calculada da seguinte maneira:  

1.  Para cada nível na coluna categórica, calcule a média condicional da coluna numérica.  

2.  Correlacione a coluna de média condicional com a coluna numérica.  

### <a name="requirements"></a>Requisitos  

-   Uma pontuação de seleção de recursos não pode ser gerada para nenhuma coluna designada como um **rótulo** ou coluna de **Pontuação** .  

-   Se você tentar usar um método de pontuação com uma coluna de um tipo de dados ao qual o método não dá suporte, o módulo gerará um erro. Ou, uma pontuação zero será atribuída à coluna.  

-   Se uma coluna contiver valores lógicos (true/false), eles serão processados como `True = 1` e `False = 0` .  

-   Uma coluna não poderá ser um recurso se tiver sido designada como um **rótulo** ou uma **Pontuação**.  

### <a name="how-missing-values-are-handled"></a>Como os valores ausentes são tratados  

-   Você não pode especificar como uma coluna de destino (rótulo) qualquer coluna que tenha todos os valores ausentes.  

-   Se uma coluna contiver valores ausentes, o módulo os ignorará quando estiver computando a pontuação da coluna.  

-   Se uma coluna designada como uma coluna de recurso tiver todos os valores ausentes, o módulo atribuirá uma pontuação zero.   


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 

