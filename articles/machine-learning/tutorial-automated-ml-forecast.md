---
title: 'Tutorial: Previsão de demanda e AutoML'
titleSuffix: Azure Machine Learning
description: Saiba como treinar e implantar um modelo de previsão de demanda com o machine learning automatizado no Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 12/21/2020
ms.custom: automl
ms.openlocfilehash: 2653161b5828d89858234a9ca98fe432e0eacb5c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879353"
---
# <a name="tutorial-forecast-demand-with-automated-machine-learning"></a>Tutorial: Prever demanda com machine learning automatizado


Neste tutorial, você usará machine learning automatizado, ou ML automatizado, no Azure Machine Learning Studio para criar um modelo de previsão de série temporal para prever a demanda de aluguel para um serviço de compartilhamento de bicicletas.

Para ver um exemplo de modelo de classificação, confira [Tutorial: Criar um modelo de classificação com o ML automatizado no Azure Machine Learning](tutorial-first-experiment-automated-ml.md).

Neste tutorial, você aprenderá a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Criar e carregar um conjunto de dados.
> * Configurar e executar um experimento de ML automatizado.
> * Especificar configurações de previsão.
> * Explorar os resultados do experimento.
> * Implantar o melhor modelo.

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md). 

* Baixe o arquivo de dados [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)

## <a name="get-started-in-azure-machine-learning-studio"></a>Introdução ao Azure Machine Learning Studio

Para este tutorial, você criará sua execução do experimento de ML automatizado no estúdio do Azure Machine Learning, uma interface Web consolidada que inclui ferramentas de machine learning para realizar cenários de ciência de dados destinado a praticantes de ciência de dados de todos os níveis de habilidades. O estúdio não é compatível com navegadores Internet Explorer.

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com).

1. Selecione a assinatura e o workspace criado.

1. Selecione **Introdução**.

1. No painel esquerdo, selecione **ML Automatizado** na seção **Criar**.

1. Selecione **+Nova execução de ML automatizado**. 

## <a name="create-and-load-dataset"></a>Criar e carregar um conjunto de dados

Antes de configurar seu experimento, carregue o arquivo de dados no workspace na forma de um conjunto de dados do Azure Machine Learning. Essa ação permite que você garanta que os dados estejam formatados corretamente para o experimento.

1. No formulário **Selecionar conjunto de dados**, selecione **De arquivos locais** na lista suspensa **+Criar conjunto de dados**. 

    1. No formulário **Informações básicas**, dê um nome ao conjunto de dados e forneça uma descrição opcional. O tipo de conjunto de dados deverá usar **Tabela** como padrão, pois o ML automatizado no Azure Machine Learning Studio atualmente só dá suporte a conjuntos de dados de tabela.
    
    1. Selecione **Avançar** na parte inferior esquerda

    1. No formulário **Seleção de armazenamento de dados e de arquivo**, selecione o armazenamento de dados padrão que foi configurado automaticamente durante a criação do workspace, **workspaceblobstore (Armazenamento de Blobs do Azure)** . Esse é o local de armazenamento em que você carregará o arquivo de dados. 

    1. Selecione **Procurar**. 
    
    1. Escolha o arquivo **bike-no.csv** no computador local. Esse é o arquivo que você baixou como [pré-requisito](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv).

    1. Selecione **Avançar**

       Após a conclusão do upload, o formulário Configurações e visualização será pré-populado com base no tipo de arquivo. 
       
    1. Verifique se o formulário **Configurações e visualização** é populado conforme mostrado a seguir e selecione **Avançar**.
        
        Campo|Descrição| Valor para o tutorial
        ---|---|---
        Formato de arquivo|Define o layout e o tipo de dados armazenados em um arquivo.| Delimitado
        Delimitador|Um ou mais caracteres para especificar o limite entre regiões&nbsp; separadas e independentes em texto sem formatação ou outros fluxos de dados. |Vírgula
        Codificação|Identifica qual tabela de esquema de bit para caractere usar para ler seu conjunto de dados.| UTF-8
        Cabeçalhos da coluna| Indica como os cabeçalhos do conjunto de dados, se houver, serão tratados.| Usar os cabeçalhos do primeiro arquivo
        Ignorar linhas | Indica quantas linhas, se houver, serão ignoradas no conjunto de registros.| Nenhum

    1. O formulário **Esquema** permite configurar ainda mais os dados do experimento. 
    
        1. Para este exemplo, opte por ignorar as colunas **casual** e **registrada**. Essas colunas são uma divisão da coluna **cnt** e, portanto, não as incluímos.

        1. Além disso, para este exemplo, mantenha os padrões das **Propriedades** e do **Tipo**. 
        
        1. Selecione **Avançar**.

    1. No formulário **Confirmar detalhes**, verifique se as informações correspondem ao que foi previamente preenchido nos formulários **Informações básicas** e **Configurações e visualização**.

    1. Selecione **Criar** para concluir a criação do conjunto de dados.

    1. Selecione seu conjunto de dados quando ele aparecer na lista.

    1. Selecione **Avançar**.

## <a name="configure-run"></a>Configurar a execução

Depois de carregar e configurar seus dados, configure o destino de computação remota e selecione qual coluna nos dados você deseja prever.

1. Preencha o formulário **Configurar execução** da seguinte maneira:
    1. Insira um nome de experimento: `automl-bikeshare`

    1. Selecione **cnt** como a coluna de destino, o que você deseja prever. Essa coluna indica o número total de aluguéis de compartilhamento de bicicletas.

    1. Selecione **Criar uma computação** e configure o destino de computação. O ML automatizado só dá suporte à Computação do Azure Machine Learning. 

        1. Popule o formulário **Máquina Virtual** para configurar sua computação.

            Campo | Descrição | Valor para o tutorial
            ----|---|---
            Prioridade da&nbsp;máquina&nbsp;virtual |Selecione a prioridade que o experimento deve ter| Dedicado
            Tipo de&nbsp;máquina&nbsp;virtual| Selecione o tipo da máquina virtual da computação.|CPU (Unidade de Processamento Central)
            Tamanho&nbsp;da&nbsp;máquina virtual| Selecione o tamanho da máquina virtual da computação. É fornecida uma lista de tamanhos recomendados com base em seus dados e no tipo de experimento. |Standard_DS12_V2
        
        1. Selecione **Avançar** para preencher o **Formulário Definir configurações**.
        
             Campo | Descrição | Valor para o tutorial
            ----|---|---
            Nome da computação |  Um nome exclusivo que identifique o contexto de computação. | bike-compute
            Mín./máx. de nós| Para analisar os dados, é necessário especificar um ou mais nós.|Número mín. de nós: 1<br>Número máx. de nós: 6
            Segundos de espera antes de reduzir verticalmente | Tempo de espera antes de o cluster ser automaticamente reduzido verticalmente para a contagem do mínimo de nós.|120 (padrão)
            Configurações avançadas | Definições para configurar e autorizar uma rede virtual para seu experimento.| Nenhum 
  
        1. Selecione **Criar** para obter o destino de computação. 

            **Isso levará alguns minutos para ser concluído.** 

        1. Após a criação, selecione o novo destino de computação na lista suspensa.

    1. Selecione **Avançar**.

## <a name="select-forecast-settings"></a>Selecionar configurações de previsão

Conclua a configuração do experimento de ML automatizado especificando o tipo de tarefa de aprendizado de máquina e as definições de configuração.

1. No formulário **Tipo de tarefa e configurações**, selecione **Previsão de série temporal** como o tipo de tarefa de aprendizado de máquina.

1. Selecione **data** como a **Coluna de tempo** e deixe os **Identificadores de série temporal** em branco. 

1. O **horizonte de previsão** é o período de tempo no futuro que você deseja prever.  Desmarque a Detecção Automática e digite 14 no campo. 

1. Selecione **Exibir definições de configuração adicionais** e preencha os campos da seguinte maneira. Essas configurações são destinadas a controlar melhor o trabalho de treinamento e especificar configurações para sua previsão. Caso contrário, os padrões são aplicados com base na seleção e nos dados de experimento.

    Configurações&nbsp;adicionais|Descrição|Valor&nbsp;para o&nbsp;tutorial
    ------|---------|---
    Métrica principal| Métrica de avaliação pela qual o algoritmo de aprendizado de máquina será medido.|Raiz do erro quadrático médio normalizado
    Explicar o melhor modelo| Mostra automaticamente a explicabilidade no melhor modelo criado pelo ML automatizado.| Habilitar
    Algoritmos bloqueados | Algoritmos que você deseja excluir do trabalho de treinamento| Árvores aleatórias extremas
    Configurações adicionais de previsão| Essas configurações ajudam a aprimorar a precisão do modelo. <br><br> _**Retardos de destino de previsão:**_ até que ponto no passado você deseja construir os retardos da variável de destino <br> _**Janela rolante de destino**_: especifica o tamanho da janela rolante na qual recursos, como *máx., mín.* e *soma*, serão gerados. | <br><br>Retardos&nbsp;de destino&nbsp;de previsão: Nenhum <br> Tamanho&nbsp;da janela&nbsp;rolante&nbsp;de destino: Nenhum
    Critério de saída| Se um critério for atendido, o trabalho de treinamento será interrompido. |Hora&nbsp;do&nbsp;trabalho de treinamento (horas): 3 <br> Limite de&nbsp;pontuação da&nbsp;métrica: Nenhum
    Validação | Escolha um tipo de validação cruzada e um número de testes.|Tipo de validação:<br>validação cruzada&nbsp;k-fold&nbsp; <br> <br> Número de validações: 5
    Simultaneidade| O número máximo de iterações paralelas executadas por iteração| Máximo de&nbsp;iterações&nbsp;simultâneas: 6
    
    Clique em **Salvar**.

## <a name="run-experiment"></a>Executar o experimento

Para executar o experimento, selecione **Concluir**. A tela **Detalhes da execução** será aberta com o **Status da execução** na parte superior ao lado do número da execução. Esse status é atualizado conforme o progresso do experimento. Também aparecem notificações no canto superior direito do estúdio para informar você sobre o status do experimento.

>[!IMPORTANT]
> A preparação leva de **10 a 15 minutos** para preparar a execução do experimento.
> Durante a execução, são necessários **mais 2 a 3 minutos para cada iteração**.<br> <br>
> Em produção, provavelmente, você terá tempo para outras tarefas, pois esse processo é demorado. Enquanto você aguarda, sugerimos que você comece a explorar os algoritmos testados na guia **Modelos** conforme eles são concluídos. 

##  <a name="explore-models"></a>Explorar modelos

Navegue até a guia **Modelos** para ver os algoritmos (modelos) testados. Por padrão, os modelos são ordenados pela pontuação da métrica à medida que são concluídos. Para este tutorial, o modelo com a pontuação mais alta com base na métrica **Raiz do erro quadrático médio normalizado** escolhida é exibido no início da lista.

Enquanto você aguarda a conclusão de todos os modelos de experimento, selecione o **Nome do algoritmo** de um modelo concluído para explorar seus detalhes de desempenho. 

O exemplo a seguir navega pelas guias **Detalhes** e **Métricas** para exibir as propriedades, as métricas e os gráficos de desempenho do modelo selecionado. 

![Detalhe da execução](./media/tutorial-automated-ml-forecast/explore-models.gif)

## <a name="deploy-the-model"></a>Implantar o modelo

O machine learning automatizado no estúdio do Azure Machine Learning permite que você implante o melhor modelo como um serviço Web em algumas etapas. A implantação é a integração do modelo para que ele possa prever novos dados e identificar possíveis áreas de oportunidade. 

Para este experimento, a implantação em um serviço Web significa que a empresa de compartilhamento de bicicletas agora tem uma solução da Web iterativa e escalonável para prever a demanda de aluguel de compartilhamento de bicicletas. 

Quando a execução estiver concluída, volte para a página de execução pai selecionando **Execução 1** na parte superior da sua tela.

Na seção **Resumo do melhor modelo**, **StackEnsemble** é considerado o melhor modelo no contexto deste experimento, com base na métrica **Raiz do erro quadrático médio normalizado**.  

Implantamos esse modelo, mas saiba que a implantação demora cerca de 20 minutos para ser concluída. O processo de implantação envolve várias etapas, incluindo o registro do modelo, a geração de recursos e a configuração deles para o serviço Web.

1. Selecione **StackEnsemble** para abrir a página específica do modelo.

1. Selecione o botão **Implantar** localizado na área superior esquerda da tela.

1. Preencha o painel **Implantar um Modelo** da seguinte maneira:

    Campo| Valor
    ----|----
    Nome da implantação| bikeshare-deploy
    Descrição da implantação| implantação da demanda de compartilhamento de bicicletas
    Tipo de computação | Selecionar ACI (Instância de Computação do Azure)
    Habilitar autenticação| Desabilite. 
    Usar ativos da implantação personalizada| Desabilite. A desabilitação permite que o arquivo de driver padrão (script de pontuação) e o arquivo de ambiente sejam gerados automaticamente. 
    
    Para este exemplo, usamos os padrões fornecidos no menu *Avançado*. 

1. Selecione **Implantar**.  

    Uma mensagem de êxito verde será exibida na parte superior da tela **Executar** indicando que a implantação foi iniciada com êxito. O progresso da implantação pode ser encontrado no painel de **Resumo do modelo** em **Status da implantação**.
    
Após o êxito da implantação, você terá um serviço Web operacional para gerar previsões. 

Vá para as [**Próximas etapas**](#next-steps) para saber mais sobre como consumir o novo serviço Web e testar as previsões usando o suporte interno do Power BI no Azure Machine Learning.

## <a name="clean-up-resources"></a>Limpar os recursos

Os arquivos de implantação são maiores que os dados e os arquivos de teste, portanto, eles custam mais para serem armazenados. Exclua somente os arquivos de implantação para minimizar os custos de sua conta ou se você quiser manter o workspace e os arquivos do experimento. Caso contrário, se você não planeja usar nenhum dos arquivos, exclua o grupo de recursos inteiro.  

### <a name="delete-the-deployment-instance"></a>Excluir a instância de implantação

Exclua apenas a instância de implantação do estúdio do Azure Machine Learning se você quiser manter o grupo de recursos e o workspace para outros tutoriais e explorações. 

1. Vá para o [estúdio do Azure Machine Learning](https://ml.azure.com/). Navegue até o workspace e, no lado esquerdo, no painel **Ativos**, selecione **Pontos de extremidade**. 

1. Selecione a implantação que você deseja excluir e selecione **Excluir**. 

1. Selecione **Continuar**.

### <a name="delete-the-resource-group"></a>Exclua o grupo de recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou o ML automatizado no Azure Machine Learning Studio para criar e implantar um modelo de previsão de série temporal que prevê a demanda de aluguel de compartilhamento de bicicletas. 

Confira este artigo para obter as etapas de como criar um esquema compatível com o Power BI para facilitar o consumo de seu serviço Web recém-implantado:

> [!div class="nextstepaction"]
> [Consumir um serviço Web](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ Saiba mais sobre o [aprendizado de máquina automatizado](concept-automated-ml.md).
+ Para saber mais sobre métricas e gráficos de classificação, confira o artigo [Noções básicas sobre os resultados de machine learning automatizado](how-to-understand-automated-ml.md).
+ Saiba mais sobre a [personalização](how-to-configure-auto-features.md#featurization).
+ Saiba mais sobre a [criação de perfil de dados](how-to-connect-data-ui.md#profile).

>[!NOTE]
> Este conjunto de dados de compartilhamento de bicicletas foi modificado para este tutorial. Esse conjunto de dados foi disponibilizado como parte de uma [competição da Kaggle](https://www.kaggle.com/c/bike-sharing-demand/data) e estava originalmente disponível por meio da [Capital Bikeshare](https://www.capitalbikeshare.com/system-data). Também pode ser encontrado no [Banco de dados de machine learning do UCI](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset).<br><br>
> Origem: Fanaee-T, Hadi e Gama, Joao, Rotulagem de evento combinando detectores de agrupamento e conhecimento prévio, Progresso em inteligência artificial (2013): pp. 1-15, Springer Berlim Heidelberg.