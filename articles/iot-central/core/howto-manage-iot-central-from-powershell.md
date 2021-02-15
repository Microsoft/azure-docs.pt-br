---
title: Gerenciar o IoT Central do Azure PowerShell | Microsoft Docs
description: Este artigo descreve como criar e gerenciar seus aplicativos de IoT Central do Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
manager: philmea
ms.openlocfilehash: 832147fd06dbc7439500b911efbbde6d9b1d81fd
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123055"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Gerenciar o IoT Central do Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerenciar IoT Central aplicativos no site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) , você pode usar [Azure PowerShell](/powershell/azure/) para gerenciar seus aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você preferir executar o Azure PowerShell em seu computador local, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Quando você executar o Azure PowerShell localmente, use o cmdlet **AzAccount Connect** para entrar antes de experimentar os cmdlets neste artigo.

> [!TIP]
> Se você precisar executar seus comandos do PowerShell em uma assinatura do Azure diferente, consulte [alterar a assinatura ativa](/powershell/azure/manage-subscriptions-azureps?view=azps-3.4.0#change-the-active-subscription).

## <a name="install-the-iot-central-module"></a>Instalar o módulo do IoT Central

Execute o seguinte comando para verificar se o [módulo do IoT Central](/powershell/module/az.iotcentral/) está instalado em seu ambiente do PowerShell:

```powershell
Get-InstalledModule -name Az.I*
```

Se a lista de módulos instalados não incluir **Az.IotCentral**, execute o seguinte comando:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Criar um aplicativo

Use o cmdlet [New-AzIotCentralApp](/powershell/module/az.iotcentral/New-AzIotCentralApp) para criar um aplicativo do IoT Central na sua assinatura do Azure. Por exemplo:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

Primeiro, o script cria um grupo de recursos na região Leste dos EUA para o aplicativo. A tabela a seguir descreve os parâmetros usados com o comando **New-AzIotCentralApp**:

|Parâmetro         |Descrição |
|------------------|------------|
|ResourceGroupName |O grupo de recursos que contém o aplicativo. Esse grupo de recursos já precisa existir na sua assinatura. |
|Location |Por padrão, esse cmdlet usa a localização do grupo de recursos. No momento, você pode criar um aplicativo IoT Central nas regiões da **Austrália**, **Pacífico Asiático**, **Europa**, **Estados Unidos**, **Reino Unido**e **Japão** . |
|Nome              |Digite o nome do aplicativo no portal do Azure. |
|Subdomínio         |O subdomínio na URL do aplicativo. No exemplo, a URL do aplicativo é `https://mysubdomain.azureiotcentral.com`. |
|Sku               |No momento, você pode usar **ST1** ou **ST2**. Confira [Preço do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Modelo          | O modelo de aplicativo a usar. Para obter mais informações, confira a tabela a seguir. |
|DisplayName       |O nome do aplicativo, conforme exibido na interface do usuário. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>Exibir seus aplicativos do IoT Central

Use o cmdlet [Get-AzIotCentralApp](/powershell/module/az.iotcentral/Get-AzIotCentralApp) para listar seus aplicativos do IoT Central e exibir os metadados.

## <a name="modify-an-application"></a>Modificar um aplicativo

Use o cmdlet [AzIotCentralApp conjunto](/powershell/module/az.iotcentral/set-aziotcentralapp) para atualizar os metadados de um aplicativo do IoT Central. Por exemplo, para alterar o nome de exibição do aplicativo:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Remover um aplicativo

Use o cmdlet [Remove-AzIotCentralApp](/powershell/module/az.iotcentral/Remove-AzIotCentralApp) para excluir um aplicativo do IoT Central. Por exemplo:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a gerenciar os aplicativos do Azure IoT Central no Azure PowerShell, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administrar seu aplicativo](howto-administer.md)