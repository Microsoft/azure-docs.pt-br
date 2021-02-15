---
title: 'Tutorial: Usar a interface do usuário do tipo "arrastar e soltar" para criar o modelo preditivo (parte 1 de 2)'
titleSuffix: Azure Machine Learning
description: Saiba como criar e implantar um modelo de machine learning preditivo usando o designer. Posteriormente, você poderá usá-lo para prever resultados no Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.custom: designer
ms.openlocfilehash: 995979c7fe100637aa8e241489805fb09d6723f7
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814781"
---
# <a name="tutorial-power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Tutorial: Integração do Power BI – Usar a interface do usuário do tipo "arrastar e soltar" para criar o modelo preditivo (parte 1 de 2)

Na parte 1 deste tutorial, você treinará e implantará um modelo de machine learning preditivo usando o designer do Azure Machine Learning. O designer é uma interface do usuário do tipo "arrastar e soltar" com pouca codificação. Na parte 2, você usará o modelo para prever resultados no Microsoft Power BI.

Neste tutorial, você:

> [!div class="checklist"]
> * Criar uma instância de computação do Azure Machine Learning.
> * Criar um cluster de inferência do Azure Machine Learning.
> * Criar um conjunto de dados.
> * Treinar um modelo de regressão.
> * Implantar o modelo em um ponto de extremidade de pontuação em tempo real.


Há três maneiras de criar e implantar o modelo que você usará no Power BI.  Este artigo aborda a "Opção B: treinar e implantar modelos usando o designer".  Essa opção é uma experiência de criação com pouca codificação que usa a interface do designer.  

Mas, em vez disso, você pode usar uma das outras opções:

* [Opção A: treinar e implantar modelos usando Jupyter Notebooks](tutorial-power-bi-custom-model.md). Essa experiência de criação que começa pela codificação usa Jupyter Notebooks hospedados no Estúdio do Azure Machine Learning.
* [Opção C: treinar e implantar modelos usando o machine learning automatizado](tutorial-power-bi-automated-model.md). Essa experiência de criação sem codificação automatiza totalmente a preparação de dados e o treinamento do modelo.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você ainda não tiver uma assinatura, poderá usar uma [avaliação gratuita](https://aka.ms/AMLFree). 
- Um Workspace do Azure Machine Learning. Se você ainda não tiver um workspace, confira [Criar e gerenciar workspaces do Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Conhecimento introdutório de fluxos de trabalho de machine learning.


## <a name="create-compute-to-train-and-score"></a>Criar computação para treinar e pontuar

Nesta seção, você cria uma *instância de computação*. As instâncias de computação são usadas para treinar modelos de machine learning. Você também cria um *cluster de inferência* a fim de hospedar o modelo implantado para pontuação em tempo real.

Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com). No menu à esquerda, selecione **Computação** e depois **Nova**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Captura de tela mostrando como criar uma instância de computação.":::

Na página **Criar instância de computação**, selecione o tamanho da VM. Para este tutorial, selecione uma VM **Standard_D11_v2**. Em seguida, selecione **Avançar**. 

Na página **Configurações**, dê um nome à instância de computação. Em seguida, selecione **Criar**. 

>[!TIP]
> Você também pode usar a instância de computação para criar e executar notebooks.

Agora, o **Status** da instância de computação é **Criando**. O computador leva cerca de 4 minutos para provisionar. 

Enquanto aguarda, na página **Computação**, selecione a guia **Clusters de inferência**. Em seguida, selecione **Novo**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Captura de tela mostrando como criar um cluster de inferência.":::

Na página **Criar cluster de inferência**, selecione uma região e um tamanho de VM. Para este tutorial, selecione uma VM **Standard_D11_v2**. Em seguida, selecione **Avançar**. 

Na página **Definir Configurações**:

1. Forneça um nome de computação válido.
1. Selecione **Dev-test** como a finalidade do cluster. Essa opção cria um nó individual para hospedar o modelo implantado.
1. Selecione **Criar**.

Agora, o **Status** do cluster de inferência é **Criando**. O cluster de nó único leva cerca de 4 minutos para ser implantado.

## <a name="create-a-dataset"></a>Criar um conjunto de dados

Neste tutorial, você usará o [conjunto de dados de Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Esse conjunto de dados está disponível nos [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).

Para criar o conjunto de dados, no menu à esquerda, selecione **Conjuntos de dados**. Em seguida, selecione **Criar conjunto de dados**. Você verá as seguintes opções:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Captura de tela mostrando como criar um conjunto de dados.":::

Selecione **Do Open Datasets**. Na página **Criar conjunto de dados do Open Datasets**:

1. Use a barra de pesquisa para localizar *diabetes*.
1. Selecione **Exemplo: Diabetes**.
1. Selecione **Avançar**.
1. Dê um nome ao conjunto de dados *diabetes*.
1. Selecione **Criar**.

Para explorar os dados, selecione o conjunto de dados e escolha **Explorar**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Captura de tela mostrando como explorar um conjunto de dados.":::

Os dados têm 10 variáveis de entrada de linha de base, como idade, sexo, índice de massa corpórea, pressão sanguínea média e seis medidas de soro sanguíneo. Eles também têm uma variável de destino, chamada **Y**. Essa variável de destino é uma medida quantitativa da progressão de diabetes um ano após a linha de base.

## <a name="create-a-machine-learning-model-by-using-the-designer"></a>Criar um modelo de machine learning usando o designer

Depois de criar a computação e os conjuntos de dados, você pode usar o designer para criar o modelo de machine learning. No Estúdio do Azure Machine Learning, selecione **Designer**, depois **Novo pipeline**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Captura de tela mostrando como criar um pipeline.":::

Você verá uma *tela* em branco e o menu **Configurações**:

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Captura de tela mostrando como selecionar um destino de computação.":::

No menu **Configurações**, escolha **Selecionar destino de computação**. Selecione a instância de computação que você criou anteriormente e escolha **Salvar**. Altere o **Nome do rascunho** para algo mais fácil de memorizar, como *modelo de diabetes*. Por fim, insira uma descrição.

Na lista de ativos, expanda **Conjuntos de dados** e localize o conjunto de dados **diabetes**. Arraste este componente para a tela:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Captura de tela mostrando como arrastar um componente para a tela.":::

Depois, arraste os seguintes componentes para a tela:

1. **Regressão linear** (localizada em **Algoritmos de Machine Learning**)
1. **Modelo de treinamento** (localizado no **Modelo de treinamento**)

Na tela, observe os círculos na parte superior e inferior dos componentes. Esses círculos são portas.

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Captura de tela mostrando as portas em componentes não conectados.":::
 
Agora *conecte* os componentes uns aos outros. Selecione a porta na parte inferior do conjunto de dados **diabetes**. Arraste para a porta do lado superior direito do componente **Treinar modelo**. Selecione a porta na parte inferior do componente **Regressão linear**. Arraste para a porta do lado superior esquerdo do componente **Treinar modelo**.

Escolha a coluna do conjunto de dados a ser usada como a variável de rótulo (destino) para a previsão. Selecione o componente **Treinar modelo** e depois **Editar coluna**. 

Na caixa de diálogo, selecione **Insira o nome da coluna** > **Y**:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Captura de tela mostrando como selecionar uma coluna de rótulo.":::

Clique em **Salvar**. O *fluxo de trabalho* de machine learning terá a seguinte aparência:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Captura de tela mostrando os componentes conectados.":::

Selecione **Enviar**. Em **Experimento**, selecione **Criar**. Dê um nome ao experimento e selecione **Enviar**.

>[!NOTE]
> A primeira execução do experimento deve levar cerca de 5 minutos. Execuções subsequentes são muito mais rápidas porque o designer armazena em cache os componentes que foram executados para reduzir a latência.

Quando o experimento for concluído, você verá esta exibição:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Captura de tela mostrando a execução concluída.":::

Para inspecionar os logs de experimento, selecione **Treinar modelo** e depois **Saídas + logs**.

## <a name="deploy-the-model"></a>Implantar o modelo

Para implantar o modelo, na parte superior da tela, selecione **Criar pipeline de inferência** > **Pipeline de inferência em tempo real**:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Captura de tela mostrando onde selecionar um pipeline de inferência em tempo real.":::
 
O pipeline é condensado a apenas os componentes necessários para pontuar o modelo. Ao pontuar os dados, você não saberá os valores das variáveis de destino. Portanto, você poderá remover **Y** do conjunto de dados. 

Para remover **Y**, adicione um componente **Selecionar colunas no conjunto de dados** à tela. Conecte o componente de modo que o conjunto de dados de diabetes seja a entrada. Os resultados são a saída para o componente **Modelo de Pontuação**:

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Captura de tela mostrando como remover uma coluna.":::

Na tela, selecione o componente **Selecionar colunas no conjunto de dados** e depois **Editar colunas**. 

Na caixa de diálogo **Selecionar colunas**, escolha **Por nome**. Em seguida, verifique se todas as variáveis de entrada estão selecionadas e se a variável de destino *não* está selecionada:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Captura de tela mostrando como remover as configurações de coluna.":::

Clique em **Salvar**. 

Por fim, selecione o componente **Modelo de pontuação** e verifique se a caixa de seleção **Acrescentar colunas de pontuação à saída** está desmarcada. Para reduzir a latência, as previsões são enviadas de volta sem as entradas.

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Captura de tela mostrando as configurações do componente Modelo de pontuação.":::

Na parte superior da tela, selecione **Enviar**.

Depois de executar o pipeline de inferência com sucesso, você pode implantar o modelo no cluster de inferência. Selecione **Implantar**. 

Na caixa de diálogo **Configurar ponto de extremidade em tempo real**, selecione **Implantar novo ponto de extremidade em tempo real**. Dê ao ponto de extremidade o nome *my-diabetes-model*. Selecione a inferência que você criou anteriormente e escolha **Implantar**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Captura de tela mostrando as configurações do ponto de extremidade em tempo real.":::
## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu como treinar e implantar um modelo de designer. Na próxima parte, você aprenderá a consumir (pontuar) esse modelo no Power BI.

> [!div class="nextstepaction"]
> [Tutorial: Consumir um modelo no Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
