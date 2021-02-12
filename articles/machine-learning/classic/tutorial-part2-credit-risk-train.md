---
title: 'Tutorial 2 do ML Studio (clássico): Treinar modelos de risco de crédito – Azure'
description: Um tutorial detalhado que mostra como criar uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning Studio (clássico). Este tutorial é a segunda parte de uma série de tutoriais de três partes. Ele mostra como treinar e avaliar modelos.
keywords: risco de crédito, solução de análise preditiva, avaliação de riscos
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 557c624b6ed683f701b6a8d38854cd8604e05b31
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325257"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>Tutorial 2: treinar modelos de risco de crédito – Azure Machine Learning Studio (clássico)

**APLICA-SE A:**  ![Esta é uma marca de seleção, o que significa que este artigo se aplica ao Machine Learning Studio (clássico).](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![Isto é um X, o que significa que este artigo se aplica ao Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Neste tutorial, você analisará de maneira aprofundada o processo de desenvolvimento de uma solução de análise preditiva. Você desenvolverá um modelo simples no Machine Learning Studio (clássico).  Em seguida, você implantará o modelo como um serviço Web do Azure Machine Learning.  Esse modelo implantado pode fazer previsões usando novos dados. Este tutorial é a **segunda parte de uma série de tutoriais de três partes**.

Suponha que você precisa prever o risco de crédito de uma pessoa com base nas informações dadas em um aplicativo de crédito.  

A avaliação de risco de crédito é um problema complexo, mas este tutorial simplificará um pouco esse tópico. Você usará isso como exemplo da forma de criar uma solução de análise preditiva usando o Microsoft Azure Machine Learning Studio (clássico). Você usará o Azure Machine Learning Studio (clássico) e um serviço Web do Machine Learning para esta solução.  

Neste tutorial de três partes, você começará com os dados de risco de crédito disponíveis publicamente.  Em seguida, você desenvolverá e treinará um modelo preditivo.  Por fim, você implantará o modelo como um serviço Web.

Na [primeira parte do tutorial](tutorial-part1-credit-risk.md), você criou um workspace do Machine Learning Studio (clássico), carregou dados e criou um experimento.

Nesta parte do tutorial, você vai:
 
> [!div class="checklist"]
> * Treinar vários modelos
> * Pontuar e avaliar os modelos


Na [terceira parte do tutorial](tutorial-part3-credit-risk-deploy.md), você implantará o modelo como um serviço Web.

## <a name="prerequisites"></a>Pré-requisitos

Conclusão da [primeira parte do tutorial](tutorial-part1-credit-risk.md).

## <a name="train-multiple-models"></a><a name="train"></a>Treinar vários modelos

Uma das vantagens de usar o Azure Machine Learning Studio (clássico) para criar modelos de machine learning é a capacidade de experimentar mais de um tipo de modelo de cada vez em um teste e comparar os resultados. Esse tipo de experimentação ajuda a encontrar a melhor solução para seu problema.

No experimento que estamos desenvolvendo neste tutorial, você criará dois tipos diferentes de modelos e, em seguida, comparará os resultados de suas pontuações para decidir qual algoritmo desejará usar em nosso experimento final.  

Existem diversos modelos dentre os quais você poderá escolher. Para ver os modelos disponíveis, expanda o nó **Machine Learning** na paleta do módulo e expanda **Inicializar Modelo** e os nós abaixo dele. Para os fins deste experimento, você selecionará os módulos [SVM (Computador de Vetor de Suporte) de Duas Classes][two-class-support-vector-machine] e [Árvore de Decisão Aumentada de Duas Classes][two-class-boosted-decision-tree].

> [!TIP]
> Para obter ajuda para decidir qual algoritmo de Machine Learning melhor se adapta ao problema específico que você está tentando resolver, confira [Como escolher algoritmos para o Microsoft Azure Machine Learning Studio (clássico)](../how-to-select-algorithms.md).
> 
> 

Você adicionará os módulos [Árvore de Decisão Aumentada de Duas Classes][two-class-boosted-decision-tree] e [Computador de Vetor de Suporte de Duas Classes][two-class-support-vector-machine] neste experimento.

### <a name="two-class-boosted-decision-tree"></a>Árvore de decisão aumentada em duas classes

Primeiro, configure o modelo de árvore de decisão aumentada.

1. Localize o módulo [Árvore de Decisão Aumentada de Duas Classes][two-class-boosted-decision-tree] na paleta do módulo e arraste-o para a tela.

1. Localize o módulo [Modelo de Treinamento][train-model], arraste-o até a tela e depois conecte a saída do módulo [Árvore de Decisão Aumentada de Duas Classes][two-class-boosted-decision-tree] à porta de entrada esquerda do módulo [Modelo de Treinamento][train-model].
   
   O módulo [Árvore de Decisão Aumentada de Duas Classes][two-class-boosted-decision-tree] inicializa o modelo genérico e o [Treinar Modelo][train-model] usa os dados de treinamento para treinar o modelo. 

1. Conecte a saída esquerda do módulo esquerdo [Executar Script R][execute-r-script] à porta de entrada direita do módulo [Treinar Modelo][train-model] (neste tutorial, você [usou os dados provenientes do lado esquerdo](#train) do módulo Dividir Dados para o treinamento).
   
   > [!TIP]
   > Você não precisará de duas das entradas e uma das saídas do módulo [Executar Script R][execute-r-script] para este experimento e, portanto, poderá deixá-las desanexadas. 
   > 
   > 

Esta parte do teste se parece um pouco com o seguinte:  

![Treinando um modelo](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Agora, você precisará informar o módulo [Treinar Modelo][train-model] de que deseja que o modelo preveja o valor do Risco de Crédito.

1. Selecione o módulo [Treinar Modelo][train-model]. No painel **Propriedades**, clique em **Iniciar seletor de coluna**.

1. Na caixa de diálogo **Selecionar uma única coluna**, digite "risco de crédito" no campo de pesquisa sob **Colunas Disponíveis**, selecione "Risco de Crédito" abaixo e clique no botão de seta para a direita ( **>** ) para mover o "Risco de crédito" para **Colunas Selecionadas**. 

    ![Selecione a coluna Risco de Crédito para o módulo Modelo de Treinamento](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Clique na marca de seleção **OK**.

### <a name="two-class-support-vector-machine"></a>Computador de vetor de suporte de duas classes

Em seguida, você configurará o modelo SVM.  

Primeiro, uma pequena explicação sobre o SVM. Árvores de decisão aumentadas funcionam bem com recursos de qualquer tipo. No entanto, como o módulo SVM gera um classificador linear, o modelo que ele gera apresenta o melhor erro de teste quando todos os recursos numéricos possuem a mesma escala. Para converter todos os recursos numéricos na mesma escala, use uma transformação "Tanh" (com o módulo [Normalizar Dados][normalize-data]). Isso transforma nossos números no intervalo [0,1]. O módulo SVM converte os recursos de cadeia de caracteres em recursos categóricos e, em seguida, em recursos binários 0/1. Portanto, não é necessário transformar manualmente os recursos de cadeia de caracteres. Além disso, você não deseja transformar a coluna Risco de Crédito (coluna 21) – ela é numérica, mas é o valor que estamos treinando para o modelo prever e, portanto, você precisa deixá-la inalterada.  

Para configurar o modelo SVM, faça o seguinte:

1. localize o módulo [Computador de Vetor de Suporte de Duas Classes][two-class-support-vector-machine] na paleta do módulo e arraste-o para a tela.

1. Clique com o botão direito do mouse no módulo [Treinar Modelo][train-model], selecione **Copiar** e, em seguida, clique com o botão direito do mouse na tela e selecione **Colar**. A cópia do módulo [Treinar Modelo][train-model] tem a mesma seleção de coluna que o original.

1. Conecte a saída do módulo [Computador de Vetor de Suporte de Duas Classes][two-class-support-vector-machine] à porta de entrada esquerda do segundo módulo [Treinar Modelo][train-model].

1. Localize o módulo [Normalizar Dados][normalize-data] e arraste-o para a tela.

1. Conecte a saída esquerda do módulo [Executar Script R][execute-r-script] esquerdo à entrada desse módulo (observe que a porta de saída de um módulo pode ser conectada a mais de um outro módulo).

1. Conecte a porta de saída esquerda do módulo [Normalizar Dados][normalize-data] à porta de entrada direita do segundo módulo [Treinar Modelo][train-model].

Esta parte de nosso teste deve se parecer um pouco com o seguinte:  

![Treinando o segundo modelo](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Agora configure o módulo [Normalizar Dados][normalize-data]:

1. clique para selecionar o módulo [Normalizar Dados][normalize-data]. No painel **Propriedades**, selecione **Tanh** para o parâmetro **Método de transformação**.

1. Clique no **Seletor de coluna de inicialização**, selecione “Sem colunas” para **Começar Com**, selecione **Incluir** na primeira lista suspensa, selecione **tipo de coluna** na segunda lista suspensa e selecione **Numérico** na terceira lista suspensa. Isso especifica que todas as colunas numéricas (e somente as numéricas) serão transformadas.

1. Clique no sinal de adição (+) à direita desta linha - isso cria uma nova linha de listas suspensas. Selecione **Excluir** na primeira lista suspensa, selecione **nomes de coluna** na segunda lista suspensa e insira "Risco de Crédito" no campo de texto. Isso especifica que a coluna Risco de Crédito deve ser ignorada (você precisará fazer isso porque essa coluna é numérica e, portanto, será transformada se você não a excluir).

1. Clique na marca de seleção **OK**.  

    ![Selecionar as colunas para o módulo Normalizar Dados](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


Agora, o módulo [Normalizar Dados][normalize-data] está definido para executar uma transformação Tanh em todas as colunas numéricas, exceto para a coluna Risco de Crédito.  

## <a name="score-and-evaluate-the-models"></a>Pontuar e avaliar os modelos

Você usará os dados de teste que foram separados pelo módulo [Dividir Dados][split] a fim de pontuar nossos modelos treinados. Em seguida, você poderá comparar os resultados dos dois modelos para ver qual gerou os melhores resultados.  

### <a name="add-the-score-model-modules"></a>Adicionar os módulos de Modelo de Pontuação

1. Localize o módulo [Modelo de Pontuação][score-model] e arraste-o para a tela.

1. Conecte o módulo [Treinar Modelo][train-model] que está conectado ao módulo [Árvore de Decisão Aumentada de Duas Classes][two-class-boosted-decision-tree] à porta de entrada esquerda do módulo [Modelo de Pontuação][score-model].

1. Conecte o módulo [Executar Script R][execute-r-script] (nossos dados de teste) à porta de entrada direita do módulo [Modelo de Pontuação][score-model].

    ![Módulo Modelo de Pontuação conectado](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   Agora, o módulo [Modelo de Pontuação][score-model] pode pegar as informações de crédito dos dados de teste, executá-las por meio do modelo e comparar as previsões que o modelo gera com a coluna de risco de crédito real dos dados de teste.

1. Copie e cole o módulo [Modelo de Pontuação][score-model] para criar uma segunda cópia.

1. Conecte a saída do modelo SVM (ou seja, a porta de saída do módulo [Treinar Modelo][train-model] que está conectada ao módulo [Computador de Vetor de Suporte de Duas Classes][two-class-support-vector-machine]) à porta de entrada do segundo módulo [Modelo de Pontuação][score-model].

1. Para o modelo SVM, você precisará fazer a mesma transformação nos dados de teste que você fez nos dados de treinamento. Portanto, copie e cole o módulo [Normalizar Dados][normalize-data] para criar uma segunda cópia e conectá-la ao módulo [Executar Script R][execute-r-script] direito.

1. Conecte a saída esquerda do segundo módulo [Normalizar Dados][normalize-data] à porta de entrada direita do segundo módulo [Modelo de Pontuação][score-model].

    ![Os dois módulos Modelo de Pontuação estão conectados](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Adicionar o módulo Modelo de Avaliação

Para avaliar os dois resultados de pontuação e compará-los entre si, use um módulo [Modelo de Avaliação][evaluate-model].  

1. Localize o módulo [Modelo de Avaliação][evaluate-model] e arraste-o para a tela.

1. Conecte a porta de saída do módulo [Modelo de Pontuação][score-model] associado ao modelo de árvore de decisão aumentada à porta de entrada esquerda do módulo [Modelo de Avaliação][evaluate-model].

1. Conecte o outro módulo [Modelo de Pontuação][score-model] à porta de entrada direita.  

    ![Módulo Modelo de Avaliação conectado](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Execute o teste e verifique os resultados

Clique no botão **EXECUTAR** abaixo das telas para executar o teste. Isso pode levar alguns minutos. Um indicador girando em cada módulo indica que o módulo está em execução e, depois, uma marca de seleção verde mostra quando o módulo foi concluído. Quando todos os modelos tiverem uma marca de seleção, a execução do teste estará concluída.

O teste deve se parecer como o seguinte:  

![Avaliando os dois modelos](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Para verificar os resultados, clique na porta de saída do módulo [Modelo de Avaliação][evaluate-model] e selecione **Visualizar**.  

O módulo [Modelo de Avaliação][evaluate-model] produz um par de curvas e métricas que lhe permitem comparar os resultados dos dois modelos pontuados. Você pode exibir os resultados como curvas ROC (Receiver Operator Characteristic), curvas de Precisão/Repetição, ou curvas de Elevação. Os dados adicionais exibidos incluem uma matriz de confusão, valores cumulativos para a área sob a curva (AUC) e outras métricas. Você pode alterar o valor de limite movendo o controle deslizante para esquerda ou direita e vendo como isso afeta o conjunto de métricas.  

À direita do gráfico, clique em **Conjunto de dados pontuados** ou **Conjunto de dados pontuados para comparar** para destacar a curva associada e exibir as métricas associadas abaixo. Na legenda das curvas, "Conjunto de dados pontuados" corresponde à porta de entrada esquerda do módulo [Modelo de Avaliação][evaluate-model] - em nosso caso, esse é o modelo de árvore de decisão aumentada. O "Conjunto de dados pontuados para comparar" corresponde à porta de entrada direita - o modelo SVM em nosso caso. Ao clicar em um desses rótulos, a curva desse modelo será destacada e exibirá as métricas correspondentes, como demonstrado no gráfico a seguir.  

![Curvas ROC dos modelos](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Ao examinar esses valores, você pode decidir qual modelo está mais próximo de fornecer os resultados que você está procurando. Você pode voltar e iterar seu teste alterando os valores de parâmetro nos diferentes modelos. 

A ciência e a arte de interpretar esses resultados e ajustar o desempenho do modelo estão fora do escopo deste tutorial. Para obter mais ajuda, leia os artigos a seguir:
- [Como avaliar o desempenho do modelo no Azure Machine Learning Studio (clássico)](evaluate-model-performance.md)
- [Escolher parâmetros para otimizar os algoritmos no Azure Machine Learning Studio (clássico)](algorithm-parameters-optimize.md)
- [Interpretar os resultados do modelo no Azure Machine Learning Studio (clássico)](interpret-model-results.md)

> [!TIP]
> Toda vez que você executar o teste, um registro dessa iteração será mantido no Histórico de Execução. Você pode exibir essas iterações e retornar a qualquer item clicando em **EXIBIR HISTÓRICO DE EXECUÇÃO** abaixo das telas. Você também pode clicar em **Execução Anterior** no painel **Propriedades** para retornar à iteração imediatamente, precedendo aquela que você abriu.
> 
> Você pode fazer uma cópia de qualquer iteração de seu teste clicando em **SALVAR COMO** abaixo das telas. 
> Use as propriedades **Resumo** e **Descrição** do teste para manter um registro do que você experimentou em suas iterações de teste.
> 
> Para obter mais informações, confira [Gerenciar iterações de experimento no Azure Machine Learning Studio (clássico)](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você concluiu estas etapas: 
 
> [!div class="checklist"]
> * Criar uma experiência
> * Treinar vários modelos
> * Pontuar e avaliar os modelos

Agora você está pronto para implantar modelos para esses dados.

> [!div class="nextstepaction"]
> [Tutorial 3 – Implantar modelos](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[split]: /azure/machine-learning/studio-module-reference/split-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/