---
title: Exportar ou excluir dados do workspace
titleSuffix: Azure Machine Learning service
description: Saiba como exportar ou excluir seu workspace com o portal do Azure, a CLI, o SDK e as APIs REST autenticadas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: ph-com
ms.author: pahusban
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 3f40606d5fae3b3784ac7f1fdcf4977b7fd9eb1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819426"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportar ou excluir dados do espaço de trabalho no serviço do Machine Learning 

No Azure Machine Learning, você pode exportar ou excluir seus dados de workspace com a API REST autenticada. Este artigo informa como fazer isso.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Controlar seus dados de workspace
Os dados armazenados no produto pelos serviços do Azure Machine Learning estão disponíveis para exportação e exclusão por meio do portal do Azure, da CLI, do SDK e de APIs REST autenticadas. É possível acessar os dados telemétricos por meio do Portal de Privacidade do Azure. 

Nos serviços do Azure Machine Learning, os dados pessoais são formados por informações do usuário em documentos de histórico de execução e registros telemétricos de algumas interações do usuário com o serviço.

## <a name="delete-workspace-data-with-the-rest-api"></a>Excluir dados de workspace com a API REST 

Para excluir os dados, as chamadas da API a seguir podem ser feitas com o verbo HTTP DELETE. Eles são autorizados devido ao cabeçalho `Authorization: Bearer <arm-token>` na solicitação, em que `<arm-token>` é o token de acesso do AAD para o ponto de extremidade `https://management.core.windows.net/`.  

Para saber como obter esse token e chamar os pontos de extremidade do Azure, confira [Documentação da API REST do Azure](https://docs.microsoft.com/rest/api/azure/).  

Nos exemplos a seguir, substitua o texto em {} pelos nomes de instância que determinam o recurso associado.

### <a name="delete-an-entire-workspace"></a>Excluir um workspace inteiro

Use essa chamada para excluir um workspace inteiro.  
> [!WARNING]
> Todas as informações serão excluídas e o workspace não será mais utilizável.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Excluir modelos

Use essa chamada para obter uma lista de modelos e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

É possível excluir modelos individuais com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Excluir ativos

Use essa chamada para obter uma lista dos ativos e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Os ativos individuais podem ser excluídos com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Excluir imagens

Use essa chamada para obter uma lista das imagens e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

É possível excluir imagens individuais com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Excluir serviços

Use essa chamada para obter uma lista das serviços e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

É possível excluir serviços individuais com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>Exportar dados de serviço com a API REST

Para exportar os dados, as chamadas da API a seguir podem ser feitas com o verbo HTTP GET. Eles são autorizados devido ao cabeçalho `Authorization: Bearer <arm-token>` na solicitação, em que `<arm-token>` é o token de acesso do AAD para o ponto de extremidade `https://management.core.windows.net/`  

Para saber como obter esse token e chamar os pontos de extremidade do Azure, confira [Documentação da API REST do Azure](https://docs.microsoft.com/rest/api/azure/).   

Nos exemplos a seguir, substitua o texto em {} pelos nomes de instância que determinam o recurso associado.

### <a name="export-workspace-information"></a>Exportar informações do Workspace

Use essa chamada para obter uma lista de todos os workspaces:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Informações sobre um workspace individual podem ser obtidas por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Exportar informações de computação

Todos os destinos de computação anexados a um workspace podem ser obtidos por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Informações sobre um único destino de computação podem ser obtidas por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Exportar dados do histórico de execução
Use essa chamada para obter uma lista de todos os experimentos e suas informações:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Todas as execuções de um experimento específico podem ser obtidas por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

É possível obter os itens do histórico de execução com:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Todas as métricas de execução de um experimento podem ser obtidas por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Uma única métrica de execução pode ser obtida por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Exportar artefatos

Use essa chamada para obter uma lista dos artefatos e seus caminhos:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Exportar notificações

Use essa chamada para obter uma lista de tarefas armazenadas:     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Notificações para uma única tarefa podem ser obtidas por:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Exportar armazenamentos de dados

Use essa chamada para obter uma lista de armazenamentos de dados:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Armazenamentos de dados individuais podem ser obtidos por:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Exportar modelos

Use essa chamada para obter uma lista de modelos e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

É possível obter modelos individuais com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Exportar ativos

Use essa chamada para obter uma lista dos ativos e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Ativos individuais podem ser obtidos por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Exportar imagens

Use essa chamada para obter uma lista das imagens e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Imagens individuais podem ser obtidas por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Exportar serviços

Use essa chamada para obter uma lista das serviços e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

É possível obter serviços individuais com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Exportar Experimentos de Pipeline

Experimentos individuais podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Exportar Gráficos de Pipeline

Gráficos individuais podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Exportar Módulos de Pipeline

Os módulos podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Exportar Modelos de Pipeline

Modelos podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Exportar Fontes de Dados de Pipeline

Fontes de Dados podem ser obtidas por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}
