---
title: Crie seu primeiro experimento de Machine Learning automatizado
titleSuffix: Azure Machine Learning service
description: Saiba como treinar e implantar um modelo de classificação com o aprendizado de máquina automatizado no portal do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 07/23/2019
ms.openlocfilehash: 7c7d90d4ca1625edecc9d84e1ff7beec50032884
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444754"
---
# <a name="tutorial-train-and-deploy-a-classification-model-with-automated-machine-learning-in-the-azure-portal-preview"></a>Tutorial: Treinar e implantar um modelo de classificação com o Machine Learning automatizado na portal do Azure (versão prévia)

Neste tutorial, você aprenderá a criar seu primeiro experimento de aprendizado de máquina automatizado no portal do Azure. Este exemplo cria um modelo de classificação para prever se um cliente assinará ou não um depósito de termo com o banco. 

Usando os recursos automatizados de aprendizado de máquina do serviço e o portal do Azure, você inicia o processo de aprendizado de máquina automatizado e a seleção de algoritmo e o ajuste de hiperparâmetro acontecem para você. A técnica de aprendizado de máquina automatizada itera em várias combinações de algoritmos e hiperparâmetros até encontrar o melhor modelo com base no seu critério, tudo sem a necessidade de escrever uma única linha de código.

Neste tutorial, você aprende as seguintes tarefas:

> [!div class="checklist"]
> * Configurar um workspace do Serviço do Azure Machine Learning.
> * Crie um experimento.
> * Treinar automaticamente um modelo de classificação.
> * Exibir detalhes da execução de treinamento.
> * Implantar o modelo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* O arquivo de dados **bankmarketing_train. csv** . Faça download dela [aqui](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

## <a name="create-a-workspace"></a>Criar um espaço de trabalho

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>Criar uma experiência

1. Navegue até o painel esquerdo do seu espaço de trabalho. Selecione **Machine Learning automatizado** na seção **criação (visualização)** .

    ![Painel de navegação portal do Azure](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

    Como este é seu primeiro experimento com o Machine Learning automatizado, você verá a tela **Bem-vindo à Machine Learning automatizada** . 

1.  Selecione **criar experimento**. Em seguida, insira **My-1º-automl-experimento** como o nome do experimento.

1. Selecione **criar uma nova computação** e configure seu contexto de computação para este experimento.

    Campo| Valor
    ---|---
    Nome de computação| Insira um nome exclusivo que identifique o contexto de computação. Para este exemplo, usamos **automl-Compute**.
    Tamanho da máquina virtual| Selecione o tamanho da máquina virtual para sua computação. Usamos **Standard_DS12_V2**.
    Configurações adicionais| *Nó mínimo*: 1. Para habilitar a criação de perfil de dados, você deve ter um ou mais nós. <br> *Nó máximo*: 6. 

    Para iniciar a criação de sua nova computação, selecione **criar**. Isso pode levar alguns minutos. 

    Após a conclusão da criação, selecione a nova computação na lista suspensa e, em seguida, selecione **Avançar**.

1. Para este tutorial, usamos a conta de armazenamento padrão e o contêiner criado com sua nova computação. Isso é preenchido automaticamente no formulário.

1. Selecione **carregar** para escolher o arquivo **bankmarketing_train. csv** do computador local e carregá-lo no contêiner padrão. A visualização pública dá suporte apenas a carregamentos de arquivos locais e contas de armazenamento de BLOBs do Azure. Quando o upload for concluído, selecione o arquivo na lista. 

    [![Selecionar arquivo de dados](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. A guia **Visualização** nos permite configurar ainda mais nossos dados para esse experimento.

    Na guia Visualização, indique que os dados incluem cabeçalhos. O padrão do serviço é incluir todos os recursos (colunas) para treinamento. Para este exemplo, role para a direita e **ignore** o recurso **day_of_week** .

    ![Configuração da guia de visualização](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > A criação de perfil de dados não está disponível com computações que têm 0 nós no mínimo.

1. Selecione **classificação** como a tarefa de previsão.

1. Selecione **y** como a coluna de destino, a coluna na qual desejamos fazer previsões. Esta coluna indica se o cliente assinou um termo de depósito ou não.

1. Expanda as **Configurações avançadas** e preencha os campos da seguinte maneira.

    Configurações avançadas|Valor
    ------|------
    Métrica primária| AUC_weighted 
    Critérios de saída| Quando qualquer um desses critérios é atendido, o trabalho de treinamento termina antes da conclusão completa. <br> *Tempo de trabalho de treinamento (minutos)* : 5  <br> *Número máximo de iterações*: 10 
    Pré-processamento| Habilite o pré-processamento feito pelo aprendizado de máquina automatizado. Isso inclui a limpeza automática de dados, a preparação e a transformação para gerar recursos sintéticos.
    Validação| Selecione validação cruzada de K-dobra e 2 para o número de validações cruzadas. 
    Simultaneidade| Selecione 5 para o número máximo de iterações simultâneas.

   >[!NOTE]
   > Para este experimento, não definimos um limite de métrica ou de iterações máximas e não impedem que os algoritmos sejam testados.

1. Clique em **Iniciar** para executar o experimento.

   Depois que o experimento for iniciado, você verá uma tela de **detalhes de execução** em branco com o status a seguir na parte superior. O processo de preparação do experimento leva alguns minutos. Quando o processo de preparação for concluído, a mensagem de status será alterada para **execução**.

      ![Executar preparação](media/tutorial-1st-experiment-automated-ml/run-preparing.png)

##  <a name="view-experiment-details"></a>Exibir detalhes do experimento

À medida que o experimento progride, a tela de **detalhes da execução** atualiza o gráfico de iteração e a lista com as diferentes iterações (modelos) que são executadas. A lista de iterações é ordenada por Pontuação de métrica e, por padrão, o modelo que pontua o mais alto com base em nossa métrica **AUC_weighted** está na parte superior da lista.

>[!TIP]
> Os trabalhos de treinamento podem levar vários minutos para que cada pipeline termine a execução.

[![Painel de detalhes de execução](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-model"></a>Implantar modelo

Para esse experimento, o **VotingEnsemble** é considerado o melhor modelo com base na métrica **AUC_weighted** . Com o Machine Learning automatizado na portal do Azure, podemos implantar esse modelo como um serviço Web para prever novos dados com um clique. 

1. Na página **executar detalhe** , selecione o botão **implantar melhor modelo** .

1. Preencha o painel **implantar melhor modelo** da seguinte maneira,

    Campo| Valor
    ----|----
    Nome da implantação| meu-automl-implantar
    Descrição da implantação| Minha primeira implantação de experimento automatizada do Machine Learning
    Script de Pontuação| AutoGenerate
    Script de ambiente| AutoGenerate
    
1. Selecione **Implantar**. A implantação pode levar cerca de 20 minutos para ser concluída.

    A mensagem a seguir é exibida quando a implantação é concluída com êxito.

    ![Implantação concluída](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    É só isso! Você tem um serviço Web operacional para gerar previsões.

## <a name="clean-up-resources"></a>Limpar recursos

Os arquivos de implantação são maiores que os dados e os arquivos de teste, portanto, eles custam mais para serem armazenados. Exclua apenas os arquivos de implantação para minimizar os custos em sua conta e se você quiser manter o espaço de trabalho e os arquivos de teste. Caso contrário, exclua o grupo de recursos inteiro, se você não planeja usar nenhum dos arquivos.  

### <a name="delete-deployment-instance"></a>Excluir instância de implantação

Exclua apenas a instância de implantação do portal do Azure, se você quiser manter o grupo de recursos e o espaço de trabalho para outros tutoriais e explorações. 

1. Navegue até o painel **ativos** à esquerda e selecione implantações. 

1. Selecione a implantação que você deseja excluir e selecione **excluir**. 

1. Selecione **continuar**.

### <a name="delete-resource-group"></a>Excluir grupo de recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial de aprendizado de máquina automatizado, você usou o portal do Azure para criar e implantar um modelo de classificação. Consulte estes artigos para obter mais informações e próximas etapas.

+ Como [consumir um serviço Web](how-to-consume-web-service.md).
+ Saiba mais sobre o [pré-processamento](how-to-create-portal-experiments.md#preprocess).
+ Saiba mais sobre a [criação de perfil de dados](how-to-create-portal-experiments.md#profile).
+ Saiba mais sobre o [aprendizado de máquina automatizado](concept-automated-ml.md).

>[!NOTE]
> Esse conjunto de Cco de [marketing bancário é disponibilizado no Creative Commons Domínio público)](https://creativecommons.org/publicdomain/zero/1.0/). Todos os direitos no conteúdo individual do banco de dados são licenciados sob a [licença de conteúdo do banco de dados](https://creativecommons.org/publicdomain/zero/1.0/) e estão disponíveis em [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Esse conjunto de dados está originalmente disponível no banco de dado do [Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
>  Cite o seguinte trabalho: <br> [Moro et al., 2014] S. moro, P. Cortez e P. Rita. Uma abordagem controlada por dados para prever o sucesso do telemarketing bancário. Sistemas de suporte a decisões, Elsevier, 62:22-31, junho 2014.