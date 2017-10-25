---
title: "Previsão de rotatividade de clientes usando o Azure Machine Learning | Microsoft Docs"
description: "Como realizar análise da rotatividade usando o Azure ML Workbench."
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ms.openlocfilehash: 2ee7ec91700c66d5bedd917d0203a726b5c5e300
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Previsão de rotatividade de clientes usando o Azure Machine Learning

Em média, manter clientes existentes é cinco vezes mais barato do que o custo de recrutar novos. Como resultado, geralmente os executivos de marketing se veem tentando estimar a probabilidade de rotatividade de clientes e encontrar as ações necessárias para minimizar a taxa de rotatividade.

O objetivo desta solução é demonstrar a análise de rotatividade preditiva usando o Azure Machine Learning Workbench. Esta solução fornece um modelo fácil de usar para desenvolver pipelines de dados preditivos de rotatividade para revendedores. O modelo pode ser usado com conjuntos de dados diferentes e diferentes definições de rotatividade. O objetivo do exemplo prático é:

1. Entender as ferramentas de Preparação de Dados do Azure Machine Learning Workbench para limpar e ingerir dados de relacionamento com o cliente para análise de rotatividade.

2. Realizar a transformação de recursos para manipular dados heterogêneos com ruído.

3. Integrar bibliotecas de terceiros (como `scikit-learn` e `azureml`) para desenvolver classificadores Bayesianos e baseados em árvore para prever a rotatividade.

4. Implantar.

## <a name="link-of-the-gallery-github-repository"></a>Link do repositório do GitHub da galeria
A seguir está o link para o repositório público do GitHub em que todo o código está hospedado:

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>Visão geral de casos de uso
As empresas precisam de uma estratégia eficiente para gerenciar a rotatividade de clientes. A rotatividade de clientes inclui clientes que param de usar um serviço, mudam para um serviço concorrente, mudam para uma experiência de nível inferior no serviço ou reduzem o envolvimento com o serviço.

Neste caso de uso, examinamos dados da empresa de telecomunicações francesa Orange para identificar clientes que estão propensos a promoverem rotatividade no curto prazo a fim de aprimorar o serviço e criar campanhas de atendimento personalizado que ajudem a reter os clientes.

As empresas de telecomunicações enfrentam um mercado competitivo. Muitas operadoras perdem receita de clientes pós-pagos devido à rotatividade. Assim, a capacidade de identificar com precisão a rotatividade do cliente pode ser uma grande vantagem competitiva.

Alguns fatores que contribuem para a rotatividade do cliente de telecomunicações incluem:

* Percepção de interrupções de serviço frequentes
* Experiências de atendimento ao cliente ruim em lojas online/de varejo
* Ofertas de outras operadoras concorrentes (melhor plano familiar, plano de dados, etc.).

Nesta solução, usaremos um exemplo concreto da criação de um modelo de rotatividade de clientes preditivo para empresas de telecomunicações.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta do Azure](https://azure.microsoft.com/free/) (avaliações gratuitas estão disponíveis)

* Uma cópia instalada do [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguindo o [guia de instalação de início rápido](./quickstart-installation.md) para instalar o programa e criar um espaço de trabalho

* Para a operacionalização, será melhor se você tiver um mecanismo do Docker instalado e em execução localmente. Caso contrário, será possível usar a opção de cluster, mas lembre-se de que executar um ACS (Serviço de Contêiner do Azure) pode ser caro.

* Esta solução pressupõe que você esteja executando o Azure Machine Learning Workbench no Windows 10 com o mecanismo do Docker instalado localmente. Se você estiver usando o macOS, a instrução será basicamente a mesma.

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto de Workbench

Crie um novo projeto usando este exemplo como modelo:
1.  Abra o Azure Machine Learning Workbench
2.  Na página **Projetos**, clique no sinal **+** e selecione **Novo Projeto**
3.  No painel **Criar Novo Projeto**, preencha as informações do seu novo projeto
4.  Na caixa de pesquisa **Pesquisar modelos de projeto**, digite "Previsão de rotatividade de clientes" e selecione o modelo
5.  Clique em **Criar**

## <a name="data-description"></a>Descrição dos dados

O conjunto de dados usado na solução é da concorrência SIDKDD 2009. É chamado `CATelcoCustomerChurnTrainingSample.csv` e está localizado na pasta [`data`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/tree/master/data). O conjunto de dados consiste em dados heterogêneos com ruídos (variáveis numéricas/categóricas) da empresa de telecomunicações francesa Orange e é mantido anônimo.

As variáveis capturam informações demográficas do cliente, estatísticas de chamadas (como duração média das chamadas, taxa de falha de chamadas, etc.), informações sobre o contrato, estatísticas de reclamação. A variável de rotatividade é binária (0 – não houve rotatividade, 1 – houve rotatividade).

## <a name="scenario-structure"></a>Estrutura do cenário

A estrutura de pastas é organizada da seguinte maneira:

__dados__: contém o conjunto de dados usado na solução  

__docs__: contém todos os laboratórios práticos

A ordem dos Laboratórios práticos para executar a solução é a seguinte:
1. Preparação de Dados: o arquivo principal relacionado com Preparação de Dados na pasta dos dados é `CATelcoCustomerChurnTrainingSample.csv`
2. Modelagem e avaliação: o arquivo principal relacionado com a modelagem e a avaliação na pasta raiz é `CATelcoCustomerChurnModeling.py`
3. Modelagem e avaliação no Docker: o arquivo principal para esta tarefa na pasta raiz é `CATelcoCustomerChurnModelingDocker.py`
4. Operacionalização: os principais arquivos para implantação são o modelo (`model.pkl`) e `churn_schema_gen.py`

| Classificar| Nome do Arquivo | Arquivos relacionados |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/DataPreparation.md) | 'data/CATelcoCustomerChurnTrainingSample.csv' |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluation.md) | 'CATelcoCustomerChurnModeling.py' |
| 3 | [`ModelingAndEvaluationDocker.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluationDocker.md) | 'CATelcoCustomerChurnModelingDocker.py' |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/Operationalization.md) | 'model.pkl'<br>'churn_schema_gen.py' |

Siga os Laboratórios da maneira sequencial descrita acima.

## <a name="conclusion"></a>Conclusão
Esse cenário prático demonstrou como realizar previsão de rotatividade usando o Azure Machine Learning Workbench. Primeiro realizamos a limpeza de dados para manipular dados heterogêneos e com ruídos, seguida pela engenharia de recursos usando ferramentas de Preparação de dados. Em seguida, usamos ferramentas de aprendizado de máquina de software livre para criar e avaliar um modelo de classificação. Depois, usamos o contêiner do docker local para implantar o modelo, tornando-o pronto para a produção.
