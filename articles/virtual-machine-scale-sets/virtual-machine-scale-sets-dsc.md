---
title: "Uso de Configuração de Estado Desejado com Conjuntos de Dimensionamento de Máquinas Virtuais | Microsoft Docs"
description: "Uso de Conjuntos de Escala de Máquina Virtual com a Extensão de DSC do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: b61b0acf3072569ab733a13defb465c921d26187
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Uso de Conjuntos de Escala de Máquina Virtual com a Extensão de DSC do Azure
[Os Conjuntos de Dimensionamento de Máquina Virtual](virtual-machine-scale-sets-overview.md) podem ser usados com o manipulador de extensão [DSC (Configuração de Estado Desejado) do Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Os conjuntos de dimensionamento de máquina virtual fornecem uma maneira de implantar e gerenciar uma grande quantidade de máquinas virtuais e podem ser reduzidos e escalados horizontalmente de forma elástica em resposta ao carregamento. O DSC é usado para configurar as VMs quando elas ficam online, para que executem o software de produção.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Diferenças entre implantação para máquinas virtuais e conjuntos de dimensionamento de máquina virtual
A estrutura do modelo subjacente para um conjunto de dimensionamento de máquinas virtuais é ligeiramente diferente daquela encontrada em uma única VM. Especificamente, uma única VM implanta extensões sob o nó "virtualMachines". Há uma entrada do tipo "extensions" em que o DSC é adicionado ao modelo

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Um nó de conjunto de dimensionamento de máquinas virtuais tem uma seção "properties" com o atributo "VirtualMachineProfile", "extensionProfile". O DSC é adicionado sob "extensões"

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Comportamento de um conjunto de dimensionamento de máquinas virtuais
O comportamento de um conjunto de dimensionamento de máquinas virtuais é idêntico ao comportamento de uma única VM. Quando uma nova VM é criada, ela é provisionada automaticamente com a extensão de DSC. Se uma versão mais recente do WMF for necessária para a extensão, a VM será reiniciada antes de ser colocada online. Quando ela estiver online, baixará o arquivo .zip de configuração de DSC e o provisionará na VM. Mais detalhes podem ser encontrados em [Visão geral da Extensão DSC do Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas
Examine o [modelo do Azure Resource Manager para a extensão de DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Saiba como a [extensão DSC manipula com segurança as credenciais](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para saber mais sobre o manipulador de extensões DSC do Azure, confira [Introduction to the Azure Desired State Configuration extension handler (Introdução ao manipulador de extensões Configuração de Estado Desejado do Azure)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para saber mais sobre a DSC do PowerShell, [visite o centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

