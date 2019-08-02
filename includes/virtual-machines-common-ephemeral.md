---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a57335eccfce1e81fe0cc85ae6fa7b12aa27e1c3
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805889"
---
Efêmeros discos do sistema operacional são criados no armazenamento de máquina virtual local (VM) e não são salvos no armazenamento do Azure remoto. Discos de SO efêmeros funcionam bem para cargas de trabalho sem monitoração de estado, em que os aplicativos são tolerantes a falhas VM individuais, mas são mais afetadas pelo tempo de implantação de VM ou refazer a imagem de instâncias de VM individuais. Com o disco do SO efêmero, você obtém a menor latência de leitura/gravação para o disco do sistema operacional e a recriação de imagem de VM mais rápida. 
 
Os principais recursos dos discos efêmeros são: 
- Ideal para aplicativos sem monitoração de estado.
- Eles podem ser usados com o Marketplace e imagens personalizadas.
- Instâncias de conjunto de capacidade de reinicialização rápida ou VMs de recriação de imagem e escala para o estado original de inicialização.  
- Latência mais baixa, semelhante a um disco temporário. 
- Discos de SO efêmeros são gratuitos, você incorrerá em nenhum custo de armazenamento para o disco do sistema operacional.
- Eles estão disponíveis em todas as regiões do Azure. 
- Há suporte para o disco do SO efêmero por [Galeria de imagens compartilhadas](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Principais diferenças entre os discos do sistema operacional persistentes e efêmeras:

|                             | Disco do sistema operacional persistente                          | Disco do SO Efêmero                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limite de tamanho do disco do sistema operacional      | 2 TiB                                                                                        | Cache de tamanho para o tamanho da VM ou 2TiB, o que for menor. Para o **tamanho em GiB de cache**, consulte [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), e [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Tamanhos de VM compatíveis          | Todos                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Suporte ao tipo de disco           | Disco do sistema operacional gerenciado e não gerenciado                                                                | Somente disco do sistema operacional gerenciado                                                               |
| Suporte de regiões              | Todas as regiões                                                                                  | Todas as regiões                              |
| Persistência de dados            | Dados de disco do sistema operacional gravados em disco do sistema operacional são armazenados no armazenamento do Azure                                  | Dados gravados no disco do sistema operacional são armazenados no armazenamento de VM local e não são persistidos no armazenamento do Azure. |
| Estado parada desalocada      | As VMs e instâncias de conjunto de dimensionamento podem ser interrompida e desalocada e reiniciadas do estado parada desalocada | Máquinas virtuais e instâncias de conjunto de escala não podem ser interrompida e desalocada                                  |
| Suporte de disco do sistema operacional especializado | Sim                                                                                          | Não                                                                                 |
| Redimensionamento do disco do sistema operacional              | Durante a criação da VM e depois a VM é interrompida e desalocada com suporte                                | Suporte durante a criação da VM apenas                                                  |
| Redimensionar para um novo tamanho VM   | Os dados de disco do sistema operacional são preservados                                                                    | Dados no disco do SO são excluídos, o sistema operacional for novamente provisionado                                      |

## <a name="size-requirements"></a>Requisitos de tamanho

Você pode implantar imagens VM e instância até o tamanho do cache VM. Por exemplo, imagens do Windows Server padrão do marketplace são GiB prestes a 127, o que significa que você precisa de um tamanho de VM que tem um cache maior do que 127 GiB. Nesse caso, o [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series) tem um tamanho de cache de 86 GiB, que não é grande o suficiente. O Standard_DS2_v2 tem um tamanho de cache de GiB 172, que é grande o suficiente. Nesse caso, o Standard_DS3_v2 é o menor tamanho da série DSv2 que você pode usar com essa imagem. Imagens do Linux básicas as imagens do Marketplace e no Windows Server que são denotados por `[smallsize]` tendem a ser cerca de 30 GiB e podem usar a maioria dos tamanhos de VM disponíveis.

Os discos efêmeros também exigem que o tamanho da VM dá suporte a armazenamento Premium. Os tamanhos normalmente (mas nem sempre) têm um `s` no nome, como DSv2 e EsV3. Para obter mais informações, consulte [tamanhos de VM do Azure](../articles/virtual-machines/linux/sizes.md) para obter mais detalhes em torno dos quais tamanhos dão suporte a armazenamento Premium.

## <a name="powershell"></a>PowerShell

Para usar um disco efêmero para uma implantação de VM do PowerShell, use [AzVMOSDisk conjunto](/powershell/module/az.compute/set-azvmosdisk) em sua configuração de VM. Defina as `-DiffDiskSetting` à `Local` e `-Caching` para `ReadOnly`.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Para implantações de conjunto de dimensionamento, use o [AzVmssStorageProfile conjunto](/powershell/module/az.compute/set-azvmssstorageprofile) cmdlet em sua configuração. Defina as `-DiffDiskSetting` à `Local` e `-Caching` para `ReadOnly`.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Para usar um disco efêmero para uma implantação de VM da CLI, defina as `--ephemeral-os-disk` parâmetro no [criar vm az](/cli/azure/vm#az-vm-create) para `true` e o `--os-disk-caching` parâmetro `ReadOnly`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Para conjuntos de dimensionamento, use o mesmo `--ephemeral-os-disk true` parâmetro para [az vmss create](/cli/azure/vmss#az-vmss-create) e defina as `--os-disk-caching` parâmetro `ReadOnly`.

## <a name="portal"></a>Portal   

No portal do Azure, você pode optar por usar discos efêmeros ao implantar uma VM, abrindo o **Advanced** seção o **discos** guia. Para **disco do SO efêmero uso** selecionar **Sim**.

![Captura de tela mostrando o botão de opção para escolher usar um disco do SO efêmero](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Se a opção para usar um disco efêmero estiver acinzentada, talvez você tenha selecionado um tamanho de VM que não tem um tamanho de cache maior que a imagem do sistema operacional ou que não dá suporte a armazenamento Premium. Volte para o **Noções básicas de** página e tente escolher outro tamanho de VM.

Você também pode criar conjuntos de dimensionamento com discos efêmeros do sistema operacional usando o portal. Apenas certifique-se de selecionar um tamanho de VM com um tamanho de cache grande o suficiente e, em seguida, na **disco do SO efêmero uso** selecionar **Sim**.

![Captura de tela mostrando o botão de opção para escolher usar um disco do SO efêmero para seu conjunto de dimensionamento](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Implantação de modelo do conjunto de dimensionamento  
O processo para criar um conjunto de dimensionamento que usa um disco do SO efêmero é adicionar o `diffDiskSettings` propriedade para o `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` tipo de recurso no modelo. Além disso, a política de cache deve ser definida como `ReadOnly` para o disco do SO efêmero. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Implantação de modelo VM 
Você pode implantar uma VM com um disco do SO efêmero usando um modelo. O processo para criar uma VM que usa discos de SO efêmeros é adicionar o `diffDiskSettings` propriedade ao tipo de recurso Compute/virtualmachines no modelo. Além disso, a política de cache deve ser definida como `ReadOnly` para o disco do SO efêmero. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Refazer a imagem de uma VM usando REST
Atualmente, o único método para refazer a imagem de uma instância de máquina Virtual com o disco do SO efêmero é por meio do usando a API REST. Para conjuntos de dimensionamento, refazer a imagem já está disponível por meio do Powershell, CLI e o portal.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: O que é o tamanho dos discos do sistema operacional local?**

R: Damos suporte a plataforma e imagens personalizadas, até o tamanho do cache VM, onde todas as leituras/gravações no disco do sistema operacional será locais no mesmo nó que a máquina Virtual. 

**P: O disco do SO efêmero pode ser redimensionado?**

R: Não, uma vez que o disco do SO efêmero é provisionado, o disco do sistema operacional não pode ser redimensionado. 

**P: Pode anexar um disco gerenciado a uma VM efêmero?**

R: Sim, você pode anexar um disco de dados gerenciado a uma VM que usa um disco do SO efêmero. 

**P: Todos os tamanhos VM terá suporte para discos do SO efêmeros?**

R: Não, todos os tamanhos de VM de armazenamento Premium são compatíveis (DS, ES, FS, GS e M), exceto a série B, N-series e os tamanhos da série H.  
 
**P: O disco do SO efêmero pode ser aplicado a máquinas virtuais existentes e conjuntos de dimensionamento?**

R: Não, efêmero só pode ser usado durante a VM de disco do sistema operacional e criação do conjunto de dimensionamento. 

**P: Você pode misturar efêmeros e normais discos do sistema operacional em um conjunto de dimensionamento?**

R: Não, você não pode ter uma combinação de instâncias de disco do SO efêmeras e persistentes dentro do mesmo conjunto de dimensionamento. 

**P: Pode o disco do SO efêmero ser criado usando o Powershell ou CLI?**

R: Sim, você pode criar VMs com disco do SO efêmero usando REST, modelos, PowerShell e CLI.

**P: Quais recursos não são compatíveis com o disco do SO efêmero?**

R: Os discos efêmeros não dão suporte:
- Captura de imagens VM
- Instantâneos de disco 
- Azure Disk Encryption 
- Serviço de Backup do Azure
- Azure Site Recovery  
- Troca de disco do sistema operacional 
