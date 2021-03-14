---
title: Exportar ou excluir dados do workspace
titleSuffix: Azure Machine Learning
description: Saiba como exportar ou excluir seu espaço de trabalho com o Azure Machine Learning Studio, CLI, SDK e APIs REST autenticadas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 030e09f0bb1514e8d3828da52604d751a2a8a11a
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520074"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportar ou excluir dados do workspace no serviço do Machine Learning

No Azure Machine Learning, você pode exportar ou excluir os dados do espaço de trabalho usando a interface gráfica do portal ou o SDK do Python. Este artigo descreve as duas opções.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Controlar seus dados de workspace

Os dados no produto armazenados por Azure Machine Learning estão disponíveis para exportação e exclusão. Você pode exportar e excluir usando Azure Machine Learning Studio, CLI e SDK. É possível acessar os dados telemétricos por meio do Portal de Privacidade do Azure. 

Em Azure Machine Learning, os dados pessoais consistem em informações do usuário em documentos de histórico de execução. 

## <a name="delete-high-level-resources-using-the-portal"></a>Excluir recursos de alto nível usando o portal

Quando você cria um espaço de trabalho, o Azure cria um número de recursos dentro do grupo de recursos:

- O próprio espaço de trabalho
- Uma conta de armazenamento
- Um registro de contêiner
- Uma instância do Application insights
- Um cofre de chaves

Esses recursos podem ser excluídos selecionando-os na lista e escolhendo **excluir** 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="Captura de tela do portal, com o ícone Excluir realçado":::

Os documentos de histórico de execução, que podem conter informações pessoais do usuário, são armazenados na conta de armazenamento no armazenamento de BLOBs, em subpastas do `/azureml` . Você pode baixar e excluir os dados do Portal.

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="Captura de tela do diretório do azureml na conta de armazenamento, dentro do portal":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>Exportar e excluir recursos de aprendizado de máquina usando o Azure Machine Learning Studio

O Azure Machine Learning Studio fornece uma exibição unificada dos seus recursos de aprendizado de máquina, como notebooks, conjuntos de informações, modelos e experimentos. O Azure Machine Learning Studio enfatiza a preservação de um registro de seus dados e experimentos. Recursos computacionais, como pipelines e recursos de computação, podem ser excluídos usando o navegador. Para esses recursos, navegue até o recurso em questão e escolha **excluir**. 

Os conjuntos de dados podem ter o registro cancelado e os experimentos podem ser arquivados, mas essas operações não excluem os dados. Para remover totalmente os dados, os conjuntos de dados e o Run data devem ser excluídos no nível de armazenamento. A exclusão no nível de armazenamento é feita usando o portal, conforme descrito anteriormente.

Você pode baixar os artefatos de treinamento de execuções experimentais usando o estúdio. Escolha o **experimento** e **Execute** em que você está interessado. Escolha **saída + logs** e navegue até os artefatos específicos que você deseja baixar. Escolha **...** e **Baixe**.

Você pode baixar um modelo registrado navegando até o **modelo** desejado e escolhendo **baixar**. 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="Captura de tela da página modelo do estúdio com a opção de download realçada":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>Exportar e excluir recursos usando o SDK do Python

Você pode baixar as saídas de uma execução específica usando: 

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['my-experiment']
run = next(run for run in ex.get_runs() if run.id == run_id)
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Os seguintes recursos de aprendizado de máquina podem ser excluídos usando o SDK do Python: 

| Tipo | Chamada de função | Anotações | 
| --- | --- | --- |
| `Workspace` | [`delete`](/python/api/azureml-core/azureml.core.workspace.workspace#delete-delete-dependent-resources-false--no-wait-false-) | Use `delete-dependent-resources` para propagar a exclusão |
| `Model` | [`delete`](/python/api/azureml-core/azureml.core.model%28class%29#delete--) | | 
| `ComputeTarget` | [`delete`](/python/api/azureml-core/azureml.core.computetarget#delete--) | |
| `WebService` | [`delete`](/python/api/azureml-core/azureml.core.webservice%28class%29) | |