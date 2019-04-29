---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: jboeshart
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: jaboes
ms.custom: include file
ms.openlocfilehash: 6085eb2b520217c4e678a75032e8a1cb4b9343ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60581065"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Utilizar discos gerenciados nos modelos do Azure Resource Manager

Este documento aborda as diferenças entre discos gerenciados e não gerenciados ao utilizar os modelos do Azure Resource Manager para provisionar máquinas virtuais. O exemplo ajuda você a atualizar os modelos existentes que estão usando Discos não gerenciados para os discos gerenciados. Para referência, estamos usando o modelo [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) como um guia. É possível visualizar o modelo utilizando ambos os [Discos gerenciados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) e uma versão anterior utilizando [discos não gerenciados](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json), se você deseja compará-los diretamente.

## <a name="unmanaged-disks-template-formatting"></a>Formatação de modelo de Discos não gerenciados

Para começar, veremos como os discos não gerenciados são implantados. Ao criar discos não gerenciados, é necessária uma conta de armazenamento para armazenar os arquivos de VHD. É possível criar uma nova conta de armazenamento ou utilizar uma conta já existente. Este artigo mostra como criar uma nova conta de armazenamento. Crie um recurso de conta de armazenamento em bloco de recursos, conforme mostrado abaixo.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

Dentro do objeto de máquina virtual, adicione uma dependência na conta de armazenamento para garantir que é criada antes da máquina virtual. Na seção `storageProfile`, especifique o URI completo do local do VHD que faz referência à conta de armazenamento e necessário para o disco do SO e qualquer disco de dados.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Formatação de modelo de discos gerenciados

Com o Azure Managed Disks, o disco torna-se um recurso de nível superior e não exige mais uma conta de armazenamento a ser criada pelo usuário. Os discos gerenciados foram expostos pela primeira vez na versão da API `2016-04-30-preview`. Eles estão disponíveis em todas as versões de API subsequentes e agora são o tipo de disco padrão. As seções a seguir percorrem as configurações padrão e detalham como personalizar ainda mais seus discos.

> [!NOTE]
> Recomendamos usar uma versão da API posterior a `2016-04-30-preview`, pois houve alterações consideráveis entre `2016-04-30-preview` e `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Configurações de disco gerenciados padrão

Para criar uma VM com discos gerenciados, não é mais necessário criar o recurso da conta de armazenamento e atualizar o recurso da máquina virtual, conforme a seguir. Observe especificamente que `apiVersion` reflete `2017-03-30` e `osDisk` e `dataDisks` não referem-se mais a um URI específico para VHD. Ao implantar sem especificar propriedades adicionais, o disco usará um tipo de armazenamento com base no tamanho da VM. Por exemplo, se você estiver usando um tamanho de VM com capacidade Premium (tamanhos com "s" em seu nome, como Standard_D2s_v3), o sistema usará o armazenamento Premium_LRS. Use a configuração de sku do disco para especificar um tipo de armazenamento. Se nenhum nome for especificado, será necessário o formato de `<VMName>_OsDisk_1_<randomstring>` para o disco do SO e `<VMName>_disk<#>_<randomstring>` para cada disco de dados. Por padrão, a criptografia do disco do Azure está desabilitada; o caching é Leitura/Gravação para o disco do OS e Nenhum para os discos de dados. É possível notar no exemplo abaixo que ainda há uma dependência de conta de armazenamento, porém, isso é apenas para armazenamento de diagnósticos e não é necessário para armazenamento em disco.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Utilizar um recurso de disco gerenciado de nível superior

Como alternativa para especificar a configuração do disco no objeto da máquina virtual, você pode criar um recurso de disco de nível superior e anexá-lo como parte da criação da máquina virtual. Por exemplo, é possível criar um recurso de disco da seguinte forma para utiiza como um disco de dados.

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

Dentro do objeto da VM é possível fazer referência a este objeto de disco para ser anexado. Especificar o ID do recurso do disco gerenciado criado na propriedade `managedDisk` permite a conexão do disco à medida que a VM é criada. O `apiVersion` para o recurso da VM está definido como `2017-03-30`. Uma dependência no recurso de disco é adicionada para garantir que tenha sido criado com êxito antes da criação da VM. 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Criar conjuntos de disponibilidade gerenciados com VMs utilizando discos gerenciados

Para criar conjuntos de disponibilidade gerenciados com VMs utilizando discos gerenciados, adicione o objeto `sku` ao recurso do conjunto de disponibilidade e defina a propriedade `name` para `Aligned`. Essa propriedade garante que os discos para cada VM estejam suficientemente isolados uns dos outros para evitar pontos únicos de falha. Observe também que o `apiVersion` para o recurso do conjunto de disponibilidade está definido como `2017-03-30`.

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>Discos SSD Standard

Abaixo estão os parâmetros necessários no modelo do Gerenciador de Recursos para criar discos SSD padrão:

* *apiVersion* para Microsoft.Compute deve ser definido como `2018-04-01` (ou posterior)
* Especifique *managedDisk.storageAccountType* como `StandardSSD_LRS`

A exemplo a seguir mostra a seção *properties.storageProfile.osDisk* de uma VM que usa discos SSD padrão:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Para obter um exemplo de modelo completo de como criar um disco SSD padrão com um modelo, consulte [Criar uma máquina virtual a partir de uma imagem do Windows com discos de dados padrão SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Cenários e personalizações adicionais

Para encontrar informações completas sobre as especificações de API REST, revise [criar uma documentação de API REST de disco gerenciado](/rest/api/manageddisks/disks/disks-create-or-update). Você encontrará cenários adicionais, assim como valores padrão e aceitáveis que podem ser enviados para a API por meio de implantações de modelos. 

## <a name="next-steps"></a>Próximos passos

* Para modelos completos que utilizam discos gerenciados, visite os seguintes links do Repositório de Início Rápido do Azure.
    * [VM do Windows VM com disco gerenciado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [VM do Linux VM com disco gerenciado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Lista completa de modelos de disco gerenciado](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Consulte o documento [Visão Geral do Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md) para saber mais sobre discos gerenciados.
* Revise a documentação de referência do modelo para recursos da máquina virtual, visitando o documento em [Microsoft.Compute/referência de modelo de virtualMachines](/azure/templates/microsoft.compute/virtualmachines).
* Revise a documentação de referência do modelo para recursos de disco, visitando o documento em [Microsoft.Compute/referência de modelo de discos](/azure/templates/microsoft.compute/disks).
* Para obter informações sobre como usar discos gerenciados em conjuntos de Dimensionamento de Máquinas Virtuais do Microsoft Azure, visite o documento [Usar discos de dados com conjuntos de escala](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks).
