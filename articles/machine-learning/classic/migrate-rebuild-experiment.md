---
title: 'ML Studio (clássico): migrar para o experimento de recompilação Azure Machine Learning'
description: Recompilar experimentos do estúdio (clássico) no designer de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bb944cb034fdd7cc51648314154a654bc1265533
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564802"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>Recompile um experimento do estúdio (clássico) no Azure Machine Learning

Neste artigo, você aprenderá a recriar um experimento de estúdio (clássico) no Azure Machine Learning. Para obter mais informações sobre como migrar do Studio (clássico), consulte [o artigo Visão geral da migração](migrate-overview.md).

Os **experimentos** de estúdio (clássicos) são semelhantes aos **pipelines** no Azure Machine Learning. No entanto, no Azure Machine Learning pipelines são criados no mesmo back-end que alimenta o SDK. Isso significa que você tem duas opções para o desenvolvimento de Machine Learning: os SDKs do designer do tipo "arrastar e soltar" ou do Code-First.

Para obter mais informações sobre como criar pipelines com o SDK, consulte [o que são pipelines de Azure Machine Learning](../concept-ml-pipelines.md#building-pipelines-with-the-python-sdk).


## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Um Workspace do Azure Machine Learning. [Criar um workspace do Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Um experimento de estúdio (clássico) para migrar.
- [Carregue seu conjunto de seus conjuntos](migrate-register-dataset.md) de Azure Machine Learning.

## <a name="rebuild-the-pipeline"></a>Recompilar o pipeline

Depois de [migrar o conjunto de seus para Azure Machine Learning](migrate-register-dataset.md), você estará pronto para recriar seu experimento.

No Azure Machine Learning, o grafo Visual é chamado de **rascunho de pipeline**. Nesta seção, você recriará seu experimento clássico como um rascunho de pipeline.

1. Ir para o Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com))
1. No painel de navegação esquerdo, selecione o **Designer** > captura de tela de **módulos predefinidos de fácil utilização** ![ mostrando como criar um novo rascunho de pipeline.](../media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Recompile manualmente o experimento com os módulos do designer.
    
    Consulte a [tabela de mapeamento de módulo](migrate-overview.md#studio-classic-and-designer-module-mapping) para localizar os módulos de substituição. Muitos dos módulos mais populares do estúdio (clássico) têm versões idênticas no designer.

    > [!Important]
    > Se seu experimento usar o módulo executar script R, você precisará executar etapas adicionais para migrar seu experimento. Para obter mais informações, consulte [migrar módulos de script R](migrate-execute-r-script.md).

1. Ajustar parâmetros.
    
    Selecione cada módulo e ajuste os parâmetros no painel configurações do módulo para a direita. Use os parâmetros para recriar a funcionalidade do experimento do seu estúdio (clássico). Para obter mais informações sobre cada módulo, consulte a [referência do módulo](../algorithm-module-reference/module-reference.md).

## <a name="submit-a-run-and-check-results"></a>Enviar uma execução e verificar os resultados

Depois de recriar seu experimento do estúdio (clássico), é hora de enviar uma **execução de pipeline**.

Uma execução de pipeline é executada em um **destino de computação** anexado ao seu espaço de trabalho. Você pode definir um destino de computação padrão para o pipeline inteiro ou pode especificar destinos de computação por módulo.

Depois de enviar uma execução de um rascunho de pipeline, ele se transforma em uma **execução de pipeline**. Cada execução de pipeline é registrada e registrada Azure Machine Learning.

Para definir um destino de computação padrão para o pipeline inteiro:
1. Selecione o **ícone de engrenagem** ![ ícone de engrenagem no designer ](../media/tutorial-designer-automobile-price-train-score/gear-icon.png) ao lado do nome do pipeline.
1. Selecione **Selecionar destino de computação**.
1. Selecione uma computação existente ou crie uma nova computação seguindo as instruções na tela.

Agora que seu destino de computação está definido, você pode enviar uma execução de pipeline:

1. Na parte superior da tela, selecione **Enviar**.
1. Selecione **criar novo** para criar um novo experimento.
    
    Os experimentos organizam as execuções de pipeline semelhantes juntas. Se executar um pipeline várias vezes, você poderá selecionar o mesmo experimento para execuções sucessivas. Isso é útil para registro em log e acompanhamento.
1. Insira um nome de experimento. Em seguida, selecione **enviar**.

    A primeira execução pode levar até 20 minutos. Como as configurações de computação padrão têm um tamanho de nó mínimo de 0, o designer deve alocar recursos depois de ficar ocioso. As execuções sucessivas levam menos tempo, já que os nós já estão alocados. Para acelerar o tempo de execução, você pode criar um recurso de computação com um tamanho de nó mínimo de 1 ou maior.

Após a conclusão da execução, você pode verificar os resultados de cada módulo:

1. Clique com o botão direito do mouse no módulo cuja saída você deseja ver.
1. Selecione **Visualizar**, **Exibir saída** ou **Exibir log**.

    - **Visualizar**: visualize o conjunto de resultados de Results.
    - **Exibir saída**: Abra um link para o local de armazenamento de saída. Use isso para explorar ou baixar a saída. 
    - **Exibir log**: exibir o driver e os logs do sistema. Use a **70_driver_log** para ver informações relacionadas ao script enviado pelo usuário, como erros e exceções.

> [!IMPORTANT]
> Os módulos do designer usam pacotes python de software livre, em comparação com pacotes C# no estúdio (clássico). Como resultado, a saída do módulo pode variar um pouco entre o designer e o Studio (clássico). 


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a recriar um experimento de estúdio (clássico) no Azure Machine Learning. A próxima etapa é [recriar serviços Web no Azure Machine Learning](migrate-rebuild-web-service.md).


Consulte os outros artigos da série de migração do Studio (clássico):

1. [Visão geral da migração](migrate-overview.md).
1. [Migrar conjunto](migrate-register-dataset.md)de um.
1. **Recompile um pipeline de treinamento do Studio (clássico)**.
1. [Recompile um serviço Web Studio (clássico)](migrate-rebuild-web-service.md).
1. [Integre um serviço web Azure Machine Learning com aplicativos cliente](migrate-rebuild-integrate-with-client-app.md).
1. [Migrar executar script R](migrate-execute-r-script.md).
