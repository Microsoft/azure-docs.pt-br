---
title: 'Tutorial: Implantar modelos de ML com o designer'
titleSuffix: Azure Machine Learning
description: Crie uma solução de análise preditiva no designer do Azure Machine Learning. Treine, pontue e implante um modelo de machine learning usando módulos do tipo "arrastar e soltar".
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 01/15/2021
ms.custom: designer
ms.openlocfilehash: 6bba5ad17cbb6f1ed72d06b37c6d6af9ebd26495
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246461"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer"></a>Tutorial: Implantar um modelo de machine learning com o designer


Você poderá implantar o modelo preditivo desenvolvido na [parte um do tutorial](tutorial-designer-automobile-price-train-score.md) para dar aos outros uma chance de usá-lo. Na primeira parte, você treinou seu modelo. Agora é hora de gerar novas previsões com base na entrada do usuário. Nesta parte do tutorial, você:

> [!div class="checklist"]
> * Criará um pipeline de inferência em tempo real.
> * Criará um cluster de inferência.
> * Implantará o ponto de extremidade em tempo real.
> * Testará o ponto de extremidade em tempo real.

## <a name="prerequisites"></a>Pré-requisitos

Conclua a [parte um do tutorial](tutorial-designer-automobile-price-train-score.md) para aprender a treinar e pontuar um modelo de machine learning na interface visual.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>Criar um pipeline de inferência em tempo real

Para implantar o pipeline, primeiro, converta o pipeline de treinamento em um pipeline de inferência em tempo real. Esse processo remove módulos de treinamento e adiciona entradas e saídas do serviço Web para processar as solicitações.

### <a name="create-a-real-time-inference-pipeline"></a>Criar um pipeline de inferência em tempo real

1. Acima da tela do pipeline, selecione **Criar pipeline de inferência** > **Pipeline de inferência em tempo real**.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png"alt-text="Captura de tela mostrando onde encontrar o botão criar pipeline":::

    Agora, seu pipeline deve ter esta aparência: 

   ![Captura de tela mostrando a configuração esperada do pipeline após sua preparação para implantação](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Quando você seleciona **Criar pipeline de inferência**, várias coisas acontecem:
    
    * O modelo treinado é armazenado como um módulo de **Conjunto de dados** na paleta de módulos. Você pode encontrá-lo em **Meus conjuntos de dados**.
    * Os módulos de treinamento como **Treinar Modelo** e **Dividir Dados** são removidos.
    * O modelo treinado salvo é adicionado de volta ao pipeline.
    * Os módulos **Entrada de Serviço Web** e **Saída de Serviço Web** são adicionados. Esses módulos mostram o local em que os dados do usuário entram no pipeline e o local em que são retornados.

    > [!NOTE]
    > Por padrão, a **Entrada do Serviço Web** esperará o mesmo esquema de dados dos dados de treinamento usados para criar o pipeline preditivo. Nesse cenário, o preço é incluído no esquema. No entanto, o preço não é usado como um fator durante a previsão.
    >

1. Selecione **Enviar** e use o mesmo destino de computação e experimento usados na primeira parte.

    Se essa for a primeira execução do pipeline, ela poderá levar até 20 minutos para ser concluída. As configurações de computação padrão têm um tamanho de nó mínimo de 0, o que significa que o designer precisa alocar recursos depois de ficar ocioso. Execuções de pipeline repetidas levarão menos tempo, já que os recursos de computação já estão alocados. Além disso, o designer usa resultados armazenados em cache para cada módulo para melhorar ainda mais a eficiência.

1. Selecione **Implantar**.

## <a name="create-an-inferencing-cluster"></a>Cria um cluster inferência

Na caixa de diálogo exibida, selecione uma opção entre os clusters existentes do AKS (Serviço de Kubernetes do Azure) no qual o modelo será implantado. Se não tiver um cluster do AKS, use as etapas a seguir para criar um.

1. Selecione **Computação** na caixa de diálogo exibida para acessar a página **Computação**.

1. Na faixa de opções de navegação, selecione **Clusters de Inferência** >  **+ Novo**.

    ![Captura de tela mostrando como acessar o novo painel do cluster de inferência](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. No painel do cluster de inferência, configure um novo Serviço de Kubernetes.

1. Insira *aks-compute* para o **Nome de computação**.
    
1. Selecione uma região próxima que esteja disponível para a **Região**.

1. Selecione **Criar**.

    > [!NOTE]
    > Leva aproximadamente 15 minutos para criar um novo serviço do AKS. Verifique o estado de provisionamento na página **Clusters de Inferência**.
    >

## <a name="deploy-the-real-time-endpoint"></a>Implantar o ponto de extremidade em tempo real

Após o provisionamento do serviço do AKS, volte para o pipeline de inferência em tempo real para concluir a implantação.

1. Selecione **Implantar** acima da tela.

1. Selecione **Implantar novo ponto de extremidade em tempo real**. 

1. Selecione o cluster do AKS que você criou.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png"alt-text="Captura de tela mostrando como configurar um novo ponto de extremidade em tempo real":::

    Altere também a configuração **Avançada** do ponto de extremidade em tempo real.
    
    |Configuração avançada|Descrição|
    |---|---|
    |Habilitar o diagnóstico e a coleta de dados do Application Insights| Indica se o Azure Application Ingishts deve ser habilitado para coletar dados dos pontos de extremidade implantados. </br> Por padrão: falso |
    |Tempo limite de pontuação| Um tempo limite em milissegundos para impor as chamadas de pontuação ao serviço Web.</br>Por padrão: 60000|
    |Dimensionamento automático habilitado|   Habilitar o dimensionamento automático para o serviço Web.</br>Por padrão: verdadeiro|
    |Número mínimo de réplicas| O número mínimo de contêineres a serem usados no dimensionamento automático desse serviço Web.</br>Por padrão: 1|
    |Número máximo de réplicas| O número máximo de contêineres a serem usados no dimensionamento automático desse serviço Web.</br> Por padrão: 10|
    |Utilização de destino|A utilização de destino (em percentual) que o dimensionador automático deve tentar manter para esse serviço Web.</br> Por padrão: 70|
    |Período de atualização|A frequência (em segundos) com que o dimensionamento automático tenta escalar esse serviço Web.</br> Por padrão: 1|
    |Capacidade reserva de CPU|O número de núcleos de CPU a serem alocados para esse serviço Web.</br> Por padrão: 0,1|
    |Capacidade reserva de memória|A quantidade de memória (em GB) a ser alocada para esse serviço Web.</br> Por padrão: 0.5|
        

1. Selecione **Implantar**. 

    Uma notificação de êxito acima da tela é exibida após a conclusão da implantação. Isso pode levar alguns minutos.

> [!TIP]
> Implante também a **ACI** (Instância de Contêiner do Azure) se você selecionar **Instância de Contêiner do Azure** em **Tipo de computação** na caixa configuração de ponto de extremidade em tempo real.
> A Instância de Contêiner do Azure é usada para teste ou desenvolvimento. Use a ACI para cargas de trabalho baseadas em CPU de baixa escala que exigem menos de 48 GB de RAM.

## <a name="view-the-real-time-endpoint"></a>Ver o ponto de extremidade em tempo real

Após a conclusão da implantação, veja o ponto de extremidade em tempo real acessando a página **Pontos de extremidade**.

1. Na página **Pontos de extremidade**, selecione o ponto de extremidade implantado.

1. Na guia **Detalhes**, você pode ver mais informações, como o URI REST, o status e as marcas.

1. Na guia **Consumir**, você pode encontrar chaves de segurança e definir métodos de autenticação.

1. Na guia **Logs de implantação**, encontre os logs de implantação detalhados do ponto de extremidade em tempo real. 

Para obter mais informações sobre como consumir seu serviço Web, confira [Consumir um modelo implantado como um webservice](how-to-consume-web-service.md)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu as etapas principais na criação, na implantação e no consumo de um modelo de machine learning no designer. Para saber mais sobre como você pode usar o designer, confira os seguintes links:

+ [Amostras do designer](samples-designer.md): saiba como usar o designer para resolver outros tipos de problemas.
+ [Usar o estúdio do Azure Machine Learning em uma rede virtual do Azure](how-to-enable-studio-virtual-network.md).