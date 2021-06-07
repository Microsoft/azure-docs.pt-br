---
title: 'Tutorial: Prever preços de carro com o designer'
titleSuffix: Azure Machine Learning
description: Treine o modelo de machine learning para prever preços de automóveis usando a regressão linear. Este tutorial é parte de uma série de duas partes.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 09/28/2020
ms.custom: designer
ms.openlocfilehash: 7f602f82063e2a8092eecf533a75449231c1e900
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660977"
---
# <a name="tutorial-predict-automobile-price-with-the-designer"></a>Tutorial: Prever preço de automóvel com o designer


Neste tutorial de duas partes, você aprenderá a usar o designer do Azure Machine Learning para treinar e implantar um modelo de machine learning que prevê o preço de qualquer carro. O designer é uma ferramenta do tipo "arrastar e soltar" que permite criar modelos de machine learning sem nenhuma linha de código.

Na primeira parte do tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um pipeline.
> * Importar dados.
> * Preparar os dados.
> * Treinar um modelo de machine learning.
> * Avaliar um modelo de machine learning.

Na [segunda parte](tutorial-designer-automobile-price-deploy.md) do tutorial, você implantará seu modelo como um ponto de extremidade de inferência em tempo real para prever o preço de qualquer carro com base nas especificações técnicas enviadas. 

> [!NOTE]
>Uma versão concluída deste tutorial está disponível como um pipeline de exemplo.
>
>Para encontrá-lo, acesse o designer em seu workspace. Na seção **Novo pipeline**, selecione **Amostra 1 – Regressão: Previsão de Preços de Automóveis (Básica)** .

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-new-pipeline"></a>Criar um novo pipeline

Os pipelines do Azure Machine Learning organizam várias etapas do aprendizado de máquina e do processamento de dados em um só recurso. Os pipelines permitem que você organize, gerencie e reutilize fluxos de trabalho complexos de aprendizado de máquina entre projetos e usuários.

Para criar um pipeline do Azure Machine Learning, você precisa de um workspace do Azure Machine Learning. Nesta seção, você aprenderá a criar esses dois recursos.

### <a name="create-a-new-workspace"></a>Criar um novo workspace

Você precisa de um Workspace do Azure Machine Learning para usar o designer. O workspace é o recurso de nível superior para Azure Machine Learning; ele fornece um local centralizado para trabalhar com todos os artefatos que você cria no Azure Machine Learning. Para obter instruções sobre como criar um workspace, confira [Criar e gerenciar workspaces do Azure Machine Learning](how-to-manage-workspace.md).

> [!NOTE]
> Se o seu workspace usa uma rede virtual, há etapas de configuração adicionais que você precisa seguir para usar o designer. Para obter mais informações, confira [Usar o estúdio do Azure Machine Learning em uma rede virtual do Azure](how-to-enable-studio-virtual-network.md)

### <a name="create-the-pipeline"></a>Criar o pipeline

1. Entre em <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> e selecione o workspace com o qual deseja trabalhar.

1. Selecione **Designer**.

    ![Captura de tela do workspace visual mostrando como acessar o designer](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Selecione **Módulos predefinidos e fáceis de usar**.

1. Na parte superior da tela, selecione o nome do pipeline padrão **Pipeline-Created-on**. Renomeie-o como *Previsão de preços de automóveis*. O nome não precisa ser exclusivo.

## <a name="set-the-default-compute-target"></a>Definir o destino de computação padrão

Um pipeline é executado em um destino de computação, que é um recurso de computação anexado ao workspace. Depois de criar um destino de computação, você poderá reutilizá-lo para execuções futuras.

Você pode definir um **Destino de computação padrão** para o pipeline inteiro, que informará a cada módulo para usar o mesmo destino de computação por padrão. No entanto, você pode especificar destinos de computação por módulo.

1. Ao lado do nome do pipeline, selecione o **ícone de engrenagem** ![Captura de tela do ícone de engrenagem](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) na parte superior da tela para abrir o painel **Configurações**.

1. No painel **Configurações** à direita da tela, selecione **Selecionar de destino de computação**.

    Se já tiver um destino de computação disponível, você poderá selecioná-lo para executar esse pipeline.

    > [!NOTE]
    > O designer só pode executar experimentos de treinamento na Computação do Azure Machine Learning, mas outros destinos de computação não são mostrados.

1. Insira um nome para o recurso de computação.

1. Clique em **Salvar**.

    > [!NOTE]
    > A criação de um recurso de computação demora aproximadamente cinco minutos. Depois que o recurso for criado, você poderá reutilizá-lo e ignorar esse tempo de espera para execuções futuras.
    >
    > O recurso de computação será dimensionado automaticamente para zero nós quando estiver ocioso, a fim de economizar custos. Ao usá-lo novamente após um atraso, talvez precise aguardar aproximadamente cinco minutos enquanto ele é escalado verticalmente mais uma vez.

## <a name="import-data"></a>Importar dados

Há vários conjuntos de dados de exemplo incluídos no designer para que você possa fazer experimentos. Neste tutorial, use os **Dados de preços de automóveis (brutos)** . 

1. À esquerda da tela do pipeline há uma paleta de conjuntos de dados e módulos. Selecione **Conjuntos de dados de exemplo** para exibir os conjuntos de dados de exemplo disponíveis.

1. Selecione o conjunto de dados **Dados de preços de automóveis (Brutos)** e arraste-o para a tela.

   ![Arraste os dados para a tela](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Visualizar os dados

Você pode visualizar os dados para entender o conjunto de dados que será usado.

1. Clique com o botão direito do mouse nos **Dados de preço do automóvel (Brutos)** e selecione **Visualizar** > **Saída do conjunto de dados**.

1. Selecione as diferentes colunas na janela de dados para exibir informações sobre cada um.

    Cada linha representa um automóvel e as variáveis associadas a cada automóvel aparecem como colunas. Há 205 linhas e 26 colunas nesse conjunto de dados.

## <a name="prepare-data"></a>Preparar dados

Os conjuntos de dados normalmente exigem algum pré-processamento antes da análise. Talvez você tenha observado alguns valores ausentes quando inspecionou o conjunto de dados. Esses valores ausentes precisam ser limpos para que o modelo possa analisar os dados corretamente.

### <a name="remove-a-column"></a>Remover uma coluna

Quando treina um modelo, você precisa fazer algo sobre os dados que estão faltando. Neste conjunto de dados, a coluna **normalized-losses** tem muitos valores ausentes; portanto, você a excluirá do modelo completamente.

1. Na paleta de módulos à esquerda da tela, expanda a seção **Transformação de Dados** e localize o módulo **Selecionar colunas no conjunto de dados**.

1. Arraste o módulo **Selecionar Colunas no Conjunto de Dados** para a tela. Solte o módulo embaixo do módulo do conjunto de dados.

1. Conecte o conjunto de dados **Dados de preços de automóveis (Brutos)** ao módulo **Selecionar Colunas no Conjunto de Dados**. Arraste da porta de saída do conjunto de dados, que é o pequeno círculo na parte inferior do conjunto de dados na tela, até a porta de entrada do módulo **Selecionar colunas no conjunto de dados**, que é o pequeno círculo na parte superior do módulo.

    > [!TIP]
    > Crie um fluxo de dados por meio do seu pipeline quando você conectar a porta de saída de um módulo a uma porta de entrada de outro.
    >

    ![Conectar módulos](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Selecione o módulo **Selecionar colunas no conjunto de dados**.

1. No painel de detalhes do módulo à direita da tela, selecione **Editar coluna**.

1. Expanda o menu suspenso **Nomes de coluna** ao lado de **Incluir** e selecione **Todas as colunas**.

1. Selecione o **+** para adicionar uma nova regra.

1. Nos menus suspensos, selecione **Excluir** e **Nomes de coluna**.
    
1. Insira *normalized-losses* na caixa de texto.

1. No canto inferior direito, selecione **Salvar** para fechar o seletor de coluna.

    ![Excluir uma coluna](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Selecione o módulo **Selecionar colunas no conjunto de dados**. 

1. No painel detalhes do módulo à direita da tela, marque a caixa de texto **Comentário** e insira *Excluir perdas normalizadas*.

    Os comentários serão exibidos no grafo para ajudar você a organizar seu pipeline.

### <a name="clean-missing-data"></a>Limpar dados ausentes

Seu conjunto de dados ainda tem valores ausentes após a remoção da coluna **normalized-losses**. Você pode remover os dados ausentes restantes usando o módulo **Limpar Dados Ausentes**.

> [!TIP]
> Limpar os valores ausentes dos dados de entrada é um pré-requisito para usar a maioria dos módulos do designer.

1. Na paleta de módulos à esquerda da tela, expanda a seção **Transformação de Dados** e localize o módulo **Limpar Dados Ausentes**.

1. Arraste o módulo **Limpar Dados Ausentes** para a tela do pipeline. Conecte-o ao módulo **Selecionar Colunas no Conjunto de Dados**. 

1. Selecione o módulo **Limpar Dados Ausentes**.

1. No painel de detalhes do módulo à direita da tela, selecione **Editar Coluna**.

1. Na janela **Colunas a serem limpas** que é exibida, expanda o menu suspenso ao lado de **Incluir**. Selecione **Todas as colunas**

1. Selecione **Salvar**

1. No painel detalhes do módulo à direita da tela, selecione **Remover linha inteira** em **Modo de limpeza**.

1. No painel detalhes do módulo à direita da tela, marque a caixa de texto **Comentário** e insira *Remover linhas com valores ausentes*. 

    Agora, seu pipeline deve ser semelhante ao seguinte:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png" alt-text="Select-column":::

## <a name="train-a-machine-learning-model"></a>Treinar um modelo de machine learning

Agora que você tem os módulos em vigor para processar os dados, configure os módulos de treinamento.

Como você deseja prever o preço, que é um número, use um algoritmo de regressão. Para este exemplo, você usará um modelo de regressão linear.

### <a name="split-the-data"></a>Dividir os dados

A divisão de dados é uma tarefa comum no aprendizado de máquina. Você dividirá seus dados em dois conjuntos de dados separados. Um conjunto de dados treinará o modelo e o outro testará o desempenho do modelo.

1. Na paleta de módulos, expanda a seção **Transformação de Dados** e localize o módulo **Dividir Dados**.

1. Arraste o módulo **Dividir Dados** até a tela do pipeline.

1. Conecte a porta esquerda do módulo **Limpar Dados Ausentes** ao módulo **Dividir Dados**.

    > [!IMPORTANT]
    > Verifique se as portas de saída esquerdas de **Limpar Dados Ausentes** se conectam a **Dividir Dados**. A porta esquerda contém os dados limpos. A porta direita contém os dados descartados.

1. Selecione o módulo **Dividir dados**.

1. No painel de detalhes do módulo à direita da tela, defina a **Fração de linhas no primeiro conjunto de dados de saída** como 0,7.

    Essa opção divide 70% dos dados para treinar o modelo e 30% para testá-lo. O conjunto de dados de 70% estará acessível por meio da porta de saída esquerda. Os dados restantes estarão disponíveis por meio da porta de saída direita.

1. No painel detalhes do módulo à direita da tela, marque a caixa **Comentário** e insira *Dividir o conjunto de dados em um conjunto de treinamento (0,7) e conjunto de teste (0,3)* .

### <a name="train-the-model"></a>Treinar o modelo

Treine o modelo fornecendo a ele um conjunto de dados que inclua o preço. O algoritmo constrói um modelo que explica a relação entre os recursos e o preço, conforme apresentado pelos dados de treinamento.

1. Na paleta de módulos, expanda **Algoritmos de Machine Learning**.
    
    Essa opção exibe várias categorias de módulos que podem ser usados para inicializar algoritmos de aprendizado.

1. Selecione **Regressão** > **Regressão Linear** e arraste-a para a tela do pipeline.

1. Na paleta de módulos, expanda a seção **Treinamento de módulo** e arraste o módulo **Treinar Modelo** até a tela.

1. Conecte a saída do módulo **Regressão Linear** à entrada esquerda do módulo **Treinar Modelo**.

1. Conecte-se a saída dos dados de treinamento (porta esquerda) do módulo **Dividir Dados** à entrada à direita do módulo **Treinar Modelo**.
    
    > [!IMPORTANT]
    > Verifique se as portas de saída esquerdas de **Dividir Dados** se conectam a **Treinar Modelo**. A porta esquerda contém o conjunto de treinamento. A porta direita contém o conjunto de teste.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png" alt-text="Captura de tela mostrando a configuração correta do módulo Treinar Modelo. O módulo Regressão Linear se conecta à porta esquerda e o módulo Dividir Dados se conecta à porta direita do módulo Treinar Modelo.":::

1. Selecione o módulo **Treinar Modelo**.

1. No painel de detalhes do módulo à direita da tela, selecione o seletor **Editar coluna**.

1. Na caixa de diálogo **Coluna de rótulo**, expanda o menu suspenso e selecione **Nomes de colunas**. 

1. Na caixa de texto, insira *preço* para especificar o valor que o modelo vai prever.

    >[!IMPORTANT]
    > É necessário que você insira o nome exato da coluna. Não use letras maiúsculas em **price**. 

    Seu pipeline deve ter esta aparência:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png" alt-text="Captura de tela mostrando a configuração correta do pipeline após a adição do módulo Treinar Modelo.":::

### <a name="add-the-score-model-module"></a>Módulo Adicionar o Modelo de Pontuação

Depois de treinar o modelo usando 70% dos dados, você poderá usá-lo para pontuar os outros 30% e ver se o modelo funciona corretamente.

1. Insira *pontuar modelo* na caixa de pesquisa para encontrar o módulo **Pontuar Modelo**. Arraste o módulo para a tela do pipeline. 

1. Conecte a saída do módulo **Treinar Modelo** à porta de entrada esquerda do módulo **Pontuar Modelo**. Conecte a saída de dados de teste (porta direita) do módulo **Dividir Dados** à porta de entrada direita do módulo **Pontuar Modelo**.

### <a name="add-the-evaluate-model-module"></a>Adicionar o módulo Modelo de Avaliação

Use o módulo **Avaliar Modelo** para avaliar a o desempenho do modelo na pontuação do conjunto de dados de teste.

1. Insira *avaliar* na caixa de pesquisa para encontrar o módulo **Avaliar Modelo**. Arraste o módulo para a tela do pipeline. 

1. Conecte a saída do módulo **Pontuar Modelo** à entrada esquerda do módulo **Avaliar Modelo**. 

    O pipeline final deve ser semelhante ao seguinte:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png" alt-text="Captura de tela mostrando a configuração correta do pipeline.":::

## <a name="submit-the-pipeline"></a>Enviar o pipeline

Agora que o pipeline está configurado, você poderá enviar uma execução de pipeline para treinar seu modelo de machine learning. Você pode enviar uma execução de pipeline válida a qualquer momento, que pode ser usada para examinar as alterações no pipeline durante o desenvolvimento.

1. Na parte superior da tela, selecione **Enviar**.

1. Na caixa de diálogo **Configurar execução de pipeline**, selecione **Criar**.

    > [!NOTE]
    > Pipelines semelhantes no grupo de experimentos são executados juntos. Se executar um pipeline várias vezes, você poderá selecionar o mesmo experimento para execuções sucessivas.

    1. Para obter um **Novo nome do experimento**, insira **Tutorial-CarPrices**.

    1. Selecione **Enviar**.
    
    Você pode exibir o status e os detalhes da execução no canto superior direito da tela.
    
    Se essa for a primeira execução do pipeline, ela poderá levar até 20 minutos para ser concluída. As configurações de computação padrão têm um tamanho de nó mínimo de 0, o que significa que o designer precisa alocar recursos depois de ficar ocioso. Execuções de pipeline repetidas levarão menos tempo, já que os recursos de computação já estão alocados. Além disso, o designer usa resultados armazenados em cache para cada módulo para melhorar ainda mais a eficiência.

### <a name="view-scored-labels"></a>Exibir os rótulos pontuados

Depois que a execução for concluída, você poderá exibir os resultados da execução do pipeline. Primeiro, examine as previsões geradas pelo modelo de regressão.

1. Clique com o botão direito do mouse no módulo **Modelo de Pontuação** e selecione **Visualizar** > **Conjunto de dados pontuado** para ver a saída dele.

    Aqui você poderá ver os preços previstos e os preços reais dos dados de teste.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/score-result.png" alt-text="Captura de tela da visualização de saída realçando a coluna Rótulo Pontuado":::

### <a name="evaluate-models"></a>Avaliar os modelos

Use **Avaliar Modelo** para ver como o desempenho do modelo treinado no conjunto de dados de teste.

1. Clique com o botão direito do mouse no módulo **Avaliar Modelo** e selecione **Visualizar** > **Resultados da avaliação** para ver a saída dele.

As seguintes estatísticas são mostradas para o modelo:

* **MAE (Média de Erros Absolutos)** : A média de erros absolutos. Um erro é a diferença entre o valor previsto e o valor real.
* **RMSE (Raiz Quadrada da Média de Erros Quadrados)** : a raiz quadrada da média de erros quadrados de previsões feitas no conjunto de dados de teste.
* **Erro absoluto relativo**: a média de erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
* **Erro ao quadrado relativo**: a média de erros quadrados relativos à diferença quadrada entre os valores reais e a média de todos os valores reais.
* **Coeficiente de determinação**: Também conhecida como o valor de R-quadrado, essa métrica estatística indica se o modelo se ajusta bem aos dados.

Para cada estatística de erro, menos é melhor. Um valor menor indica que as previsões estão mais próximas dos valores reais. Quanto ao coeficiente de determinação, quanto mais próximo o valor estiver de um (1), melhores serão as previsões.

## <a name="clean-up-resources"></a>Limpar os recursos

Ignore esta seção se desejar prosseguir com a parte 2 do tutorial, [implantar modelos](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Na segunda parte, você aprenderá a implantar seu modelo como um ponto de extremidade em tempo real.

> [!div class="nextstepaction"]
> [Continuar a implantação de modelos](tutorial-designer-automobile-price-deploy.md)
