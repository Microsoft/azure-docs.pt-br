---
title: "Etapa 4: treinar e avaliar os modelos de análise preditiva | Microsoft Docs"
description: "Etapa 4 - desenvolver um passo a passo de solução de previsão: treinar, pontuar e avaliar vários modelos no Azure Machine Learning Studio."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: d905f6b3-9201-4117-b769-5f9ed5ee1cac
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: 5a545b9f9f7f935be91fed743779ebd6b4d930a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Etapa 4 do passo a passo: Treinar e avaliar os modelos de análise preditiva
Esse tópico contém a quarta etapa do passo a passo, [Desenvolver uma solução de análise preditiva com o Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Criar um espaço de trabalho do Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Carregar dados existentes](walkthrough-2-upload-data.md)
3. [Criar um novo teste](walkthrough-3-create-new-experiment.md)
4. **Treinar e avaliar os modelos**
5. [Implantar o serviço Web](walkthrough-5-publish-web-service.md)
6. [Acessar o serviço Web](walkthrough-6-access-web-service.md)

- - -
Uma das vantagens de usar o Azure Machine Learning Studio para criar modelos de aprendizado de máquina é a capacidade de experimentar mais de um tipo de modelo de cada vez em um único teste e comparar os resultados. Esse tipo de experimentação ajuda a encontrar a melhor solução para seu problema.

No teste que estamos desenvolvendo neste passo a passo, vamos criar dois tipos diferentes de modelos e comparar os resultados de suas pontuações para decidir qual algoritmo desejamos usar em nosso teste final.  

Existem diversos modelos dentre os quais podemos escolher. Para ver os modelos disponíveis, expanda o nó **Machine Learning** na paleta do módulo e expanda **Inicializar Modelo** e os nós abaixo dele. Para os fins deste teste, selecionaremos os módulos [Computador de Vetor de Suporte (SVM) de Duas Classes][two-class-support-vector-machine] e [Árvores de Decisão Aumentada de Duas Classes][two-class-boosted-decision-tree].    

> [!TIP]
> Para obter ajuda para decidir qual algoritmo do Machine Learning melhor se ajusta ao problema específico que você está tentando resolver, consulte [Como escolher algoritmos de Microsoft Azure Machine Learning](algorithm-choice.md).
> 
> 

## <a name="train-the-models"></a>Treinar os modelos

Adicionaremos os módulos [Arvore de Decisão Aumentada de Duas Classes][two-class-boosted-decision-tree] e [Computador de Vetor de Suporte de Duas Classes][two-class-support-vector-machine] neste teste.

### <a name="two-class-boosted-decision-tree"></a>Árvore de decisão aumentada em duas classes

Primeiro, vamos configurar o modelo de árvore de decisão aumentada.

1. Localize o módulo [Árvore de Decisão Aumentada de Duas Classes][two-class-boosted-decision-tree] na paleta do módulo e arraste-o até a tela.

2. Localize o módulo [Modelo de Treinamento][train-model], arraste-o até a tela e depois conecte a saída do módulo [Árvore de Decisão Aumentada de Duas Classes][two-class-boosted-decision-tree] à porta de entrada esquerda do módulo [Modelo de Treinamento][train-model].
   
   O módulo [Árvore de Decisão Aumentada de Duas Classes][two-class-boosted-decision-tree] inicializa o modelo genérico, e [Modelo de Treinamento][train-model] usa dados de treinamento para treinar o modelo. 

3. Conecte a saída esquerda do módulo [Executar Script R][execute-r-script] esquerdo à porta de entrada direita do módulo à direita do módulo [Modelo de Treinamento][train-model] (decidimos na [Etapa 3](walkthrough-3-create-new-experiment.md) deste passo a passo usar os dados provenientes do lado esquerdo do módulo Dividir Dados para o treinamento).
   
   > [!TIP]
   > Não precisamos de duas das entradas e uma das saídas do módulo [Executar Script R][execute-r-script] para este teste, portanto, as deixaremos desconectadas. 
   > 
   > 

Esta parte do teste se parece um pouco com o seguinte:  

![Treinando um modelo][1]

Agora, precisamos informar ao módulo [Modelo de Treinamento][train-model] que queremos que o modelo preveja o valor do Risco de Crédito.

1. Selecione o módulo [Modelo de Treinamento][train-model]. No painel **Propriedades**, clique em **Iniciar seletor de coluna**.

2. Na caixa de diálogo **Selecionar uma única coluna**, digite "risco de crédito" no campo de pesquisa sob **Colunas Disponíveis**, selecione "Risco de Crédito" abaixo e clique no botão de seta para a direita (**>**) para mover o "Risco de crédito" para **Colunas Selecionadas**. 

    ![Selecione a coluna Risco de Crédito para o módulo Modelo de Treinamento][0]

3. Clique na marca de seleção **OK**.

### <a name="two-class-support-vector-machine"></a>Computador de vetor de suporte de duas classes

Em seguida, configuramos o modelo SVM.  

Primeiro, uma pequena explicação sobre o SVM. Árvores de decisão aumentadas funcionam bem com recursos de qualquer tipo. No entanto, como o módulo SVM gera um classificador linear, o modelo que ele gera apresenta o melhor erro de teste quando todos os recursos numéricos possuem a mesma escala. Para converter todos os recursos numéricos para a mesma escala, usamos a Transformação “Tanh” (com o módulo [Normalizar Dados][normalize-data]). Isso transforma nossos números no intervalo [0,1]. O módulo SVM converte os recursos de cadeia de caracteres em recursos categóricos e, em seguida, para recursos binários 0/1, portanto, não precisamos transformar manualmente os recursos de cadeia de caracteres. Também não desejamos transformar a coluna Risco de Crédito (coluna 21) – ela é numérica, mas esse é o valor que estamos treinando para o modelo prever, portanto, precisamos deixá-la intacta.  

Para configurar o modelo SVM, faça o seguinte:

1. Localize o módulo [Computador de Vetor de Suporte de Duas Classes][two-class-support-vector-machine] na paleta do módulo e arraste-o para as telas.

2. Clique com o botão direito do mouse no módulo [Modelo de Treinamento][train-model], selecione **Copiar**, depois clique com o botão direito do mouse na tela e selecione **Colar**. A cópia do módulo [Modelo de Treinamento][train-model] tem a mesma seleção de coluna que o original.

3. Conecte a saída do módulo [Computador de Vetor de Suporte de Duas Classes][two-class-support-vector-machine] à porta de entrada esquerda do segundo módulo [Modelo de Treinamento][train-model].

4. Localize o módulo [Normalizar Dados][normalize-data] e arraste-o até a tela.

5. Conecte a saída esquerda do módulo [Executar Script R][execute-r-script] esquerdo à entrada desse módulo (observe que a porta de saída de um módulo pode ser conectada a mais de um outro módulo).

6. Conecte a porta de saída esquerda do módulo [Normalizar Dados][normalize-data] à porta de entrada direita do segundo módulo [Modelo de Treinamento][train-model].

Esta parte de nosso teste deve se parecer um pouco com o seguinte:  

![Treinando o segundo modelo][2]  

Agora configure o módulo [Normalizar Dados][normalize-data]:

1. Clique para selecionar o módulo [Normalizar Dados][normalize-data]. No painel **Propriedades**, selecione **Tanh** para o parâmetro **Método de transformação**.

2. Clique no **Seletor de coluna de inicialização**, selecione “Sem colunas” para **Começar Com**, selecione **Incluir** na primeira lista suspensa, selecione **tipo de coluna** na segunda lista suspensa e selecione **Numérico** na terceira lista suspensa. Isso especifica que todas as colunas numéricas (e somente as numéricas) serão transformadas.

3. Clique no sinal de adição (+) à direita desta linha - isso cria uma nova linha de listas suspensas. Selecione **Excluir** na primeira lista suspensa, selecione **nomes de coluna** na segunda lista suspensa e insira "Risco de Crédito" no campo de texto. Isso especifica que a coluna Risco de crédito deve ser ignorada (precisamos fazer isso porque essa coluna é numérica e seria transformada se não a excluíssemos).

4. Clique na marca de seleção **OK**.  

    ![Selecionar as colunas para o módulo Normalizar Dados][5]

Agora, o módulo [Normalizar Dados][normalize-data] está definido para executar uma transformação Tanh em todas as colunas numéricas, exceto para a coluna Risco de crédito.  

## <a name="score-and-evaluate-the-models"></a>Pontuar e avaliar os modelos

Usaremos os dados de teste que foram separados pelo módulo [Dividir Dados][split] para pontuar nossos modelos treinados. Podemos comparar os resultados dos dois modelos para ver quais geraram os melhores resultados.  

### <a name="add-the-score-model-modules"></a>Adicionar os módulos de Modelo de Pontuação

1. Localize o módulo [Modelo de Pontuação][score-model] e arraste-o até a tela.

2. Conecte o módulo [Treinar modelo][train-model] que esteja conectado ao módulo [Árvore de Decisão Aumentada de Duas Classes][two-class-boosted-decision-tree] à porta de entrada da esquerda do módulo [Modelo de Pontuação][score-model].

3. Conecte o módulo [Executar Script R][execute-r-script] (nossos dados de teste) à porta de entrada direita do módulo [Modelo de Pontuação][score-model].

    ![Módulo Modelo de Pontuação conectado][6]
   
   O módulo [Modelo de Pontuação][score-model] pode pegar as informações de crédito dos dados de teste, executá-los por meio do modelo e comparar as previsões que o modelo gera com a coluna de risco de crédito real nos dados de teste.

4. Copie e cole o módulo [Modelo de Pontuação][score-model] para criar uma segunda cópia.

5. Conecte a saída do modelo SVM (ou seja, a porta de saída do módulo [Modelo de Treinamento][train-model] que está conectado ao módulo [Computador de Vetor de Suporte de Duas Classes][two-class-support-vector-machine]) à porta de entrada do segundo módulo [Modelo de Pontuação][score-model].

6. Para o modelo SVM, precisamos fazer a mesma transformação nos dados do teste que fizemos nos dados de treinamento. Portanto, copie e cole o módulo [Normalizar Dados][normalize-data] para criar uma segunda cópia e conectá-la ao módulo [Executar Script R][execute-r-script] direito.

7. Conecte a saída esquerda do segundo módulo [Normalizar Dados][normalize-data] à porta de entrada direita do segundo módulo [Modelo de Pontuação][score-model].

    ![Os dois módulos Modelo de Pontuação estão conectados][7]

### <a name="add-the-evaluate-model-module"></a>Adicionar o módulo Modelo de Avaliação

Para avaliar os dois resultados de pontuação e compará-los, usaremos um módulo [Modelo de Avaliação][evaluate-model].  

1. Localize o módulo [Modelo de Avaliação][evaluate-model] e arraste-o até a tela.

2. Conecte a porta de saída do módulo [Modelo de Pontuação][score-model] associado ao modelo de árvore de decisão aumentada à porta de entrada esquerda do módulo [Modelo de Avaliação][evaluate-model].

3. Conecte o outro módulo de [Modelo de Pontuação][score-model] à porta de entrada direita.  

    ![Módulo Modelo de Avaliação conectado][8]

### <a name="run-the-experiment-and-check-the-results"></a>Execute o teste e verifique os resultados

Clique no botão **EXECUTAR** abaixo das telas para executar o teste. Isso pode levar alguns minutos. Um indicador girando em cada módulo indica que o módulo está em execução e, depois, uma marca de seleção verde mostra quando o módulo foi concluído. Quando todos os modelos tiverem uma marca de seleção, a execução do teste estará concluída.

O teste deve se parecer como o seguinte:  

![Avaliando os dois modelos][3]

Para verificar os resultados, clique na porta de saída do módulo [Modelo de Avaliação][evaluate-model] e selecione **Visualizar**.  

O módulo [Modelo de Avaliação][evaluate-model] produz um par de curvas e métricas que lhe permitem comparar os resultados dos dois modelos pontuados. Você pode exibir os resultados como curvas ROC (Receiver Operator Characteristic), curvas de Precisão/Repetição, ou curvas de Elevação. Os dados adicionais exibidos incluem uma matriz de confusão, valores cumulativos para a área sob a curva (AUC) e outras métricas. Você pode alterar o valor de limite movendo o controle deslizante para esquerda ou direita e vendo como isso afeta o conjunto de métricas.  

À direita do gráfico, clique em **Conjunto de dados pontuados** ou **Conjunto de dados pontuados para comparar** para destacar a curva associada e exibir as métricas associadas abaixo. Na legenda das curvas, "Conjunto de dados pontuados" corresponde à porta de entrada esquerda do módulo [Modelo de Avaliação][evaluate-model] - em nosso caso, esse é o modelo de árvore de decisão aumentada. O "Conjunto de dados pontuados para comparar" corresponde à porta de entrada direita - o modelo SVM em nosso caso. Ao clicar em um desses rótulos, a curva desse modelo será destacada e exibirá as métricas correspondentes, como demonstrado no gráfico a seguir.  

![Curvas ROC dos modelos][4]

Ao examinar esses valores, você pode decidir qual modelo está mais próximo de fornecer os resultados que você está procurando. Você pode voltar e iterar seu teste alterando os valores de parâmetro nos diferentes modelos. 

A ciência e arte de interpretar esses resultados e ajustar o desempenho do modelo está fora do escopo deste passo a passo. Para obter mais ajuda, leia os artigos a seguir:
- [Como avaliar o desempenho do modelo no Azure Machine Learning](evaluate-model-performance.md)
- [Escolher parâmetros para otimizar seus algoritmos do Azure Machine Learning](algorithm-parameters-optimize.md)
- [Interpretar os resultados do modelo no Azure Machine Learning](interpret-model-results.md)

> [!TIP]
> Toda vez que você executar o teste, um registro dessa iteração será mantido no Histórico de Execução. Você pode exibir essas iterações e retornar a qualquer item clicando em **EXIBIR HISTÓRICO DE EXECUÇÃO** abaixo das telas. Você também pode clicar em **Execução Anterior** no painel **Propriedades** para retornar à iteração imediatamente, precedendo aquela que você abriu.
> 
> Você pode fazer uma cópia de qualquer iteração de seu teste clicando em **SALVAR COMO** abaixo das telas. 
> Use as propriedades **Resumo** e **Descrição** do teste para manter um registro do que você experimentou em suas iterações de teste.
> 
> Consulte [Gerenciar iterações do experimento no Machine Learning Studio do Azure](manage-experiment-iterations.md)para obter mais detalhes.  
> 
> 

- - -
**Próximo: [implantar o serviço Web](walkthrough-5-publish-web-service.md)**

[0]: ./media/walkthrough-4-train-and-evaluate-models/train-model-select-column.png
[1]: ./media/walkthrough-4-train-and-evaluate-models/experiment-with-train-model.png
[2]: ./media/walkthrough-4-train-and-evaluate-models/svm-model-added.png
[3]: ./media/walkthrough-4-train-and-evaluate-models/final-experiment.png
[4]: ./media/walkthrough-4-train-and-evaluate-models/roc-curves.png
[5]: ./media/walkthrough-4-train-and-evaluate-models/normalize-data-select-column.png
[6]: ./media/walkthrough-4-train-and-evaluate-models/score-model-connected.png
[7]: ./media/walkthrough-4-train-and-evaluate-models/both-score-models-added.png
[8]: ./media/walkthrough-4-train-and-evaluate-models/evaluate-model-added.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
