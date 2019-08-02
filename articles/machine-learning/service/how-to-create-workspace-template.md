---
title: Usar um modelo de Azure Resource Manager para criar um espaço de trabalho
titleSuffix: Azure Machine Learning service
description: Saiba como usar um modelo do Azure Resource Manager para criar um novo workspace de Serviço do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: 0e78d9cfce59615a53534fe9815205e39f64853d
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868829"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Usar um modelo de Azure Resource Manager para criar um espaço de trabalho para o serviço Azure Machine Learning

Neste artigo, você aprenderá várias maneiras de criar um workspace de Serviço do Azure Machine Learning usando modelos do Azure Resource Manager. Um modelo do Resource Manager facilita a criação de recursos como uma operação única e coordenada. Um modelo é um documento JSON que define os recursos necessários para uma implantação. Além disso, pode especificar os parâmetros de implantação. Os parâmetros são usados para fornecer valores de entrada ao usar o modelo.

Para saber mais, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura do Azure**. Se você não tiver uma, experimente a [versão paga ou gratuita do Serviço do Azure Machine Learning](https://aka.ms/AMLFree).

* Para usar um modelo a partir de uma CLI, você precisará do [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) ou da [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modelo do Resource Manager

O seguinte modelo do Resource Manager pode ser usado para criar um espaço de trabalho de serviço Azure Machine Learning e recursos do Azure associados:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Esse modelo cria os seguintes serviços do Azure:

* Grupo de recursos do Azure
* Conta de Armazenamento do Azure
* Cofre da Chave do Azure
* Azure Application Insights
* Registro de Contêiner do Azure
* Workspace do Azure Machine Learning

O grupo de recursos é o contêiner que retém os serviços. Os diversos serviços são exigidos pelo workspace do Azure Machine Learning.

O modelo de exemplo tem dois parâmetros:

* A **localização** onde o grupo de recursos e serviços serão criados.

    O modelo usará a localização selecionada para a maioria dos recursos. A exceção é o serviço do Application Insights que não está disponível em todos os locais de disponibilidade dos outros serviços. Se você selecionar uma localização onde não esteja disponível, o serviço será criado na localização Centro-Sul dos EUA.

* O **nome do workspace** é o nome amigável do workspace do Azure Machine Learning.

    Os nomes dos outros serviços são gerados aleatoriamente.

Para obter mais informações sobre modelos, consulte os artigos a seguir:

* [Criar modelos do Gerenciador de Recursos do Azure](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Implantar um aplicativo com o modelo do Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md)
* [Tipos de recursos do Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

1. Siga as etapas em [Implantar recursos do modelo personalizado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Ao acessar a tela __Editar modelo__, cole o modelo deste documento.
1. Selecione __Salvar__ para usar o modelo. Forneça as informações a seguir e concorde com os termos e condições listados:

   * Assinatura: Selecione a assinatura do Azure a ser usada para esses recursos.
   * Grupo de recursos: Selecione ou crie um grupo de recursos para conter os serviços.
   * Nome do workspace: O nome a ser usado para o workspace do Azure Machine Learning que será criado. O nome do workspace deverá ter entre 3 e 33 caracteres. E o nome poderá conter apenas caracteres alfanuméricos e '-'.
   * Localização: Selecione a localização onde os recursos serão criados.

     ![Os parâmetros do modelo no portal do Azure](media/how-to-create-workspace-template/template-parameters.png)

Para obter mais informações, consulte [Implantar recursos de modelo personalizado](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure

Este exemplo assume que você salvou o modelo em um arquivo nomeado `azuredeploy.json` no diretório atual:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Para obter mais informações, consulte [Implantar recursos com modelos do Resource Manager e do Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) e [implantar modelo do Resource Manager privado com o token SAS e o Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Usar a CLI do Azure

Este exemplo assume que você salvou o modelo em um arquivo nomeado `azuredeploy.json` no diretório atual:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

Para obter mais informações, consulte [Implantar recursos com modelos do Resource Manager e da CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md) e [implantar modelo do Resource Manager privado com o token SAS e a CLI do Azure](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Política de acesso de Azure Key Vault e modelos de Azure Resource Manager

Quando você usa um modelo de Azure Resource Manager para criar o espaço de trabalho e os recursos associados (incluindo Azure Key Vault), várias vezes. Por exemplo, usando o modelo várias vezes com os mesmos parâmetros como parte de um pipeline de implantação e integração contínua.

A maioria das operações de criação de recursos por meio de modelos é idempotente, mas Key Vault limpa as políticas de acesso toda vez que o modelo é usado. Limpar as políticas de acesso interrompe o acesso ao Key Vault para qualquer espaço de trabalho existente que o esteja usando. Por exemplo, parar/criar funcionalidades de Azure Notebooks VM pode falhar.  

Para evitar esse problema, recomendamos uma das seguintes abordagens:

*  Não implante o modelo mais de uma vez para os mesmos parâmetros. Ou exclua os recursos existentes antes de usar o modelo para recriá-los.
  
* Examine as políticas de acesso do Key Vault e use essas políticas para definir a propriedade accessPolicies do modelo.
* Verifique se o recurso de Key Vault já existe. Se tiver, não a recrie por meio do modelo. Por exemplo, adicione um parâmetro que permita desabilitar a criação do recurso de Key Vault se ele já existir.

## <a name="next-steps"></a>Próximas etapas

* [Implantar recursos com modelos do Resource Manager e API REST do Resource Manager](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Criar e implantar grupos de recursos do Azure por meio do Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
