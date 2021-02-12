---
title: Habilitar extensão de VM usando CLI do Azure
description: Este artigo descreve como implantar extensões de máquina virtual para servidores habilitados para Arc do Azure em execução em ambientes de nuvem híbrida usando o CLI do Azure.
ms.date: 01/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6edb7d55e542f963c75693d535fa3b50dc5b827b
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916194"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Habilitar extensões de VM do Azure usando o CLI do Azure

Este artigo mostra como implantar e desinstalar extensões de VM, com suporte dos servidores habilitados para Arc do Azure, para uma máquina híbrida Linux ou Windows usando o CLI do Azure.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão da CLI do Azure

Os comandos ConnectedMachine não são enviados como parte do CLI do Azure. Antes de usar o CLI do Azure para gerenciar extensões de VM em seu servidor híbrido gerenciado por servidores habilitados para Arc, você precisa carregar a extensão ConnectedMachine. Execute o seguinte comando para obtê-lo:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Habilitar extensão

Para habilitar uma extensão de VM em seu servidor habilitado para Arc, use [AZ connectedmachine Extension Create](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_create) com os `--machine-name` `--extension-name` parâmetros,, `--location` ,, `--type` `settings` e `--publisher` .

O exemplo a seguir habilita a extensão de VM Log Analytics em um servidor habilitado para Arc:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "eastus" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

O exemplo a seguir habilita a extensão de script personalizado em um servidor habilitado para Arc:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

O exemplo a seguir habilita a extensão de VM Key Vault (versão prévia) em um servidor habilitado para Arc:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Listar extensões instaladas

Para obter uma lista das extensões de VM em seu servidor habilitado para Arc, use a [lista de extensões AZ connectedmachine](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) com os `--machine-name` `--resource-group` parâmetros e.

Exemplo:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Por padrão, a saída de comandos da CLI do Azure é em JSON (JavaScript Object Notation). Para alterar a saída padrão para uma lista ou tabela, por exemplo, use [az configure --output](/cli/azure/reference-index). Você também pode adicionar `--output` a qualquer comando para realizar uma alteração uma única vez no formato da saída.

O exemplo a seguir mostra a saída JSON parcial do `az connectedmachine extension -list` comando:

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Remover uma extensão instalada

Para remover uma extensão de VM instalada em seu servidor habilitado para Arc, use [AZ connectedmachine Extension Delete](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_delete) com os `--extension-name` `--machine-name` `--resource-group` parâmetros e.

Por exemplo, para remover a extensão de VM Log Analytics para Linux, execute o seguinte comando:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Próximas etapas

- Você pode implantar, gerenciar e remover extensões de VM usando o [Azure PowerShell](manage-vm-extensions-powershell.md), dos modelos [portal do Azure](manage-vm-extensions-portal.md)ou [Azure Resource Manager](manage-vm-extensions-template.md).

- As informações de solução de problemas podem ser encontradas no [guia solucionar problemas de extensões de VM](troubleshoot-vm-extensions.md).

- Examine o artigo [visão geral](/cli/azure/ext/connectedmachine/connectedmachine/extension) da extensão de VM CLI do Azure para obter mais informações sobre os comandos.
