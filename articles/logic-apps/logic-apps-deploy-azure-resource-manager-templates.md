---
title: Implantar modelos de aplicativo lógico
description: Saiba como implantar modelos de Azure Resource Manager criados para aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/25/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5f5db3fd88f04e7fe569cd675936445dcf730288
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705329"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Implantar modelos do Azure Resource Manager para Aplicativos Lógicos do Azure

Depois de criar um modelo de Azure Resource Manager para seu aplicativo lógico, você pode implantar o modelo das seguintes maneiras:

* [Portal do Azure](#portal)
* [Visual Studio](#visual-studio)
* [PowerShell do Azure](#powershell)
* [CLI do Azure](#cli)
* [API REST do Azure Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Implantar por meio do portal do Azure

Para implantar automaticamente um modelo de aplicativo lógico no Azure, você pode escolher o botão **implantar no Azure** a seguir, que entra no portal do Azure e solicita informações sobre seu aplicativo lógico. Em seguida, você pode fazer as alterações necessárias no modelo ou parâmetros do aplicativo lógico.

[![Implantar no Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Por exemplo, as seguintes informações serão solicitadas depois que você entrar no portal do Azure:

* Nome da assinatura do Azure
* Grupo de recursos que você deseja usar
* Local do aplicativo lógico
* O nome do seu aplicativo lógico
* Um URI de teste
* Aceitação dos termos e das condições especificados

Para saber mais, consulte esses tópicos:

* [Visão geral: automatizar a implantação para aplicativos lógicos com modelos de Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)
* [Implantar recursos com modelos de Azure Resource Manager e o portal do Azure](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Implantação com o Visual Studio

Para implantar um modelo de aplicativo lógico de um projeto de grupo de recursos do Azure que você criou usando o Visual Studio, siga estas [etapas para implantar manualmente seu aplicativo lógico](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) no Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Implantar com o Azure PowerShell

Para implantar em um *grupo de recursos específico do Azure*, use o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Para saber mais, consulte esses tópicos:

* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

Para implantar em um *grupo de recursos específico do Azure*, use o seguinte comando:

```azurecli
az deployment group create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Para saber mais, consulte esses tópicos:

* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/templates/deploy-cli.md)
* [`az deployment group create`](/cli/azure/deployment/group#az_deployment_group_create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Implantar com o Azure DevOps

Para implantar modelos de aplicativo lógico e gerenciar ambientes, as equipes normalmente usam uma ferramenta como [Azure pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) no [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines fornece uma [tarefa de implantação de grupo de recursos do Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) que você pode adicionar a qualquer pipeline de compilação ou versão. Para autorização para implantar e gerar o pipeline de lançamento, você também precisa de uma entidade de [serviço](../active-directory/develop/app-objects-and-service-principals.md)Azure Active Directory (AD). Saiba mais sobre como [usar entidades de serviço com Azure pipelines](/azure/devops/pipelines/library/connect-to-azure).

Para obter mais informações sobre integração contínua e implantação contínua (CI/CD) para modelos de Azure Resource Manager com Azure Pipelines, consulte estes tópicos e exemplos:

* [Integrar modelos do Resource Manager com o Azure Pipelines](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Tutorial: Integração contínua dos modelos do Azure Resource Manager com o Azure Pipelines](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [Exemplo: conectar-se a filas do barramento de serviço do Azure de aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: conectar-se a contas de armazenamento do Azure de aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: configurar uma ação de aplicativo de funções para aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: conectar-se a uma conta de integração de aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Exemplo: orquestrar Azure Pipelines usando os aplicativos lógicos do Azure](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Aqui estão as etapas gerais de alto nível para usar Azure Pipelines:

1. Em Azure Pipelines, crie um pipeline vazio.

1. Escolha os recursos necessários para o pipeline, como o modelo do aplicativo lógico e os arquivos de parâmetros de modelo, que você gera manualmente ou como parte do processo de compilação.

1. Para seu trabalho do Agent, localize e adicione a tarefa de **implantação do grupo de recursos do Azure** .

   ![Adicionar a tarefa "implantação de grupo de recursos do Azure"](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Configure com uma [entidade de serviço](/azure/devops/pipelines/library/connect-to-azure).

1. Adicione referências ao modelo de aplicativo lógico e aos arquivos de parâmetros de modelo.

1. Continue a compilar as etapas no processo de versão para quaisquer outros ambientes, testes automatizados ou aprovadores, conforme necessário.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autorizar Conexões OAuth

Após a implantação, seu aplicativo lógico funciona de ponta a ponta com parâmetros válidos, mas para gerar tokens de acesso válidos para [autenticar suas credenciais](../active-directory/develop/authentication-vs-authorization.md), você ainda precisa autorizar ou usar conexões de OAuth preautorizadas. No entanto, você só precisa implantar e autenticar recursos de conexão de API uma vez, o que significa que você não precisa incluir esses recursos de conexão em implantações subsequentes, a menos que precise atualizar as informações de conexão. Se você usar uma integração contínua e um pipeline de implantação contínua, implantaria apenas os recursos de aplicativos lógicos atualizados e não precisará reautorizar as conexões todas as vezes.

Aqui estão algumas sugestões para lidar com as conexões de autorização:

* Pré-autorizar e compartilhar recursos de conexão de API entre aplicativos lógicos que estão na mesma região. Existem conexões de API como recursos do Azure independentemente dos aplicativos lógicos. Embora os aplicativos lógicos tenham dependências de recursos de conexão de API, os recursos de conexão de API não têm dependências em aplicativos lógicos e permanecem depois que você exclui os aplicativos lógicos dependentes. Além disso, os aplicativos lógicos podem usar conexões de API que existem em outros grupos de recursos. No entanto, o designer do aplicativo lógico dá suporte à criação de conexões de API somente no mesmo grupo de recursos que seus aplicativos lógicos.

  > [!NOTE]
  > Se você estiver pensando em compartilhar conexões de API, certifique-se de que sua solução possa [lidar com possíveis problemas de limitação](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling). A limitação ocorre no nível de conexão, portanto, reutilizar a mesma conexão entre vários aplicativos lógicos pode aumentar o potencial para problemas de limitação.

* A menos que seu cenário envolva serviços e sistemas que exigem autenticação multifator, você pode usar um script do PowerShell para fornecer consentimento para cada conexão OAuth executando um trabalho de integração contínua como uma conta de usuário normal em uma máquina virtual que tenha sessões de navegador ativas com as autorizações e o consentimento já fornecido. Por exemplo, você pode adaptar o script de exemplo fornecido pelo [projeto LogicAppConnectionAuth no repositório GitHub de aplicativos lógicos](https://github.com/logicappsio/LogicAppConnectionAuth).

* Autorize manualmente as conexões OAuth abrindo seu aplicativo lógico no designer de aplicativo lógico, seja na portal do Azure ou no Visual Studio.

* Se você usar uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (AD do Azure) em vez de autorizar conexões, saiba como [especificar parâmetros de entidade de serviço em seu modelo de aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Monitorar aplicativos lógicos](../logic-apps/monitor-logic-apps.md)
