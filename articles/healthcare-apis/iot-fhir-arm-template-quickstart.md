---
title: 'Início rápido: Implantar o Conector IoT do Azure para FHIR (versão prévia) usando um modelo do ARM'
description: Neste guia de início rápido, aprenda a implantar um Conector IoT do Azure para FHIR (versão prévia) usando um modelo do ARM (Azure Resource Manager).
author: rbhaiya
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.author: rabhaiya
ms.date: 01/21/2021
ms.openlocfilehash: b45c63031596c96886a96a21bf693803088cc006
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744924"
---
# <a name="quickstart-use-an-azure-resource-manager-arm-template-to-deploy-azure-iot-connector-for-fhir-preview"></a>Início rápido: Usar um modelo de ARM (Azure Resource Manager) para implantar o Conector IoT do Azure para FHIR (versão prévia)

Neste guia de início rápido, você aprenderá a usar um modelo do ARM (Azure Resource Manager) para implantar o Conector IoT do Azure para FHIR&#174; (Fast Healthcare Interoperability Resources)*, um recurso da API do Azure para FHIR. Para implantar uma instância de trabalho do Conector IoT do Azure para FHIR, esse modelo também implanta um serviço da API do Azure para FHIR pai e um aplicativo Azure IoT Central que exporta telemetria de um simulador de dispositivo para o Conector IoT do Azure para FHIR. Você pode executar o modelo do ARM para implantar o Conector IoT do Azure para FHIR por meio do portal do Azure, do PowerShell ou da CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure depois que você se conectar.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implante um Conector IoT do Azure para FHIR no Azure usando um modelo do ARM no portal do Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

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

O [modelo](https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json) define os seguintes recursos do Azure:

* **Microsoft.HealthcareApis/services**
* **Microsoft.HealthcareApis/services/iomtconnectors**
* **Microsoft.IoTCentral/IoTApps**

## <a name="deploy-the-template"></a>Implantar o modelo

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecione o seguinte link para implantar o Conector IoT do Azure para FHIR usando o modelo do ARM no portal do Azure:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implante um serviço do Conector IoT do Azure para FHIR no Azure usando o modelo do ARM no portal do Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

Na página **Implantar a API do Azure para FHIR**:

1. Se desejar, altere a **Assinatura** do padrão para outra assinatura.

2. Para **Grupo de recursos**, selecione **Criar**, insira um nome para o novo grupo de recursos e, em seguida, selecione **OK**.

3. Se você criou um grupo de recursos, selecione uma **Região** para ele.

4. Insira um nome para sua nova instância da API do Azure para FHIR no **Nome do Serviço de FHIR**.

5. Escolha a **Localização** para sua API do Azure para FHIR. A localização pode ser igual ou diferente da região do grupo de recursos.

6. Forneça um nome para sua instância do Conector IoT do Azure para FHIR em **Nome do Conector IoT**.

7. Forneça um nome para uma conexão criada no Conector IoT do Azure para FHIR em **Nome da Conexão**. Essa conexão é usada pelo aplicativo Azure IoT Central para enviar por push a telemetria de dispositivo simulado para o Conector IoT do Azure para FHIR.

8. Insira um nome para seu novo aplicativo Azure IoT Central em **Nome do IoT Central**. Esse aplicativo usará o modelo de *Monitoramento de paciente contínuo* para simular um dispositivo.

9. Escolha a localização do seu aplicativo IoT Central no menu suspenso **Localização do IoT Central**. 

10. Selecione **Examinar + criar**.

11. Leia os termos e condições e selecione **Criar**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Caso deseje executar os scripts do PowerShell localmente, primeiro, insira `Connect-AzAccount` para configurar suas credenciais do Azure.

Se o provedor de recursos das `Microsoft.HealthcareApis` ainda não estiver registrado para sua assinatura, registre-o com o código interativo a seguir. Para executar o código em Azure Cloud Shell, selecione **Experimentar** no canto superior de qualquer bloco de código.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Se o provedor de recursos das `Microsoft.IoTCentral` ainda não estiver registrado para sua assinatura, registre-o com o código interativo a seguir. Para executar o código em Azure Cloud Shell, selecione **Experimentar** no canto superior de qualquer bloco de código.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.IoTCentral
```

Use o código a seguir para implantar o serviço Conector IoT do Azure para FHIR usando o modelo do ARM.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$fhirServiceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$location = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$iotConnectorName = Read-Host -Prompt "Enter a name for the new Azure IoT Connector for FHIR"
$connectionName = Read-Host -Prompt "Enter a name for the connection with Azure IoT Connector for FHIR"
$iotCentralName = Read-Host -Prompt "Enter a name for the new Azure IoT Central Application"
$iotCentralLocation = Read-Host -Prompt "Enter a location for the new Azure IoT Central Application"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure IoT Connector for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json `
    -fhirServiceName $fhirServiceName `
    -location $location
    -iotConnectorName $iotConnectorName
    -connectionName $connectionName
    -iotCentralName $iotCentralName
    -iotCentralLocation $iotCentralLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Se o provedor de recursos das `Microsoft.HealthcareApis` ainda não estiver registrado para sua assinatura, registre-o com o código interativo a seguir. Para executar o código em Azure Cloud Shell, selecione **Experimentar** no canto superior de qualquer bloco de código.

```azurecli-interactive
az extension add --name healthcareapis
```

Se o provedor de recursos das `Microsoft.IoTCentral` ainda não estiver registrado para sua assinatura, registre-o com o código interativo a seguir.

```azurecli-interactive
az extension add --name azure-iot
```

Use o código a seguir para implantar o serviço Conector IoT do Azure para FHIR usando o modelo do ARM.

```azurecli-interactive
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter a name for the new Azure API for FHIR service: " fhirServiceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " location &&
read -p "Enter a name for the new Azure IoT Connector for FHIR: " iotConnectorName &&
read -p "Enter a name for the connection with Azure IoT Connector for FHIR: " connectionName &&
read -p "Enter a name for the new Azure IoT Central Application: " iotCentralName &&
read -p "Enter a location for the new Azure IoT Central Application: " iotCentralLocation &&

params='fhirServiceName='$fhirServiceName' location='$location' iotConnectorName='$iotConnectorName' connectionName='$connectionName' iotCentralName='$iotCentralName' iotCentralLocation='$iotCentralLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure IoT Connector for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> A implantação leva alguns minutos para ser concluída. Anote os nomes do serviço API do Azure para FHIR, do aplicativo Azure IoT Central e do grupo de recursos, que você usará para examinar os recursos implantados posteriormente.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estas etapas para obter uma visão geral do novo serviço API do Azure para FHIR:

1. No [portal do Azure](https://portal.azure.com), procure e selecione **API do Azure para FHIR**.

2. Na lista do FHIR, escolha o novo serviço. A página **Visão Geral** do novo serviço API do Azure para FHIR será exibida.

3. Para validar se a nova conta da API do FHIR foi provisionada, selecione o link ao lado de **Ponto de extremidade dos metadados do FHIR** para buscar a instrução da funcionalidade da API do FHIR. O link tem o formato `https://<service-name>.azurehealthcareapis.com/metadata`. Se a conta tiver sido provisionada, um arquivo JSON grande será exibido.

4. Para confirmar se o novo Conector IoT do Azure para FHIR está provisionado, selecione **Conector IoT (versão prévia)** no menu de navegação esquerdo para abrir a página **Conectores IoT**. A página precisa mostrar o Conector IoT do Azure para FHIR provisionado com o valor *Status* como *Online*, o valor *Conexões* como *1* e *Mapeamento de dispositivo* e *Mapeamento FHIR* devem mostrar o ícone *Êxito*.

5. No [portal do Azure](https://portal.azure.com), procure e selecione **Aplicativos IoT Central**.

6. Na lista de aplicativos IoT Central, selecione o novo serviço. A página **Visão geral** do novo aplicativo IoT Central é exibida.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Execute o código interativo a seguir para ver detalhes sobre o serviço API do Azure para FHIR. Você precisará inserir o nome do novo serviço FHIR e do grupo de recursos.

```azurepowershell-interactive
$fhirServiceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new FHIR service has been provisioned"

$requestUri="https://" + $fhirServiceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

> [!NOTE]
> O Conector IoT do Azure para FHIR não fornece comandos do PowerShell no momento. Para validar se o Conector IoT do Azure para FHIR foi provisionado corretamente, use o processo de validação fornecido na guia **Portal**.

Execute o código interativo a seguir para ver detalhes sobre o aplicativo Azure IoT Central. Você precisará inserir o nome do novo aplicativo IoT Central e do grupo de recursos.

```azurepowershell-interactive
$iotCentralName = Read-Host -Prompt "Enter the name of your Azure IoT Central application"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName" -Verbose
Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName
```

# <a name="cli"></a>[CLI](#tab/CLI)

Execute o código interativo a seguir para ver detalhes sobre o serviço API do Azure para FHIR. Você precisará inserir o nome do novo serviço FHIR e do grupo de recursos.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " fhirServiceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$fhirServiceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

> [!NOTE]
> O Conector IoT do Azure para FHIR não fornece comandos da CLI no momento. Para validar se o Conector IoT do Azure para FHIR foi provisionado corretamente, use o processo de validação fornecido na guia **Portal**.

Execute o código interativo a seguir para ver detalhes sobre o aplicativo Azure IoT Central. Você precisará inserir o nome do novo aplicativo IoT Central e do grupo de recursos.

```azurecli-interactive
read -p "Enter the name of your IoT Central application: " iotCentralName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az iot central app show -g $resourceGroupName -n $iotCentralName" &&
az iot central app show -g $resourceGroupName -n $iotCentralName &&
```

---

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Conectar seus dados de IoT com o Conector IoT do Azure para FHIR (versão prévia)
> [!IMPORTANT]
> O modelo de mapeamento de Dispositivo fornecido neste guia foi criado para funcionar com a Exportação de Dados (herdada) no IoT Central.

No momento, o aplicativo IoT Central não fornece o modelo do ARM nem comandos do PowerShell e da CLI para definir a exportação de dados. Portanto, siga as instruções abaixo usando o portal do Azure.  

Depois de implantar seu aplicativo do IoT Central, seus dois dispositivos simulados prontos para uso começarão a gerar telemetria. Para este tutorial, vamos ingerir a telemetria do simulador *Patch de Sinais Vitais Inteligente* no FHIR por meio do Conector IoT do Azure para FHIR. Para exportar seus dados de IoT para o Conector IoT do Azure para FHIR, será interessante [configurar uma exportação de dados (herdada) no IoT Central](../iot-central/core/howto-export-data-legacy.md). Na página Exportação de dados (herdada):
- Escolha *Hubs de Eventos do Azure* como o destino de exportação.
- Selecione o valor *Usar uma cadeia de conexão* para o campo **Namespace dos Hubs de Eventos**.
- Forneça a cadeia de conexão do Conector IoT do Azure para FHIR obtida em uma etapa anterior para o campo **Cadeia de Conexão**.
- Mantenha a opção **Telemetria** *Ativada* para o campo **Dados a serem exportados**.

---

## <a name="view-device-data-in-azure-api-for-fhir"></a>Ver os dados do dispositivo na API do Azure para FHIR

Veja os recursos de Observação com base em FHIR criados pelo Conector IoT do Azure para FHIR em seu servidor FHIR usando o Postman. Configure o [Postman para acessar a API do Azure para FHIR](access-fhir-postman-tutorial.md) e faça uma solicitação `GET` a `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` para ver os recursos de Observação do FHIR com o valor de frequência cardíaca.

> [!TIP]
> Verifique se o usuário tem acesso apropriado ao plano de dados da API do Azure para FHIR. Use o [Azure RBAC (controle de acesso baseado em função do Azure)](configure-azure-rbac.md) para atribuir as funções de plano de dados necessárias.

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

Neste guia de início rápido, você implantou o recurso do Conector IoT do Azure para FHIR no recurso da API do Azure para FHIR. Selecione uma das seguintes etapas abaixo para saber mais sobre o Conector IoT do Azure para FHIR:

entenda as diferentes fases do fluxo de dados no Conector IoT do Azure para FHIR.

>[!div class="nextstepaction"]
>[Fluxo de dados do Conector IoT do Azure para FHIR](iot-data-flow.md)

Saiba como configurar o Conector IoT usando modelos de mapeamento FHIR e do dispositivo.

>[!div class="nextstepaction"]
>[Modelos de mapeamento do Conector IoT do Azure para FHIR](iot-mapping-templates.md)

*No portal do Azure, o Conector IoT do Azure para FHIR é chamado de Conector IoT (versão prévia). FHIR é uma marca registrada da HL7, usada com permissão da HL7.
