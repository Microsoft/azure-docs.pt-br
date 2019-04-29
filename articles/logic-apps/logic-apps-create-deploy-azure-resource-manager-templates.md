---
title: Implantar aplicativos lógicos com modelos do Azure Resource Manager – aplicativos lógicos do Azure
description: Implantar aplicativos lógicos usando modelos do Azure Resource Manager
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: 21603ff213bc8babb4145209a76aee0b4150cc12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681722"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>Implantar aplicativos lógicos com modelos do Azure Resource Manager

Depois de criar um modelo do Azure Resource Manager para implantar seu aplicativo lógico, você pode implantar seu modelo das seguintes maneiras:

* [Portal do Azure](#portal)
* [PowerShell do Azure](#powershell)
* [CLI do Azure](#cli)
* [APIs REST do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Pipelines do Azure de DevOps do Azure](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Implantar por meio do portal do Azure

Para implantar automaticamente um modelo de aplicativo lógico do Azure, você pode escolher o seguinte **implantar no Azure** botão, que você entra no portal do Azure e solicita informações sobre seu aplicativo lógico. Em seguida, você pode fazer as alterações necessárias para o modelo de aplicativo lógico ou os parâmetros.

[![Implantar no Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Por exemplo, você será solicitado para que essas informações depois de entrar no portal do Azure:

* Nome da assinatura do Azure
* Grupo de recursos que você deseja usar
* Local do aplicativo lógico
* O nome do seu aplicativo lógico
* Um URI de teste
* Aceitação dos termos e das condições especificados

Para obter mais informações, consulte [implantar recursos com modelos do Azure Resource Manager e o portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

## <a name="authorize-oauth-connections"></a>Autorizar Conexões OAuth

Após a implantação, o aplicativo lógico funciona de ponta a ponta com parâmetros válidos. No entanto, ainda é necessário autorizar conexões OAuth para gerar um token de acesso válido. Para implantações automatizadas, você pode usar um script que dá consentimento para cada conexão OAuth, como este [script de exemplo no projeto do GitHub LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth). Você também pode autorizar conexões OAuth por meio do portal do Azure ou no Visual Studio, abra seu aplicativo lógico no Designer de aplicativos lógicos.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Implantar com o Azure PowerShell

Para implantar em um determinado *grupo de recursos do Azure*, use este comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

Para implantar em uma assinatura do Azure específica, use este comando:

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)
* [`New-AzDeployment`](/powershell/module/az.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

Para implantar em um determinado *grupo de recursos do Azure*, use este comando:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Para implantar em uma assinatura do Azure específica, use este comando:

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Para saber mais, consulte esses tópicos: 

* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Implantar com DevOps do Azure

Para implantar modelos de aplicativo lógico e gerenciar ambientes, as equipes geralmente usam uma ferramenta, como [Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) na [DevOps do Azure](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Pipelines do Azure fornece um [tarefas de implantação do grupo de recursos do Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) que você pode adicionar a qualquer build ou pipeline da versão.
Autorização implantar e gerar o pipeline de lançamento, você também precisa de um Azure Active Directory (AD) [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md). Saiba mais sobre [usando as entidades de serviço com Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

Aqui estão as etapas gerais de alto nível para usar Pipelines do Azure:

1. Em Pipelines do Azure, crie um pipeline vazio.

1. Escolha os recursos que necessários para o pipeline, como seu modelo de aplicativo lógico e os arquivos de parâmetros de modelo, que gerar manualmente ou como parte do processo de compilação.

1. Para o trabalho de agente, localize e adicione a **implantação de grupo de recursos do Azure** tarefa.

   ![Adicionar tarefa "Implantação do grupo de recursos do Azure"](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. Configurar com um [entidade de serviço](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

1. Adicione referências ao seu modelo de aplicativo lógico e os arquivos de parâmetros de modelo.

1. Continue a compilar as etapas no processo de versão para quaisquer outros ambientes, testes automatizados ou aprovadores, conforme necessário.

## <a name="get-support"></a>Obter suporte

Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Monitorar aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md)
