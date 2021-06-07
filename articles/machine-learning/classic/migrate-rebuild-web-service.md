---
title: 'ML Studio (clássico): migrar para o serviço Web Azure Machine Learning-Rebuild'
description: Recompilar os serviços Web do estúdio (clássico) como pontos de extremidade do pipeline no Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 5e467d22cc3230bd9945fb276dc16cf1fa765bb6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564822"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>Recompilar um serviço Web Studio (clássico) no Azure Machine Learning

Neste artigo, você aprenderá a recriar um serviço Web Studio (clássico) como um **ponto de extremidade** no Azure Machine Learning.

Use Azure Machine Learning pontos de extremidade de pipeline para fazer previsões, treinar novamente modelos ou executar qualquer pipeline genérico. O ponto de extremidade REST permite executar pipelines de qualquer plataforma. 

Este artigo faz parte do estúdio (clássico) para Azure Machine Learning série de migração. Para obter mais informações sobre como migrar para o Azure Machine Learning, consulte o [artigo Visão geral da migração](migrate-overview.md).

> [!NOTE]
> Esta série de migração se concentra no designer do tipo "arrastar e soltar". Para obter mais informações sobre como implantar modelos programaticamente, consulte [implantar modelos de aprendizado de máquina no Azure](../how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Um Workspace do Azure Machine Learning. [Criar um workspace do Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Um pipeline de treinamento Azure Machine Learning. Para obter mais informações, consulte [Recompilar um experimento do estúdio (clássico) no Azure Machine Learning](migrate-rebuild-experiment.md).

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>Ponto de extremidade em tempo real vs. ponto de extremidade do pipeline

Os serviços Web do estúdio (clássico) foram substituídos por **pontos de extremidade** no Azure Machine Learning. Use a tabela a seguir para escolher o tipo de ponto de extremidade a ser usado:

|Serviço Web Studio (clássico)| Substituição de Azure Machine Learning
|---|---|
|Serviço Web de solicitação/resposta (previsão em tempo real)|Ponto de extremidade em tempo real|
|Serviço Web em lotes (previsão de lote)|Ponto de extremidade de pipeline|
|Retreinando o serviço Web (retreinando)|Ponto de extremidade de pipeline| 


## <a name="deploy-a-real-time-endpoint"></a>Implanta um ponto de extremidade em tempo real

No Studio (clássico), você usou um **serviço Web de solicitação/resposta** para implantar um modelo para previsões em tempo real. No Azure Machine Learning, você usa um **ponto de extremidade em tempo real**.

Há várias maneiras de implantar um modelo no Azure Machine Learning. Uma das maneiras mais simples é usar o designer para automatizar o processo de implantação. Use as etapas a seguir para implantar um modelo como um ponto de extremidade em tempo real:

1. Execute seu pipeline de treinamento concluído pelo menos uma vez.
1. Após a conclusão da execução, na parte superior da tela, selecione criar pipeline de **inferência**  >  **pipeline de inferência em tempo real**.

    ![criar pipeline de inferência em tempo real](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    O designer converte o pipeline de treinamento em um pipeline de inferência em tempo real. Uma conversão semelhante também ocorre no Studio (clássico).

    No designer, a etapa de conversão também [registra o modelo treinado em seu espaço de trabalho Azure Machine Learning](../how-to-deploy-and-where.md#registermodel).

1. Selecione **Enviar** para executar o pipeline de inferência em tempo real e verifique se ele é executado com êxito.

1. Depois de verificar o pipeline de inferência, selecione **implantar**.

1. Insira um nome para o ponto de extremidade e um tipo de computação.

    A tabela a seguir descreve as opções de computação de implantação no designer:

    | Destino de computação | Usado para | Descrição | Criação |
    | ----- |  ----- | ----- | -----  |
    |[AKS (Serviço de Kubernetes do Azure)](../how-to-deploy-azure-kubernetes-service.md) |Inferência em tempo real|Implantações de produção em grande escala. Tempo de resposta rápido e dimensionamento automático de serviço.| Criado pelo usuário. Para obter mais informações, consulte [criar destinos de computação](../how-to-create-attach-compute-studio.md#inference-clusters). |
    |[Instâncias de contêiner do Azure ](../how-to-deploy-azure-container-instance.md)|Teste ou desenvolvimento | Cargas de trabalho baseadas em CPU de pequena escala que exigem menos de 48 GB de RAM.| Criado automaticamente pelo Azure Machine Learning.

### <a name="test-the-real-time-endpoint"></a>Testar o ponto de extremidade em tempo real

Após a conclusão da implantação, você poderá ver mais detalhes e testar seu ponto de extremidade:

1. Vá para a guia **pontos de extremidade** .
1. Selecione o ponto de extremidade.
1. Selecione a guia **Testar**.
    
    ![Captura de tela mostrando a guia pontos de extremidade com o botão testar ponto de extremidades](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>Publicar um ponto de extremidade de pipeline para previsão de lote ou retreinamento

Você também pode usar seu pipeline de treinamento para criar um **ponto de extremidade de pipeline** em vez de um ponto de extremidade em tempo real. Use **pontos de extremidade de pipeline** para executar a previsão ou o retreinamento em lotes.

Pontos de extremidade de pipeline substituem pontos de **extremidade de execução em lote**  do estúdio (clássico) e **retreinando serviços Web**.

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>Publicar um ponto de extremidade de pipeline para previsão de lote

A publicação de um ponto de extremidade de previsão do lote é semelhante ao ponto de extremidade em tempo real.

Use as etapas a seguir para publicar um ponto de extremidade de pipeline para previsão de lote:

1. Execute seu pipeline de treinamento concluído pelo menos uma vez.

1. Depois de concluir a execução, na parte superior da tela, selecione criar pipeline de **inferência**  >  **pipeline de inferência de lote**.

    ![Captura de tela mostrando o botão criar pipeline de inferência em um pipeline de treinamento](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    O designer converte o pipeline de treinamento em um pipeline de inferência de lote. Uma conversão semelhante também ocorre no Studio (clássico).

    No designer, essa etapa também [registra o modelo treinado em seu espaço de trabalho Azure Machine Learning](../how-to-deploy-and-where.md#registermodel).

1. Selecione **Enviar** para executar o pipeline de inferência de lote e verificar se ele foi concluído com êxito.

1. Depois de verificar o pipeline de inferência, selecione **publicar**.
 
1. Crie um novo ponto de extremidade de pipeline ou selecione um existente.
    
    Um novo ponto de extremidade de pipeline cria um novo ponto de extremidade REST para seu pipeline. 

    Se você selecionar um ponto de extremidade de pipeline existente, não substituirá o pipeline existente. Em vez disso, Azure Machine Learning versões de cada pipeline no ponto de extremidade. Você pode especificar qual versão deve ser executada em sua chamada REST. Você também deve definir um pipeline padrão se a chamada REST não especificar uma versão.


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>Publicar um ponto de extremidade de pipeline para novo treinamento

Para publicar um ponto de extremidade de pipeline para novo treinamento, você já deve ter um rascunho de pipeline que treina um modelo. Para obter mais informações sobre como criar um pipeline de treinamento, consulte [Recompilar um experimento do estúdio (clássico)](migrate-rebuild-experiment.md).

Para reutilizar o ponto de extremidade de pipeline para readaptação, você deve criar um **parâmetro de pipeline** para seu conjunto de dados de entrada. Isso permite que você defina dinamicamente seu conjunto de seus conjuntos de seu treinamento, para que você possa treinar novamente seu modelo.

Use as seguintes etapas para publicar o ponto de extremidade do pipeline de novo treinamento:

1. Execute seu pipeline de treinamento pelo menos uma vez.
1. Após a conclusão da execução, selecione o módulo DataSet.
1. No painel detalhes do módulo, selecione **definir como parâmetro de pipeline**.
1. Forneça um nome descritivo como "InputDataset".    

    ![Captura de tela destacando como criar um parâmetro de pipeline](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    Isso cria um parâmetro de pipeline para seu conjunto de dados de entrada. Ao chamar o ponto de extremidade de pipeline para treinamento, você pode especificar um novo conjunto de um para treinar novamente o modelo.

1. Selecione **Publicar**.

    ![Captura de tela destacando o botão publicar em um pipeline de treinamento](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>Chamar o ponto de extremidade do pipeline no estúdio

Depois de criar sua inferência de lote ou retreinar o ponto de extremidade de pipeline, você pode chamar seu ponto de extremidade diretamente do seu navegador.

1. Vá para a guia **pipelines** e selecione **pontos de extremidade de pipeline**.
1. Selecione o ponto de extremidade do pipeline que você deseja executar.
1. Selecione **Enviar**.

    Você pode especificar qualquer parâmetro de pipeline depois de selecionar **Enviar**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a recriar um serviço Web Studio (clássico) no Azure Machine Learning. A próxima etapa é [integrar seu serviço Web com aplicativos cliente](migrate-rebuild-integrate-with-client-app.md).


Consulte os outros artigos da série de migração do Studio (clássico):

1. [Visão geral da migração](migrate-overview.md).
1. [Migrar conjunto](migrate-register-dataset.md)de um.
1. [Recompile um pipeline de treinamento do Studio (clássico)](migrate-rebuild-experiment.md).
1. **Recompile um serviço Web Studio (clássico)**.
1. [Integre um serviço web Azure Machine Learning com aplicativos cliente](migrate-rebuild-integrate-with-client-app.md).
1. [Migrar executar script R](migrate-execute-r-script.md).
