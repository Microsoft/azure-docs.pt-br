---
title: Criar um Hub IoT do Azure usando um modelo (PowerShell) | Microsoft Docs
description: Como usar um modelo do Azure Resource Manager para criar um IoT Hub com o Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: eefa17b699a0599d2d3323409cafddd1d0984c61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440311"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Criar um Hub IoT usando um modelo do Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Saiba como usar um modelo do Azure Resource Manager para criar um IoT Hub e um grupo de consumidores. Os modelos do Resource Manager são arquivos JSON que definem os recursos necessários para implantar sua solução. Para obter mais informações sobre como desenvolver modelos do Resource Manager, consulte [documentação do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

O modelo do Resource Manager usado neste início rápido provém [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Veja uma cópia do modelo:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

O modelo cria um hub Iot do Azure com três pontos de extremidade (hub de eventos, nuvem para o dispositivo e sistema de mensagens) e um grupo de consumidores. Para obter mais exemplos de modelo, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). O esquema de modelo do Iot Hub pode ser encontrado [aqui](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Há vários métodos para implantar um modelo.  Usar o Azure PowerShell neste tutorial.

Para executar o script do PowerShell, selecione **Experimente** para abrir o Azure Cloud shell. Para colar o script, o shell com o botão direito e, em seguida, selecione Colar:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Como você pode ver no script do PowerShell, o modelo usado é de modelos de início rápido do Azure. Para usar seu próprio, você precisa primeiro carregar o arquivo de modelo para o Cloud shell e, em seguida, usar o `-TemplateFile` para especificar o nome do arquivo.  Por exemplo, consulte [implantar o modelo](../azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou um hub IoT usando um modelo do Azure Resource Manager, convém explorar ainda mais:

* Leia sobre as funcionalidades da [API REST do provedor de recursos Hub IoT][lnk-rest-api].
* Leia a [Visão geral do Azure Resource Manager][lnk-azure-rm-overview] para saber mais sobre os recursos do Azure Resource Manager.
* Para obter a sintaxe e as propriedades de JSON a serem usadas em modelos, consulte [Tipos de recurso Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Para saber mais sobre como desenvolver para o Hub IoT, veja os seguintes artigos:

* [Introdução ao SDK de C][lnk-c-sdk]
* [SDKs do Azure IoT][lnk-sdks]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Implantação do IA em dispositivos de borda com o Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
