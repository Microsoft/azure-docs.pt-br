---
title: 'Início Rápido: Implantar a API do Azure para FHIR usando um modelo do ARM'
description: Neste guia de início rápido, saiba como implantar a API do Azure para FHIR® (Fast Healthcare Interoperability Resources) usando um modelo do Azure Resource Manager (modelo do ARM).
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: cavoeg
ms.date: 10/06/2020
ms.openlocfilehash: 0cc31c26a71775dfdf3e9ee94852fda8af2c0cfc
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844532"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-api-for-fhir"></a>Início Rápido: Usar um modelo do ARM para implantar a API do Azure para FHIR

Neste guia de início rápido, você aprenderá a usar um modelo do ARM (modelo do Azure Resource Manager) para implantar a API do Azure para FHIR® (Fast Healthcare Interoperability Resources). Implante a API do Azure para FHIR por meio do portal do Azure, do PowerShell ou da CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure depois que você se conectar.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implante um serviço API do Azure para FHIR no Azure usando um modelo do ARM no portal do Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

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

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azure-api-for-fhir/).

:::code language="json" source="~/quickstart-templates/101-azure-api-for-fhir/azuredeploy.json":::

O modelo define um recurso do Azure:

* **Microsoft.HealthcareApis/services**

<!--

Replace the line above with the following line once https://docs.microsoft.com/azure/templates/microsoft.healthcareapis/services goes live:

* [**Microsoft.HealthcareApis/services**](/azure/templates/microsoft.healthcareapis/services)

-->

## <a name="deploy-the-template"></a>Implantar o modelo

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecione o seguinte link para implantar a API do Azure para FHIR usando o modelo do ARM no portal do Azure:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implante um serviço API do Azure para FHIR no Azure usando o modelo do ARM no portal do Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

Na página **Implantar a API do Azure para FHIR**:

1. Se desejar, altere a **Assinatura** do padrão para outra assinatura.

2. Para **Grupo de recursos**, selecione **Criar**, insira um nome para o novo grupo de recursos e, em seguida, selecione **OK**.

3. Se você criou um grupo de recursos, selecione uma **Região** para ele.

4. Insira um novo **Nome de Serviço** e escolha a **Localização** da API do Azure para FHIR. A localização pode ser igual ou diferente da região do grupo de recursos.

    :::image type="content" source="./media/fhir-paas-arm-template-quickstart/deploy-azure-api-fhir.png" alt-text="Implante a API do Azure para FHIR usando o modelo do ARM no portal do Azure.":::

5. Selecione **Examinar + criar**.

6. Leia os termos e condições e selecione **Criar**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Caso deseje executar os scripts do PowerShell localmente, primeiro, insira `Connect-AzAccount` para configurar suas credenciais do Azure.

Se o provedor de recursos das `Microsoft.HealthcareApis` ainda não estiver registrado para sua assinatura, registre-o com o código interativo a seguir. Para executar o código em Azure Cloud Shell, selecione **Experimentar** no canto superior de qualquer bloco de código.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Use o código a seguir para implantar o serviço API do Azure para FHIR usando o modelo do ARM. O código solicitará o nome do novo serviço do FHIR, o nome de um novo grupo de recursos e as localizações de cada um deles.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure API for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json `
    -serviceName $serviceName `
    -location $serviceLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Se o provedor de recursos das `Microsoft.HealthcareApis` ainda não estiver registrado para sua assinatura, registre-o com o código interativo a seguir. Para executar o código em Azure Cloud Shell, selecione **Experimentar** no canto superior de qualquer bloco de código.

```azurecli-interactive
az extension add --name healthcareapis
```

Use o código a seguir para implantar o serviço API do Azure para FHIR usando o modelo do ARM. O código solicitará o nome do novo serviço do FHIR, o nome de um novo grupo de recursos e as localizações de cada um deles.

```azurecli-interactive
read -p "Enter a name for the new Azure API for FHIR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
params='serviceName='$serviceName' location='$serviceLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure API for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> A implantação leva alguns minutos para ser concluída. Anote os nomes do serviço API do Azure para FHIR e do grupo de recursos, que você usará para examinar os recursos implantados posteriormente.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estas etapas para obter uma visão geral do novo serviço API do Azure para FHIR:

1. No [portal do Azure](https://portal.azure.com), procure e selecione **API do Azure para FHIR**.

2. Na lista do FHIR, escolha o novo serviço. A página **Visão Geral** do novo serviço API do Azure para FHIR será exibida.

3. Para validar se a nova conta da API do FHIR foi provisionada, selecione o link ao lado de **Ponto de extremidade dos metadados do FHIR** para buscar a instrução da funcionalidade da API do FHIR. O link tem o formato `https://<service-name>.azurehealthcareapis.com/metadata`. Se a conta tiver sido provisionada, um arquivo JSON grande será exibido.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Execute o código interativo a seguir para ver detalhes sobre o serviço API do Azure para FHIR. Você precisará inserir o nome do novo serviço e do grupo de recursos.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned"

$requestUri="https://" + $serviceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Execute o código interativo a seguir para ver detalhes sobre o serviço API do Azure para FHIR. Você precisará inserir o nome do novo serviço e do grupo de recursos.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$serviceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
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

Para obter um tutorial passo a passo que orienta você durante o processo de criação de um modelo do ARM, confira o [tutorial para criar e implantar seu primeiro modelo do ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você implantou a API do Azure para FHIR em sua assinatura. Para definir configurações adicionais em sua API do Azure para FHIR, vá para o guia de instruções de configurações adicionais. Se estiver pronto para começar a usar a API do Azure para FHIR, leia mais sobre como registrar aplicativos.

>[!div class="nextstepaction"]
>[Configurações adicionais na API do Azure para FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Visão geral sobre como Registrar os Aplicativos](fhir-app-registration.md)
