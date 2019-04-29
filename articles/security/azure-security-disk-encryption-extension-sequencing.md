---
title: Dimensionamento de máquinas virtuais do Azure Disk Encryption e o Azure define o sequenciamento de extensão
description: Este artigo fornece instruções sobre como habilitar as VMs da IaaS do Microsoft Azure Disk Encryption para Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/21/2019
ms.openlocfilehash: e98e501806971f3cf1bec29960ad15ef9c0024fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611261"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Sequenciamento de extensão de conjunto de uso do Azure Disk Encryption com o dimensionamento de máquinas virtuais

As extensões, como a criptografia de disco do Azure podem ser adicionadas a uma escala de máquinas virtuais do Azure definido em uma ordem especificada. Para fazer isso, use [sequenciamento de extensão](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). 

Em geral, a criptografia deve ser aplicada a um disco:

- Depois de extensões ou scripts personalizados que prepara os discos ou volumes.
- Antes de extensões ou scripts personalizados que acessam ou consumam os dados sobre os volumes ou discos criptografados.

Em ambos os casos, o `provisionAfterExtensions` propriedade determina qual extensão deve ser adicionado posteriormente na sequência.

## <a name="sample-azure-templates"></a>Modelos do Azure de exemplo

Se você quiser ter o Azure Disk Encryption aplicada após outra extensão, coloque o `provisionAfterExtensions` propriedade no bloco de extensão AzureDiskEncryption. 

Aqui está um exemplo de uso "CustomScriptExtension", um script do Powershell que inicializa e formata um disco do Windows, seguido por "AzureDiskEncryption":

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Se você quiser ter o Azure Disk Encryption aplicado antes de outra extensão, coloque o `provisionAfterExtensions` propriedade no bloco da extensão a seguir.

Aqui está um exemplo de uso "AzureDiskEncryption" seguido por "VMDiagnosticsSettings", uma extensão que fornece o monitoramento e os recursos de diagnóstico em uma máquina virtual com base no Windows Azure:


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

Para obter mais modelos detalhados, consulte:
* Aplique a extensão Azure Disk Encryption após um script de shell personalizado que formata o disco (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)
* Aplique a extensão Azure Disk Encryption após um script do Powershell personalizado que inicializa e formata o disco (Windows): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-ADE-after-customscript.json)
* Aplicar a extensão Azure Disk Encryption antes de um script do Powershell personalizado que inicializa e formata o disco (Windows): [deploy-extseq-windows-CustomScript-after-ADE.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-CustomScript-after-ADE.json)

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o sequenciamento de extensão: [O provisionamento da extensão em conjuntos de dimensionamento de máquina virtual de sequência](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md).
- Saiba mais sobre o `provisionAfterExtensions` propriedade: [Referência de modelo Microsoft. Compute/virtualMachineScaleSets as extensões](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
