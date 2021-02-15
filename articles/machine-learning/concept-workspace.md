---
title: O que é um espaço de trabalho
titleSuffix: Azure Machine Learning
description: O workspace é o recurso de nível superior do Azure Machine Learning. Ele mantém um histórico de todas as execuções de treinamento, com logs, métricas, saída e um instantâneo de seus scripts.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.openlocfilehash: 5211555102c0a8d29eb05abd7a680935d828f9dc
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683433"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>O que é um workspace do Azure Machine Learning?

O espaço de trabalho é o recurso de nível superior para Azure Machine Learning, fornecendo um local centralizado para trabalhar com todos os artefatos que você criar ao usar Azure Machine Learning.  O espaço de trabalho mantém um histórico de todas as execuções de treinamento, incluindo logs, métricas, saída e um instantâneo de seus scripts. Essas informações são usadas para determinar quais execuções de treinamento produzem o melhor modelo.  

Quando você tiver um modelo que desejar, registre-o no espaço de trabalho. Em seguida, você usa o modelo registrado e os scripts de Pontuação para implantar nas instâncias de contêiner do Azure, no serviço kubernetes do Azure ou em uma FPGA (matriz de porta programável por campo) como um ponto de extremidade HTTP baseado em REST. Você também pode implantar o modelo em um dispositivo Azure IoT Edge como um módulo.

## <a name="taxonomy"></a>Taxonomia 

O diagrama a seguir é uma taxonomia do workspace:

[![Taxonomia do espaço de trabalho](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

O diagrama mostra os seguintes componentes de um espaço de trabalho:

+ Um espaço de trabalho pode conter [Azure Machine Learning instâncias de computação](concept-compute-instance.md), recursos de nuvem configurados com o ambiente Python necessário para executar Azure Machine Learning.

+ As [funções de usuário](how-to-assign-roles.md) permitem que você compartilhe seu espaço de trabalho com outros usuários, equipes ou projetos.
+ Os [destinos de computação](concept-azure-machine-learning-architecture.md#compute-targets) são usados para executar seus experimentos.
+ Quando você cria o espaço de trabalho, os [recursos associados](#resources) também são criados para você.
+ Os [experimentos](concept-azure-machine-learning-architecture.md#experiments) são as execuções de treinamento que você usa para criar seus modelos.  
+ [Pipelines](concept-azure-machine-learning-architecture.md#ml-pipelines) são fluxos de trabalho reutilizáveis para treinar e treinar novamente seu modelo.
+ [DataSets](concept-azure-machine-learning-architecture.md#datasets-and-datastores) auxiliam no gerenciamento dos dados que você usa para treinamento de modelo e criação de pipeline.
+ Quando você tiver um modelo que deseja implantar, crie um modelo registrado.
+ Use o modelo registrado e um script de Pontuação para criar um [ponto de extremidade de implantação](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Ferramentas para interação do espaço de trabalho

Você pode interagir com seu espaço de trabalho das seguintes maneiras:

> [!IMPORTANT]
> As ferramentas marcadas (visualização) abaixo estão atualmente em visualização pública.
> A versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+ Na Web:
    + [Azure Machine Learning Studio ](https://ml.azure.com) 
    + [Designer do Azure Machine Learning](concept-designer.md) 
+ Em qualquer ambiente do Python com o [SDK do Azure Machine Learning para Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).
+ Em qualquer ambiente de R com o [SDK do Azure Machine Learning para R (versão prévia)](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Na linha de comando usando a [extensão da CLI](./reference-azure-machine-learning-cli.md) do Azure Machine Learning
+ [Extensão de VS Code de Azure Machine Learning](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>Aprendizado de máquina com um espaço de trabalho

As tarefas de aprendizado de máquina lêem e/ou gravam artefatos em seu espaço de trabalho.

+ Executar um experimento para treinar um modelo – grava os resultados da execução do experimento no espaço de trabalho.
+ Use o ML automatizado para treinar um modelo – grava os resultados de treinamento no espaço de trabalho.
+ Registrar um modelo no espaço de trabalho.
+ Implantar um modelo – usa o modelo registrado para criar uma implantação.
+ Crie e execute fluxos de trabalho reutilizáveis.
+ Exiba artefatos de Machine Learning, como experimentos, pipelines, modelos, implantações.
+ Acompanhe e monitore modelos.

## <a name="workspace-management"></a>Gerenciamento de espaço de trabalho

Você também pode executar as seguintes tarefas de gerenciamento de espaço de trabalho:

| Tarefa de gerenciamento de espaço de trabalho   | Portal              | Estúdio | SDK/R do SDK do Python       | CLI        | Código VS
|---------------------------|---------|---------|------------|------------|------------|
| Criar um workspace        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Gerenciar o acesso ao espaço de trabalho    | **&check;**   || |  **&check;**    ||
| Criar e gerenciar recursos de computação    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Criar uma VM do notebook |   | **&check;** | |     ||

> [!WARNING]
> Não há suporte para a movimentação do workspace do Azure Machine Learning para outra assinatura nem para a movimentação da assinatura proprietária para um novo locatário. Se você fizer isso, poderá causar erros.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Criar um espaço de trabalho

Há várias maneiras de criar um espaço de trabalho:  

* Use o [portal do Azure](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace) para uma interface de apontar e clicar para orientá-lo em cada etapa.
* Use o [SDK do Azure Machine Learning para Python](how-to-manage-workspace.md?tabs=python#create-a-workspace) para criar um espaço de trabalho instantaneamente de scripts Python ou notebooks Jupyter
* Use um [modelo de Azure Resource Manager](how-to-create-workspace-template.md) ou a [CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md) quando precisar automatizar ou personalizar a criação com os padrões de segurança corporativa.
* Se você trabalhar em Visual Studio Code, use a [extensão vs Code](how-to-manage-resources-vscode.md#create-a-workspace).

> [!NOTE]
> O nome do workspace não diferencia maiúsculas de minúsculas.

## <a name="associated-resources"></a><a name="resources"></a> Recursos associados

Quando você cria um novo workspace, ele automaticamente cria vários recursos do Azure que são usados pelo workspace:

+ [Conta de armazenamento do Azure](https://azure.microsoft.com/services/storage/): é usada como o repositório de armazenamento padrão para o espaço de trabalho.  Os blocos de anotações do Jupyter que são usados com suas instâncias de computação Azure Machine Learning também são armazenados aqui. 
  
  > [!IMPORTANT]
  > Por padrão, a conta de armazenamento é uma conta v1 de uso geral. Você pode [atualizar isso para a finalidade geral v2](../storage/common/storage-account-upgrade.md) depois que o espaço de trabalho tiver sido criado. Não habilite o namespace hierárquico na conta de armazenamento após a atualização para o uso geral v2.

  Para usar uma conta de armazenamento do Azure existente, ela não pode ser uma conta Premium (Premium_LRS e Premium_GRS). Ele também não pode ter um namespace hierárquico (usado com Azure Data Lake Storage Gen2). Não há suporte para namespaces hierárquicos ou de armazenamento Premium com a conta de armazenamento _padrão_ do espaço de trabalho. Você pode usar o armazenamento Premium ou o namespace hierárquico com contas _de armazenamento não padrão_ .
  
+ [Registro de contêiner do Azure](https://azure.microsoft.com/services/container-registry/): registra os contêineres do Docker que você usa durante o treinamento e quando implanta um modelo. Para minimizar os custos, o ACR é **carregado com o preguiçoso** até que as imagens de implantação sejam criadas.

+ [Insights de aplicativo Azure](https://azure.microsoft.com/services/application-insights/): armazena informações de monitoramento sobre seus modelos.

+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): armazena segredos que são usados por destinos de computação e outras informações confidenciais que são necessárias para o espaço de trabalho.

> [!NOTE]
> Em vez disso, você pode usar instâncias de recursos do Azure existentes ao criar o espaço de trabalho com o [SDK do Python](how-to-manage-workspace.md?tabs=python#create-a-workspace), o [SDK do R](https://azure.github.io/azureml-sdk-for-r/reference/create_workspace.html)ou a CLI do Azure Machine Learning [usando um modelo ARM](how-to-create-workspace-template.md).

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>O que aconteceu com a Enterprise Edition

A partir de setembro de 2020, todos os recursos que estavam disponíveis nos espaços de trabalho da Enterprise Edition agora também estão disponíveis em espaços de trabalho da edição básica. Não é mais possível criar novos espaços de trabalho corporativos.  Todas as chamadas de SDK, CLI ou Azure Resource Manager que usam o `sku` parâmetro continuarão funcionando, mas um espaço de trabalho básico será provisionado.

A partir de 21 de dezembro, todos os espaços de trabalho do Enterprise Edition serão automaticamente definidos para a edição básica, que tem os mesmos recursos. Nenhum tempo de inatividade ocorrerá durante esse processo. Em 1º de janeiro de 2021, a Enterprise Edition será desativada formalmente. 

Em ambas as edições, os clientes são responsáveis pelos custos dos recursos do Azure consumidos e não precisarão pagar cobranças adicionais por Azure Machine Learning. Consulte a página de [preços do Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/) para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o Azure Machine Learning, confira:

+ [Visão geral de Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Criar e gerenciar um espaço de trabalho](how-to-manage-workspace.md)
+ [Tutorial: introdução ao Azure Machine Learning em seu ambiente de desenvolvimento](tutorial-1st-experiment-sdk-setup-local.md)
+ [Tutorial: introdução à criação de seu primeiro experimento do ML em uma instância de computação](tutorial-1st-experiment-sdk-setup.md)
+ [Tutorial: Crie seu primeiro modelo de classificação com o machine learning automatizado](tutorial-first-experiment-automated-ml.md) 
+ [Tutorial: Prever preço de automóvel com o designer](tutorial-designer-automobile-price-train-score.md)
