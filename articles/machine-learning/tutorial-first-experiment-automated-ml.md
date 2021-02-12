---
title: Criar modelos de classificação de ML automatizado
titleSuffix: Azure Machine Learning
description: Saiba como treinar e implantar modelos de classificação com a interface de ML automatizado (machine learning automatizado) do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 12/21/2020
ms.custom: automl
ms.openlocfilehash: ff9bd328dd98fbd614a3bb63a1edddc2027d97b2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879774"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Tutorial: Criar um modelo de classificação com o ML automatizado no Azure Machine Learning


Neste tutorial, você aprenderá a criar um modelo de classificação simples sem escrever nenhuma linha de código usando o machine learning automatizado no Azure Machine Learning Studio. Este modelo de classificação prevê se um cliente assinará um depósito a prazo fixo com uma instituição financeira.

Com o aprendizado de máquina automatizado, você pode automatizar tarefas intensivas e demoradas. O aprendizado de máquina automatizado itera rapidamente em muitas combinações de algoritmos e hiperparâmetros para ajudar você a encontrar o melhor modelo com base em uma métrica de sucesso de sua escolha.

Para ver um exemplo de previsão de série temporal, confira [Tutorial: Previsão de demanda e AutoML](tutorial-automated-ml-forecast.md).

Neste tutorial, você aprenderá a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Criar um Workspace de Azure Machine Learning.
> * Executar um experimento de aprendizado de máquina automatizado.
> * Exibir detalhes do experimento.
> * Implantar o modelo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).

* Baixe o arquivo de dados [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv). A coluna **y** indica se um cliente assinou um depósito a prazo fixo, que é posteriormente identificado como a coluna de destino para as previsões neste tutorial. 

## <a name="create-a-workspace"></a>Criar um workspace

Um Workspace do Azure Machine Learning é o recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de machine learning. Ele vincula sua assinatura do Azure e o grupo de recursos a um objeto facilmente consumido no serviço. 

Há muitas [maneiras de criar um workspace](how-to-manage-workspace.md). Neste tutorial, você cria um workspace pelo portal do Azure, um console baseado na Web para gerenciar os seus recursos do Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Anote seu **workspace** e sua **assinatura**. Você precisará dessas informações para criar o experimento no local certo. 

## <a name="get-started-in-azure-machine-learning-studio"></a>Introdução ao Azure Machine Learning Studio

Conclua a configuração do experimento a seguir e execute as etapas por meio do estúdio do Azure Machine Learning em https://ml.azure.com, uma interface da Web consolidada que inclui ferramentas de machine learning para realizar cenários de ciência de dados para praticantes de ciência de dados de todos os níveis de habilidade. O estúdio não é compatível com navegadores Internet Explorer.

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com).

1. Selecione a assinatura e o workspace criado.

1. Selecione **Introdução**.

1. No painel esquerdo, selecione **ML Automatizado** na seção **Criar**.

   Como este é seu primeiro experimento de ML automatizado, você verá uma lista vazia e links para a documentação.

   ![Página Introdução](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Selecione **+Nova execução de ML automatizado**. 

## <a name="create-and-load-dataset"></a>Criar e carregar um conjunto de dados

Antes de configurar seu experimento, carregue o arquivo de dados no workspace na forma de um conjunto de dados do Azure Machine Learning. Essa ação permite que você garanta que os dados estejam formatados corretamente para o experimento.

1. Crie um novo conjunto de dados selecionando **De arquivos locais** na lista suspensa **+ Criar conjunto de dados**. 

    1. No formulário **Informações básicas**, dê um nome ao conjunto de dados e forneça uma descrição opcional. No momento, a interface de ML automatizado só dá suporte a TabularDatasets; portanto, o padrão do tipo de conjunto de dados deve ser *Tabular*.

    1. Selecione **Avançar** na parte inferior esquerda

    1. No formulário **Seleção de armazenamento de dados e de arquivo**, selecione o armazenamento de dados padrão que foi configurado automaticamente durante a criação do workspace, **workspaceblobstore (Armazenamento de Blobs do Azure)** . É nele que você carregará seu arquivo de dados para disponibilizá-lo ao workspace.

    1. Selecione **Procurar**.
    
    1. Escolha o arquivo **bankmarketing_train.csv** no computador local. Esse é o arquivo que você baixou como [pré-requisito](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Dê um nome exclusivo ao conjunto de dados e forneça uma descrição opcional. 

    1. Selecione **Avançar** no canto inferior esquerdo para fazer upload dele no contêiner padrão que foi automaticamente configurado durante a criação do workspace.  
    
       Após a conclusão do upload, o formulário Configurações e visualização será pré-populado com base no tipo de arquivo. 
       
    1. Verifique se o formulário **Configurações e visualização** é populado conforme mostrado a seguir e selecione **Avançar**.
        
        Campo|Descrição| Valor para o tutorial
        ---|---|---
        Formato de arquivo|Define o layout e o tipo de dados armazenados em um arquivo.| Delimitado
        Delimitador|Um ou mais caracteres para especificar o limite entre regiões&nbsp; separadas e independentes em texto sem formatação ou outros fluxos de dados. |Vírgula
        Codificação|Identifica qual tabela de esquema de bit para caractere usar para ler seu conjunto de dados.| UTF-8
        Cabeçalhos da coluna| Indica como os cabeçalhos do conjunto de dados, se houver, serão tratados.| Todos os arquivos têm os mesmos cabeçalhos
        Ignorar linhas | Indica quantas linhas, se houver, serão ignoradas no conjunto de registros.| Nenhum

    1. O formulário **Esquema** permite configurar ainda mais os dados do experimento. Neste exemplo, não fazemos nenhuma seleção. Selecione **Avançar**.

    1. No formulário **Confirmar detalhes**, verifique se as informações correspondem ao que foi previamente preenchido nos formulários **Informações básicas, Seleção de armazenamento de dados e arquivo** e **Configurações e visualização**.
    
    1. Selecione **Criar** para concluir a criação do conjunto de dados.
    
    1. Selecione seu conjunto de dados quando ele aparecer na lista.
    
    1. Examine a **Visualização de dados** para garantir que você não incluiu **day_of_week** e selecione **Fechar**.

    1. Selecione **Avançar**.

## <a name="configure-run"></a>Configurar a execução

Depois de carregar e configurar seus dados, você poderá configurar seu experimento. Essa configuração inclui tarefas de design de experimento, como selecionar o tamanho do seu ambiente de computação e especificar qual coluna você deseja prever. 

1. Selecione o botão de opção **Criar**.

1. Preencha o formulário **Configurar Execução** da seguinte maneira:
    1. Insira este nome de experimento: `my-1st-automl-experiment`

    1. Selecione **y** como a coluna de destino, o que você quer prever. Essa coluna indica se o cliente assinou um depósito a prazo ou não.
    
    1. Selecione **+Criar uma computação** e configure o destino de computação. Um destino de computação é um ambiente de recursos local ou baseado em nuvem usado para executar o script de treinamento ou hospedar a implantação do serviço. Para este experimento, usamos uma computação baseada em nuvem. 
        1. Popule o formulário **Máquina Virtual** para configurar sua computação.

            Campo | Descrição | Valor para o tutorial
            ----|---|---
            Prioridade da&nbsp;máquina&nbsp;virtual |Selecione a prioridade que o experimento deve ter| Dedicado
            Tipo de&nbsp;máquina&nbsp;virtual| Selecione o tipo da máquina virtual da computação.|CPU (Unidade de Processamento Central)
            Tamanho&nbsp;da&nbsp;máquina virtual| Selecione o tamanho da máquina virtual da computação. É fornecida uma lista de tamanhos recomendados com base em seus dados e no tipo de experimento. |Standard_DS12_V2
        
        1. Selecione **Avançar** para preencher o **Formulário Definir configurações**.
        
            Campo | Descrição | Valor para o tutorial
            ----|---|---
            Nome da computação |  Um nome exclusivo que identifique o contexto de computação. | automl-compute
            Mín./máx. de nós| Para analisar os dados, é necessário especificar um ou mais nós.|Número mín. de nós: 1<br>Número máx. de nós: 6
            Segundos de espera antes de reduzir verticalmente | Tempo de espera antes de o cluster ser automaticamente reduzido verticalmente para a contagem do mínimo de nós.|120 (padrão)
            Configurações avançadas | Definições para configurar e autorizar uma rede virtual para seu experimento.| Nenhum               

        1. Selecione **Criar** para criar o destino de computação. 

            **Isso levará alguns minutos para ser concluído.** 

             ![Página de configurações](./media/tutorial-first-experiment-automated-ml/compute-settings.png)

        1. Após a criação, selecione o novo destino de computação na lista suspensa.

    1. Selecione **Avançar**.

1. No formulário **Tipo de tarefa e configurações**, conclua a configuração do experimento de ML automatizado especificando o tipo de tarefa de machine learning e as definições de configuração.
    
    1.  Selecione **Classificação** como o tipo de tarefa de machine learning.

    1. Selecione **Exibir definições de configuração adicionais** e preencha os campos da seguinte maneira. Essas configurações destinam-se a controlar melhor o trabalho de treinamento. Caso contrário, os padrões são aplicados com base na seleção e nos dados de experimento.

        Configurações&nbsp;adicionais|Descrição|Valor&nbsp;para o&nbsp;tutorial
        ------|---------|---
        Métrica principal| Métrica de avaliação pela qual o algoritmo de aprendizado de máquina será medido.|AUC_weighted
        Explicar o melhor modelo| Mostra automaticamente a explicabilidade no melhor modelo criado pelo ML automatizado.| Habilitar
        Algoritmos bloqueados | Algoritmos que você deseja excluir do trabalho de treinamento| Nenhum
        Critério de saída| Se um critério for atendido, o trabalho de treinamento será interrompido. |Hora&nbsp;do&nbsp;trabalho de treinamento (horas): 1 <br> Limite de&nbsp;pontuação da&nbsp;métrica: Nenhum
        Validação | Escolha um tipo de validação cruzada e um número de testes.|Tipo de validação:<br>validação cruzada&nbsp;k-fold&nbsp; <br> <br> Número de validações: 2
        Simultaneidade| O número máximo de iterações paralelas executadas por iteração| Máximo de&nbsp;iterações&nbsp;simultâneas: 5
        
        Clique em **Salvar**.
    
    1. Selecione **Exibir configurações de definição de recursos**. Neste exemplo, selecione o botão de alternância do recurso **day_of_week** para não o incluir na definição de recursos neste experimento.

        ![Seleção da definição de recursos](./media/tutorial-first-experiment-automated-ml/featurization-setting-config.gif)   
 
        Clique em **Salvar**.

1. Selecione **Concluir** para executar o experimento. A tela **Detalhes da Execução** é aberta com o **Status da execução** na parte superior à medida que a preparação do experimento é iniciada. Esse status é atualizado conforme o progresso do experimento. Também aparecem notificações no canto superior direito do estúdio para informar você sobre o status do experimento.

>[!IMPORTANT]
> A preparação leva de **10 a 15 minutos** para preparar a execução do experimento.
> Durante a execução, são necessários **mais 2 a 3 minutos para cada iteração**.  <br> <br>
> Em produção, provavelmente, isso demorará mais. Mas para este tutorial, sugerimos que você comece a explorar os algoritmos testados na guia **Modelos** conforme eles são concluídos, enquanto os outros ainda estão em execução. 

##  <a name="explore-models"></a>Explorar modelos

Navegue até a guia **Modelos** para ver os algoritmos (modelos) testados. Por padrão, os modelos são ordenados pela pontuação da métrica à medida que são concluídos. Por este tutorial, o modelo com a pontuação mais alta de acordo com a métrica **AUC_weighted** escolhida é exibida no início da lista.

Enquanto você aguarda a conclusão de todos os modelos de experimento, selecione o **Nome do algoritmo** de um modelo concluído para explorar seus detalhes de desempenho. 

O exemplo a seguir navega pelas guias **Detalhes** e **Métricas** para exibir as propriedades, as métricas e os gráficos de desempenho do modelo selecionado. 

![Detalhes da iteração de execução](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-best-model"></a>Implantar o melhor modelo

A interface de machine learning automatizado permite que você implante o melhor modelo como um serviço Web em algumas etapas. A implantação é a integração do modelo para que ele possa prever novos dados e identificar possíveis áreas de oportunidade. 

Para este experimento, a implantação em um serviço Web significa que a instituição financeira agora tem uma solução Web iterativa e escalonável para identificar clientes potenciais para depósito a prazo fixo. 

Verifique se a execução experimental foi concluída. Para fazer isso, volte para a página de execução pai selecionando **Execução 1** na parte superior da sua tela. Um status **Concluído** é mostrado na parte superior esquerda da tela. 

Quando a execução experimental for concluída, a página **Detalhes** será populada com uma seção **Resumo do melhor modelo**. Nesse contexto de experimento, **VotingEnsemble** é considerado o melhor modelo, com base na métrica **AUC_weighted**.  

Implantamos esse modelo, mas saiba que a implantação demora cerca de 20 minutos para ser concluída. O processo de implantação envolve várias etapas, incluindo o registro do modelo, a geração de recursos e a configuração deles para o serviço Web.

1. Selecione **VotingEnsemble** para abrir a página específica do modelo.

1. Selecione o botão **Implantar** na parte superior esquerda.

1. Preencha o painel **Implantar um Modelo** da seguinte maneira:

    Campo| Valor
    ----|----
    Nome da implantação| my-automl-deploy
    Descrição da implantação| Minha primeira implantação de experimento de aprendizado de máquina automatizado
    Tipo de computação | Selecionar ACI (Instância de Computação do Azure)
    Habilitar autenticação| Desabilite. 
    Usar implantações personalizadas| Desabilite. Permite que o arquivo de driver padrão (script de pontuação) e o arquivo de ambiente sejam gerados automaticamente. 
    
    Para este exemplo, usamos os padrões fornecidos no menu *Avançado*. 

1. Selecione **Implantar**.  

    Uma mensagem de sucesso verde aparece na parte superior da tela **Executar** e, no painel **Resumo de modelo**, uma mensagem de status é exibida em **Status de implantação**. Selecione **Atualizar** periodicamente para verificar o status da implantação.
    
Agora você tem um serviço Web operacional para gerar previsões. 

Vá para as [**Próximas Etapas**](#next-steps) para saber mais sobre como consumir seu novo serviço Web e testar as previsões usando o suporte interno do Power BI ao Azure Machine Learning.

## <a name="clean-up-resources"></a>Limpar os recursos

Os arquivos de implantação são maiores que os dados e os arquivos de teste, portanto, eles custam mais para serem armazenados. Exclua somente os arquivos de implantação para minimizar os custos de sua conta ou se você quiser manter o workspace e os arquivos do experimento. Caso contrário, se você não planeja usar nenhum dos arquivos, exclua o grupo de recursos inteiro.  

### <a name="delete-the-deployment-instance"></a>Excluir a instância de implantação

Exclua apenas a instância de implantação do Azure Machine Learning em https:\//ml.azure.com/ se desejar manter o grupo de recursos e o workspace para outros tutoriais e explorações. 

1. Acesse [Azure Machine Learning](https://ml.azure.com/). Navegue até o workspace e, no lado esquerdo, no painel **Ativos**, selecione **Pontos de extremidade**. 

1. Selecione a implantação que você deseja excluir e selecione **Excluir**. 

1. Selecione **Continuar**.

### <a name="delete-the-resource-group"></a>Exclua o grupo de recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial de machine learning automatizado, você usou a interface de ML automatizado do Azure Machine Learning para criar e implantar um modelo de classificação. Confira estes artigos para obter mais informações e ver as próximas etapas:

> [!div class="nextstepaction"]
> [Consumir um serviço Web](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ Saiba mais sobre o [aprendizado de máquina automatizado](concept-automated-ml.md).
+ Para saber mais sobre métricas e gráficos de classificação, confira o artigo [Noções básicas sobre os resultados de machine learning automatizado](how-to-understand-automated-ml.md).
+ Saiba mais sobre a [personalização](how-to-configure-auto-features.md#featurization).
+ Saiba mais sobre a [criação de perfil de dados](how-to-connect-data-ui.md#profile).


>[!NOTE]
> Esse conjunto de dados de marketing bancário é disponibilizado sob a [licença Creative Commons (CCO: Domínio Público)](https://creativecommons.org/publicdomain/zero/1.0/). Todos os direitos no conteúdo individual do banco de dados são licenciados sob a [Licença de Conteúdo do Banco de Dados](https://creativecommons.org/publicdomain/zero/1.0/) e estão disponíveis no [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Esse conjunto de dados estava originalmente disponível no [banco de dados de aprendizado de máquina da UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez and P. Rita. Uma abordagem controlada por dados para prever o sucesso do telemarketing bancário. Sistemas de suporte a decisões, Elsevier, 62:22-31, junho de 2014.