---
title: Criar um serviço vinculado com espaços de trabalho do Synapse e do Azure Machine Learning (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como vincular os espaços de trabalho Synapse e Azure Machine Learning do Azure para uma experiência de Wrangling de dados unificada.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: dd62fb5e3c7450d50b9837ee5484ca480cab78aa
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640840"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Vincular o Azure Synapse Analytics e os espaços de trabalho do Azure Machine Learning (versão prévia)

Neste artigo, você aprenderá a criar um serviço vinculado que vincula seu espaço de trabalho [do Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) e [Azure Machine Learning espaço de trabalho](concept-workspace.md).

Com seu espaço de trabalho Azure Machine Learning vinculado ao seu espaço de trabalho Synapse do Azure, você pode anexar um pool de Apache Spark como uma computação dedicada para os dados Wrangling em escala e conduzir treinamento de modelo do mesmo bloco de anotações.

Você pode vincular seu espaço de trabalho do ML e o espaço de trabalho do Synapse por meio do [SDK do Python](#link-sdk) ou do [Azure Machine Learning Studio](#link-studio).

Você também pode vincular espaços de trabalho e anexar um pool Synapse Spark a um único [modelo de Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json).

>[!IMPORTANT]
> A integração do Azure Machine Learning e do Azure Synapse está em visualização pública. As funcionalidades apresentadas no `azureml-synapse` pacote são recursos de visualização [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) e podem mudar a qualquer momento.

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um workspace do Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Crie um espaço de trabalho Synapse no portal do Azure](/azure/synapse-analytics/quickstart-create-workspace).

* [Criar Apache Spark pool usando portal do Azure, ferramentas da Web ou o Synapse Studio](/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)

* Instalar o [SDK do Azure Machine Learning Python](/python/api/overview/azure/ml/intro)

* Acesso ao [Azure Machine Learning Studio](https://ml.azure.com/).

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Vincular espaços de trabalho com o SDK do Python

> [!IMPORTANT]
> Para vincular com êxito o espaço de trabalho Synapse, você deve receber a função de **proprietário** do espaço de trabalho Synapse. Verifique seu acesso no [portal do Azure](https://ms.portal.azure.com/).
>
> Se você não for um **proprietário** e for apenas um **colaborador** do espaço de trabalho Synapse, você só poderá usar os serviços vinculados existentes. Veja como [recuperar e usar um serviço vinculado existente](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service).

O código a seguir emprega as [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) classes e para,

* Vincule seu espaço de trabalho do Machine Learning `ws` ao seu espaço de trabalho Synapse do Azure.
* Registre seu espaço de trabalho do Synapse com Azure Machine Learning como um serviço vinculado.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> Uma identidade gerenciada, `system_assigned_identity_principal_id` , é criada para cada serviço vinculado. Essa identidade gerenciada deve receber a função de **administrador de Apache Spark Synapse** do espaço de trabalho Synapse antes de iniciar a sessão Synapse. [Atribua a função de administrador de Apache Spark Synapse à identidade gerenciada no Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Para localizar o `system_assigned_identity_principal_id` de um serviço vinculado específico, use `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

### <a name="manage-linked-services"></a>Gerenciar serviços vinculados

Exiba todos os serviços vinculados associados ao seu espaço de trabalho do Machine Learning.

```python
LinkedService.list(ws)
```

Para desvincular seus espaços de trabalho, use o `unregister()` método

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Vincular espaços de trabalho por meio do estúdio

Vincule seu espaço de trabalho do Machine Learning e o espaço de trabalho Synapse por meio do Azure Machine Learning Studio com as seguintes etapas: 

1. Entre no [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selecione **Serviços vinculados** na seção **gerenciar** do painel esquerdo.
1. Selecione **Adicionar integração**.
1. No formulário **vincular espaço de trabalho** , preencha os campos

    |Campo| Descrição    
    |---|---
    |Nome| Forneça um nome para o serviço vinculado. Esse nome é o que será usado para fazer referência a esse serviço vinculado específico.
    |Nome da assinatura | Selecione o nome da sua assinatura associada ao seu espaço de trabalho do Machine Learning. 
    |Espaço de trabalho Synapse | Selecione o espaço de trabalho Synapse ao qual você deseja vincular.
    
1. Selecione **Avançar** para abrir o formulário **selecionar pools do Spark (opcional)** . Neste formulário, você seleciona o pool do Spark Synapse a ser anexado ao seu espaço de trabalho

1. Selecione **Avançar** para abrir o formulário de **revisão** e verificar suas seleções.
1. Selecione **criar** para concluir o processo de criação de serviço vinculado.

## <a name="next-steps"></a>Próximas etapas

* [Anexe pools do Synapse Spark para a preparação de dados com o Azure Synapse (versão prévia)](how-to-data-prep-synapse-spark-pool.md).
* [Como usar Apache Spark em seu pipeline do Machine Learning com o Azure Synapse (versão prévia)](how-to-use-synapsesparkstep.md)
* [Treinar um modelo](how-to-set-up-training-targets.md).
