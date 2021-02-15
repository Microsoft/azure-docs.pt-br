---
title: 'Tutorial: Criar o modelo preditivo usando ML automatizado (parte 1 de 2)'
titleSuffix: Azure Machine Learning
description: Saiba como criar e implantar modelos de machine learning automatizado, de modo que você possa usar o melhor modelo para prever resultados no Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 6dc99d58f15653e9d3f991622de3bb3388690459
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814798"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-automated-machine-learning-part-1-of-2"></a>Tutorial: Integração do Power BI – Criar o modelo preditivo usando machine learning automatizado (parte 1 de 2)

Na parte 1 deste tutorial, você treinará e implantará um modelo de machine learning preditivo. Use o ML (machine learning) automatizado no Estúdio do Azure Machine Learning.  Na segunda parte, você usará o modelo com o melhor desempenho para prever resultados no Microsoft Power BI.

Neste tutorial, você:

> [!div class="checklist"]
> * Criar um cluster de cálculo do Azure Machine Learning.
> * Criar um conjunto de dados.
> * Criar uma execução de machine learning automatizado.
> * Implantar o melhor modelo em um ponto de extremidade de pontuação em tempo real.


Há três maneiras de criar e implantar o modelo que você usará no Power BI.  Este artigo aborda a "Opção C: treinar e implantar modelos usando o machine learning automatizado no estúdio."  Essa opção é uma experiência de criação sem código. Ela automatiza totalmente a preparação de dados e o treinamento do modelo. 

Mas, em vez disso, você pode usar uma das outras opções:

* [Opção A: treinar e implantar modelos usando Jupyter Notebooks](tutorial-power-bi-custom-model.md). Essa experiência de criação que começa pela codificação usa Jupyter Notebooks hospedados no Estúdio do Azure Machine Learning.
* [Opção B: treinar e implantar modelos usando o designer do Azure Machine Learning](tutorial-power-bi-designer-model.md). Essa experiência de criação com pouco código usa uma interface do usuário do tipo "arrastar e soltar".

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você ainda não tiver uma assinatura, poderá usar uma [avaliação gratuita](https://aka.ms/AMLFree). 
- Um Workspace do Azure Machine Learning. Se você ainda não tiver um workspace, confira [Criar e gerenciar workspaces do Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-a-compute-cluster"></a>Criar um cluster de cálculo

O machine learning automatizado treina muitos modelos de machine learning para encontrar o "melhor" algoritmo e os "melhores" parâmetros. O Azure Machine Learning paraleliza a execução do treinamento de modelo em um cluster de cálculo.

Para começar, no [Estúdio do Azure Machine Learning](https://ml.azure.com), no menu à esquerda, selecione **Computação**. Abra a guia **Clusters de cálculo**. Em seguida, selecione **Novo**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Captura de tela mostrando como criar um cluster de cálculo.":::

Na página **Criar cluster de cálculo**:

1. Selecione um tamanho de VM. Para este tutorial, selecione um computador **Standard_D11_v2**.
1. Selecione **Avançar**.
1. Forneça um nome de computação válido.
1. Mantenha o **Número mínimo de nós** em `0`.
1. Altere o **Número máximo de nós** para `4`.
1. Selecione **Criar**.

O status do cluster é alterado para **Criando**.

>[!NOTE]
> O novo cluster tem 0 nó, portanto, não há custos de computação incorridos. Você incorre custos somente quando o trabalho de machine learning automatizado é executado. O cluster será escalado de novo para 0 automaticamente após 120 segundos de tempo ocioso.


## <a name="create-a-dataset"></a>Criar um conjunto de dados

Neste tutorial, você usará o [conjunto de dados de Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Esse conjunto de dados está disponível nos [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).

Para criar o conjunto de dados, no menu à esquerda, selecione **Conjuntos de dados**. Em seguida, selecione **Criar conjunto de dados**. Você verá as seguintes opções:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Captura de tela mostrando como criar um conjunto de dados.":::

Selecione **Do Open Datasets**. Em seguida, na página **Criar conjunto de dados do Open Datasets**:

1. Use a barra de pesquisa para localizar *diabetes*.
1. Selecione **Exemplo: Diabetes**.
1. Selecione **Avançar**.
1. Dê um nome ao conjunto de dados *diabetes*.
1. Selecione **Criar**.

Para explorar os dados, selecione o conjunto de dados e escolha **Explorar**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Captura de tela mostrando como explorar um conjunto de dados.":::

Os dados têm 10 variáveis de entrada de linha de base, como idade, sexo, índice de massa corpórea, pressão sanguínea média e seis medidas de soro sanguíneo. Eles também têm uma variável de destino, chamada **Y**. Essa variável de destino é uma medida quantitativa da progressão de diabetes um ano após a linha de base.

## <a name="create-an-automated-machine-learning-run"></a>Criar uma execução de machine learning automatizado

No [Estúdio do Azure Machine Learning](https://ml.azure.com), no menu à esquerda, selecione **ML Automatizado**. Depois, selecione **Nova execução de ML Automatizado**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Captura de tela mostrando como criar uma execução de machine learning automatizado.":::

Em seguida, selecione o conjunto de dados de **diabetes** criado anteriormente. Em seguida, selecione **Avançar**:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Captura de tela mostrando como selecionar um conjunto de dados.":::
 
Na página **Configurar execução**:

1. Em **Nome do experimento,** selecione **Criar**.
1. Nomeie o experimento.
1. No campo **Coluna de destino**, selecione **Y**.
1. No campo **Selecionar cluster de cálculo**, selecione o cluster de cálculo criado anteriormente. 

O formulário concluído será semelhante a:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Captura de tela mostrando como configurar o machine learning automatizado.":::

Por fim, selecione uma tarefa de machine learning. Nesse caso, a tarefa é **Regressão**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Captura de tela mostrando como configurar uma tarefa.":::

Selecione **Concluir**.

> [!IMPORTANT]
> O machine learning automatizado leva cerca de 30 minutos para concluir o treinamento de 100 modelos.

## <a name="deploy-the-best-model"></a>Implantar o melhor modelo

Quando o machine learning automatizado for concluído, você poderá ver os modelos de machine learning testados selecionando a guia **Modelos**. Os modelos são classificados por desempenho: o modelo com o melhor desempenho será mostrado primeiro. Quando você selecionar o melhor modelo, o botão **Implantar** será habilitado:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Captura de tela mostrando a lista de modelos.":::

Selecione **Implantar** para abrir uma janela **Implantar um modelo**:

1. Nomeie seu serviço de modelo como *modelo diabetes*.
1. Selecione **Serviço de Contêiner do Azure**.
1. Selecione **Implantar**.

Você verá uma mensagem informando que o modelo foi implantado com êxito.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu como treinar e implantar um modelo de machine learning usando o machine learning automatizado. No próximo tutorial, você aprenderá a consumir (pontuar) esse modelo no Power BI.

> [!div class="nextstepaction"]
> [Tutorial: Consumir um modelo no Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
