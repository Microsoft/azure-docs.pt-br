---
title: 'Início Rápido: Criar um Serviço do Azure SignalR – Modelo do ARM'
description: Neste guia de Início Rápido, aprenda como criar um Serviço do Azure SignalR usando um modelo do Azure Resource Manager (modelo ARM).
author: sffamily
ms.author: zhshang
ms.date: 10/02/2020
ms.topic: quickstart
ms.service: signalr
ms.custom:
- subject-armqs
- devx-track-azurecli
- mode-arm
ms.openlocfilehash: 075915f2a06e29eb5b80425913c6e7a2c476ff99
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536524"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>Início Rápido: Usar um modelo do ARM para implantar o Serviço do Azure SignalR

Este guia de início rápido descreve como usar um modelo do Azure Resource Manager (modelo do ARM) para criar um Serviço do Azure SignalR. Você pode implantar o Serviço do Azure SignalR por meio do portal do Azure, do PowerShell ou da CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure depois que você se conectar.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Botão para implantar o Serviço do Azure SignalR no Azure usando um modelo do ARM no portal do Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)

Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/).
* Se você quiser executar o código localmente, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[CLI](#tab/CLI)

* Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/).
* Caso deseje executar o código localmente:
    * Um shell Bash (como o Git Bash, que está incluído no [Git para Windows](https://gitforwindows.org)).
    * [CLI do Azure](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-signalr/).

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

O modelo define um recurso do Azure:

* [**Microsoft.SignalRService/SignalR**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>Implantar o modelo

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecione o seguinte link para implantar o Serviço do Azure SignalR usando o modelo do ARM no portal do Azure:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Botão para implantar o Serviço do Azure SignalR no Azure usando o modelo do ARM no portal do Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

Na página **Implantar um Serviço do Azure SignalR**:

1. Se desejar, altere a **Assinatura** do padrão.

2. Para **Grupo de recursos**, selecione **Criar**, insira um nome para o novo grupo de recursos e, em seguida, selecione **OK**.

3. Se você criou um grupo de recursos, selecione uma **Região** para ele.

4. Se desejar, insira um novo **Nome** e o **Local** (como **eastus2**) do Serviço do Azure SignalR. Se você não especificar um nome, ele será gerado automaticamente. A localização do Serviço do Azure SignalR pode ser igual ou diferente da região do grupo de recursos. Se você não especificar um local, ele será definido como a mesma região que o grupo de recursos.

5. Escolha o **Tipo de Preço** (**Free_F1** ou **Standard_S1**), insira a **Capacidade** (número de unidades do SignalR) e escolha um **Modo de Serviço** de **Padrão** (requer o servidor de hub), **Sem servidor** (não permite nenhuma conexão com o servidor) ou **Clássico** (roteado para o servidor de hub somente se o hub tiver conexão com o servidor). Em seguida, escolha se deseja **Habilitar os Logs de Conectividade** ou **Habilitar os Logs de Mensagens**.

    > [!NOTE]
    > Para o tipo de preço **Free_F1**, a capacidade é limitada a uma unidade.

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Captura de tela do modelo do ARM para criar um Serviço do Azure SignalR no portal do Azure.":::

6. Selecione **Examinar + criar**.

7. Leia os termos e condições e selecione **Criar**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Caso deseje executar os scripts do PowerShell localmente, primeiro, insira `Connect-AzAccount` para configurar suas credenciais do Azure.

Use o código a seguir para implantar o Serviço do Azure SignalR usando o modelo do ARM. O código solicita os seguintes itens:

* O nome e a região do novo Serviço do Azure SignalR
* O nome e a região de um novo grupo de recursos
* O tipo de preço do Azure (**Free_F1** ou **Standard_S1**)
* A capacidade da unidade do SignalR (1, 2, 5, 10, 20, 50 ou 100)
  > [!NOTE]
  > Para o tipo de preço **Free_F1**, a capacidade é limitada a uma unidade.
* O modo de serviço: **Padrão** para exigir um servidor de hub, **Sem servidor** para proibir qualquer conexão de servidor ou **Clássico** para fazer o roteamento para um servidor de hub somente se o hub tiver uma conexão de servidor
* Se é para habilitar logs para conectividade ou mensagens (**true** ou **false**)

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure SignalR Service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

$priceTier = Read-Host -Prompt "Enter the pricing tier (Free_F1 or Standard_S1)"
$unitCapacity = Read-Host -Prompt "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100)"
$servicingMode = Read-Host -Prompt "Enter the service mode (Default, Serverless, or Classic)"
$enableConnectionLogs = Read-Host -Prompt "Specify whether to enable connectivity logs (true or false)"
$enableMessageLogs = Read-Host -Prompt "Specify whether to enable messaging logs (true or false)"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion

$paramObjHashTable = @{
    name = $serviceName
    location = $serviceLocation
    pricingTier = $priceTier
    capacity = [int]$unitCapacity
    serviceMode = $servicingMode
    enableConnectivityLogs = $enableConnectionLogs
    enableMessagingLogs = $enableMessageLogs
}

Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure SignalR Service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateParameterObject $paramObjHashTable `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Use o código a seguir para implantar o Serviço do Azure SignalR usando o modelo do ARM. O código solicita os seguintes itens:

* O nome e a região do novo Serviço do Azure SignalR
* O nome e a região de um novo grupo de recursos
* O tipo de preço do Azure (**Free_F1** ou **Standard_S1**)
* A capacidade da unidade do SignalR (1, 2, 5, 10, 20, 50 ou 100)
    > [!NOTE]
    > Para o tipo de preço **Free_F1**, a capacidade é limitada a uma unidade.
* O modo de serviço: **Padrão** para exigir um servidor de hub, **Sem servidor** para proibir qualquer conexão de servidor ou **Clássico** para fazer o roteamento para um servidor de hub somente se o hub tiver uma conexão de servidor
* Se é para habilitar logs para conectividade ou mensagens (**true** ou **false**)

```azurecli-interactive
read -p "Enter a name for the new Azure SignalR Service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter the pricing tier (Free_F1 or Standard_S1): " priceTier &&
read -p "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100): " unitCapacity &&
read -p "Enter the service mode (Default, Serverless, or Classic): " servicingMode &&
read -p "Specify whether to enable connectivity logs (true or false): " enableConnectionLogs &&
read -p "Specify whether to enable messaging logs (true or false): " enableMessageLogs &&
params='name='$serviceName' location='$serviceLocation' pricingTier='$priceTier' capacity='$unitCapacity' serviceMode='$servicingMode' enableConnectivityLogs='$enableConnectionLogs' enableMessagingLogs='$enableMessageLogs &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure SignalR Service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> A implantação pode levar alguns minutos para ser concluída. Anote os nomes do Serviço do Azure SignalR e do grupo de recursos, que você usará para examinar os recursos implantados posteriormente.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estas etapas para obter uma visão geral do novo Serviço do Azure SignalR:

1. No [portal do Azure](https://portal.azure.com), pesquise e selecione **SignalR**.

2. Na lista do SignalR, selecione o novo serviço. A página **Visão Geral** do novo Serviço do Azure SignalR é exibida.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Execute o código interativo a seguir para ver detalhes sobre o Serviço do Azure SignalR. Você precisará inserir o nome do novo serviço e do grupo de recursos.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Execute o código interativo a seguir para ver detalhes sobre o Serviço do Azure SignalR. Você precisará inserir o nome do novo serviço e do grupo de recursos.

```azurecli-interactive
read -p "Enter the name of your Azure SignalR Service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos, que excluirá os recursos no grupo de recursos.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://portal.azure.com) pesquise e selecione **Grupos de recursos**.

2. Na lista grupo de recursos, escolha o nome do seu grupo de recursos.

3. Na página **Visão geral** do grupo de recursos, selecione **Excluir grupo de recursos**.

4. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Excluir**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Próximas etapas

Para obter um tutorial passo a passo que orienta você durante o processo de criação de um modelo do ARM, confira:

> [!div class="nextstepaction"]
> [ Tutorial: Criar e implantar seu primeiro modelo do Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
