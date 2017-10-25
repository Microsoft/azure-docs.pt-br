---
title: "Discos de dados conectados a conjuntos de dimensionamento de máquina Virtual do Azure | Microsoft Docs"
description: "Saiba como usar discos de dados anexados com conjuntos de escala de máquina virtual"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: guybo
ms.openlocfilehash: 22c7e589efa9a9f401549ec9b95c58c4eaf07b94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-vm-scale-sets-and-attached-data-disks"></a>Conjuntos de Dimensionamento de VMs do Azure e discos de dados anexados
Azure [conjuntos de escala de máquina virtual](/azure/virtual-machine-scale-sets/) agora oferecem suporte a máquinas virtuais com discos de dados anexado. Discos de dados podem ser definidos no perfil de armazenamento de conjuntos de escala que foram criados com o Azure Managed Disks. Anteriormente, as únicas opções de armazenamento com conexão direta disponíveis com as VMs em conjuntos de escala eram a unidade do sistema operacional e unidades temporárias.

> [!NOTE]
>  Quando você cria uma escala com discos de dados anexado definidos, você ainda precisa montar e formatar discos a partir de uma VM para usá-los (assim como para VMs do Azure autônomo). Uma maneira conveniente de fazer isso é usar uma extensão de script personalizado que chama um script padrão para particionar e formatar todos os discos de dados em uma máquina virtual.

## <a name="create-a-scale-set-with-attached-data-disks"></a>Criar uma escala com discos de dados anexados
Uma maneira simples de criar uma escala com discos anexados é usar o comando _vmss create_ da [CLI do Azure](https://github.com/Azure/azure-cli). O exemplo a seguir cria um grupo de recursos do Azure e um conjunto de escala VM de 10 VMs Ubuntu, cada um com discos de dados anexados 2, de 50 GB e 100 GB, respectivamente.
```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```
Observe que o comando _vmss create_ usará determinados valores de configuração como padrão se você não especificá-los. Para ver as opções disponíveis que você pode substituir, experimente:
```bash
az vmss create --help
```
É outra maneira de criar uma escala com discos de dados anexados definir um conjunto de escala em um modelo do Azure Resource manager, inclua uma seção _dataDisks_ no _storageProfile_ e implante o modelo. O exemplo de disco de 50 GB e 100 GB acima, seria definido como este no modelo:
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    }
]
```
Você pode ver um concluído, pronto para implantar o exemplo de um modelo de conjunto de escala com um disco anexado definido aqui: [https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data](https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data).

## <a name="adding-a-data-disk-to-an-existing-scale-set"></a>Adição de um disco de dados para um conjunto existente de escala
> [!NOTE]
>  Você pode anexar discos de dados somente para um conjunto de escala que foi criado com o [Azure Managed Disks](./virtual-machine-scale-sets-managed-disks.md).

Você pode adicionar um disco de dados a um conjunto de dimensionametno usando o comando _az vmss disk attach_ da CLI do Azure. Especifique um lun que ainda não esteja em uso. O exemplo da CLI a seguir adiciona uma unidade de 50 GB a lun 3:
```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```

O exemplo do PowerShell a seguir adiciona uma unidade de 50 GB a lun 3:
```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myvmssrg -VMScaleSetName myvmss
$vmss = Add-AzureRmVmssDataDisk -VirtualMachineScaleSet $vmss -Lun 3 -Caching 'ReadWrite' -CreateOption Empty -DiskSizeGB 50 -StorageAccountType StandardLRS
Update-AzureRmVmss -ResourceGroupName myvmssrg -Name myvmss -VirtualMachineScaleSet $vmss
```

> [!NOTE]
> Diferentes tamanhos de VM têm limites diferentes para a quantidade de unidades conectadas que dão suporte. Verifique o [características de tamanho de máquina virtual](../virtual-machines/windows/sizes.md) antes de adicionar um novo disco.

Você também pode adicionar um disco, adicionando uma nova entrada à propriedade _dataDisks_ no _storageProfile_ de uma definição de conjunto de dimensionamento e aplicando a alteração. Para testar isso, localize a definição do conjunto de uma escala existente [Azure Resource Explorer](https://resources.azure.com/). Selecione _editar_ e adicione um novo disco à lista de discos de dados. Por exemplo usando o exemplo acima:
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    },
    {
    "lun": 3,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 20
    }          
]
```

Em seguida, selecione _colocar_ para aplicar as alterações ao seu conjunto de escala. Este exemplo funcionaria enquanto você estiver usando um tamanho VM que oferece suporte a mais de dois discos de dados anexado.

> [!NOTE]
> Quando você fizer uma alteração na definição de uma escala como adicionar ou remover um disco de dados, se aplica a todas as VMs recentemente criadas, mas só se aplicará a máquinas virtuais existentes, se a propriedade _upgradePolicy_ estiver definida como "Automática". Se estiver definido como "Manual", você precisa aplicar manualmente o novo modelo para máquinas virtuais existentes. Você pode fazer isso no portal usando o comando _Update-AzureRmVmssInstance_ do PowerShell ou usando o comando _az vmss update-instances_ da CLI.

## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>Adicionar discos de dados preenchidos previamente a um conjunto de dimensionamento existente 
> Quando você adicionar discos a um modelo do conjunto de dimensionamento existente, por design, o disco sempre será criado vazio. Este cenário também inclui novas instâncias criadas pelo conjunto de dimensionamento. Esse comportamento ocorre porque a definição do conjunto de dimensionamento tem um disco de dados vazio. Para criar unidades de dados preenchidas previamente para um modelo do conjunto de dimensionamento existente, você pode escolher qualquer uma das duas opções:

* copie os dados da VM de instância 0 para o(s) disco(s) de dados em outras VMs executando um script personalizado.
* crie uma imagem gerenciada com o disco do SO mais o disco de dados (com os dados necessários) e crie um novo conjunto de dimensionamento com a imagem. Dessa forma, cada nova VM criada terá um disco de dados fornecido na definição do conjunto de dimensionamento. Como essa definição fará referência a uma imagem com um disco de dados que tem dados personalizados, todas as máquinas virtuais no conjunto de dimensionamento produzirão automaticamente essas alterações.

> A maneira de criar uma imagem personalizada pode ser encontrada aqui: [Criar uma imagem gerenciada de uma VM generalizada no Azure](/azure/virtual-machines/windows/capture-image-resource/) 

> O usuário precisa capturar a VM de instância 0 que tem os dados necessários, em seguida, usar esse vhd para a definição da imagem.

## <a name="removing-a-data-disk-from-a-scale-set"></a>Remoção de um disco de dados de um conjunto de escala
Você pode remover um disco de dados de um conjunto de dimensionamento de VM usando o comando _az vmss disk detach_ da CLI. Por exemplo, o comando a seguir remove o disco definido no lun 2:
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
Da mesma forma, você pode também remover um disco de um conjunto de dimensionamento ao remover uma entrada da propriedade _dataDisks_ no _storageProfile_ e aplicar a alteração. 

## <a name="additional-notes"></a>Observações adicionais
O suporte a discos Gerenciados pelo Azure e a discos de dados anexados do conjunto de dimensionamento está disponível na versão [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) ou posterior da API Microsoft.Compute.

A implementação inicial do suporte de disco anexado para conjuntos de escala, você não pode anexar ou desanexar discos de dados de VMs individuais em um conjunto de escala.

Suporte do portal do Azure para discos de dados anexados em conjuntos de escala é limitada inicialmente. Dependendo dos seus requisitos, você pode usar modelos do Azure, CLI, PowerShell, SDKs e API REST para gerenciar os discos anexados.


