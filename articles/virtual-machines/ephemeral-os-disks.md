---
title: Discos do SO Efêmero
description: Saiba mais sobre discos do sistema operacional efêmero para VMs do Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 814824fb6708abaf549bb3de19b4aced4774a244
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102485751"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Discos do sistema operacional efêmero para VMs do Azure

Os discos do sistema operacional efêmero são criados no armazenamento da VM (máquina virtual) local e não são salvos no armazenamento remoto do Azure. Os discos do sistema operacional efêmero funcionam bem para cargas de trabalho sem estado, em que os aplicativos são tolerantes a falhas de VM individuais, mas são mais afetados pelo tempo de implantação da VM ou refazendo a imagem das instâncias de VM individuais. Com o disco do sistema operacional efêmero, você obtém latência de leitura/gravação mais baixa no disco do sistema operacional e uma reimagem de VM mais rápida. 
 
Os principais recursos dos discos efêmeras são: 
- Ideal para aplicativos sem estado.
- Eles podem ser usados com imagens do Marketplace e personalizadas.
- Capacidade de redefinir rapidamente ou refazer a imagem de VMs e instâncias do conjunto de dimensionamento para o estado de inicialização original.  
- Menor latência, semelhante a um disco temporário. 
- Discos do sistema operacional efêmero são gratuitos, você não incorre em nenhum custo de armazenamento para o disco do sistema operacional.
- Eles estão disponíveis em todas as regiões do Azure. 
- O disco do so efêmero tem suporte pela [Galeria de imagens compartilhadas](./shared-image-galleries.md). 
 

 
Principais diferenças entre discos do sistema operacional persistentes e efêmeras:

|                             | Disco do so persistente                          | Disco do SO Efêmero                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **Limite de tamanho para o disco do sistema operacional**      | 2 TiB                                                                                        | Tamanho do cache para o tamanho da VM ou 2TiB, o que for menor. Para o **tamanho do cache em GIB**, consulte [DS](sizes-general.md), [es](sizes-memory.md), [M](sizes-memory.md), [FS](sizes-compute.md)e [GS](sizes-previous-gen.md#gs-series)              |
| **Tamanhos de VM com suporte**          | Tudo                                                                                          | Tamanhos de VM que dão suporte ao armazenamento Premium, como DSv1, DSv2, DSv3, Esv3, FS, FsV2, GS, M                                               |
| **Suporte a tipo de disco**           | Disco do sistema operacional gerenciado e não gerenciado                                                                | Somente disco do sistema operacional gerenciado                                                               |
| **Suporte de regiões**              | Todas as regiões                                                                                  | Todas as regiões                              |
| **Persistência de dados**            | Os dados do disco do sistema operacional gravados no disco do sistema operacional são armazenados no armazenamento do Azure                                  | Os dados gravados no disco do sistema operacional são armazenados no armazenamento da VM local e não são persistidos no armazenamento do Azure. |
| **Estado de parada desalocada**      | As VMs e as instâncias do conjunto de dimensionamento podem ser interrompidas e reiniciadas a partir do estado de parada/desalocada | VMs e instâncias do conjunto de dimensionamento não podem ser interrompidas-desalocadas                                  |
| **Suporte a disco do so especializado** | Sim                                                                                          | Não                                                                                 |
| **Redimensionamento de disco do so**              | Com suporte durante a criação da VM e depois que a VM é parada-desalocada                                | Com suporte somente durante a criação da VM                                                  |
| **Redimensionando para um novo tamanho de VM**   | OS dados do disco do sistema operacional são preservados                                                                    | Os dados no disco do sistema operacional são excluídos, o sistema operacional é reprovisionado       
| **Posicionamento do arquivo de paginação**   | Para o Windows, o arquivo de paginação é armazenado no disco de recursos                                              | Para o Windows, o arquivo de paginação é armazenado no disco do sistema operacional   |

## <a name="size-requirements"></a>Requisitos de tamanho

Você pode implantar as imagens da VM e da instância até o tamanho do cache da VM. Por exemplo, as imagens padrão do Windows Server do Marketplace são cerca de 127 GiB, o que significa que você precisa de um tamanho de VM que tenha um cache maior do que 127 GiB. Nesse caso, o [Standard_DS2_v2](dv2-dsv2-series.md) tem um tamanho de cache de 86 Gib, o que não é grande o suficiente. O Standard_DS3_v2 tem um tamanho de cache de 172 GiB, que é grande o suficiente. Nesse caso, o Standard_DS3_v2 é o menor tamanho na série DSv2 que você pode usar com essa imagem. As imagens básicas do Linux no Marketplace e as imagens do Windows Server que são indicadas `[smallsize]` tendem a ser cerca de 30 GiB e podem usar a maioria dos tamanhos de VM disponíveis.

Os discos efêmeros também exigem que o tamanho da VM dê suporte ao armazenamento Premium. Os tamanhos geralmente (mas nem sempre) têm um `s` no nome, como DSv2 e EsV3. Para obter mais informações, consulte [tamanhos de VM do Azure](sizes.md) para obter detalhes sobre quais tamanhos dão suporte ao armazenamento Premium.

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>Visualização-OS discos do sistema operacional efêmero agora podem ser armazenados em discos temporários
Os discos do sistema operacional efêmero agora podem ser armazenados no disco temporário/de recurso da VM, além do cache da VM. Então, agora você pode usar discos do sistema operacional efêmero com a VM que não tem um cache ou tem cache insuficiente, mas tem um disco temporário/de recursos para armazenar o disco do sistema operacional efêmero, como Dav3, Dav4, Eav4 e Eav3. Se uma VM tiver um espaço temporário e cache suficiente, você também poderá especificar onde deseja armazenar o disco do sistema operacional efêmero usando uma nova propriedade chamada [DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement). Com esse recurso, quando uma VM do Windows é provisionada, configuramos o arquivo de paginação para estar localizado no disco do sistema operacional. Esse recurso está atualmente na visualização. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para começar, [solicite o acesso](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u).

## <a name="powershell"></a>PowerShell

Para usar um disco efêmero para uma implantação de VM do PowerShell, use [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) em sua configuração de VM. Defina `-DiffDiskSetting` como `Local` e `-Caching` como `ReadOnly` .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Para implantações de conjunto de dimensionamento, use o cmdlet [set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) em sua configuração. Defina `-DiffDiskSetting` como `Local` e `-Caching` como `ReadOnly` .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Para usar um disco efêmero para uma implantação de VM da CLI, defina o `--ephemeral-os-disk` parâmetro em [AZ VM Create](/cli/azure/vm#az-vm-create) como `true` e o `--os-disk-caching` parâmetro para `ReadOnly` .

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

Para conjuntos de dimensionamento, use o mesmo `--ephemeral-os-disk true` parâmetro para [AZ-vmss-Create](/cli/azure/vmss#az-vmss-create) e defina o `--os-disk-caching` parâmetro como `ReadOnly` .

## <a name="portal"></a>Portal

No portal do Azure, você pode optar por usar discos efêmeros ao implantar uma VM abrindo a seção **avançada** da guia **discos** . Para **usar disco do sistema operacional efêmero** , selecione **Sim**.

![Captura de tela mostrando o botão de opção para escolher usar um disco do sistema operacional efêmero](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Se a opção para usar um disco efêmero estiver esmaecida, você poderá ter selecionado um tamanho de VM que não tenha um tamanho de cache maior do que a imagem do sistema operacional ou que não dê suporte ao armazenamento Premium. Volte para a página **noções básicas** e tente escolher outro tamanho de VM.

Você também pode criar conjuntos de dimensionamento com discos do sistema operacional efêmero usando o Portal. Apenas certifique-se de selecionar um tamanho de VM com tamanho de cache grande o suficiente e, em seguida, em **usar disco do so efêmero** , selecione **Sim**.

![Captura de tela mostrando o botão de opção para escolher usar um disco do so efêmero para seu conjunto de dimensionamento](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Implantação do modelo do conjunto de dimensionamento  
O processo para criar um conjunto de dimensionamento que usa um disco do sistema operacional efêmero é adicionar a `diffDiskSettings` propriedade ao `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` tipo de recurso no modelo. Além disso, a política de cache deve ser definida como `ReadOnly` para o disco do sistema operacional efêmero. 


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

## <a name="vm-template-deployment"></a>Implantação de modelo de VM 
Você pode implantar uma VM com um disco do sistema operacional efêmero usando um modelo. O processo para criar uma VM que usa discos do sistema operacional efêmero é adicionar a `diffDiskSettings` propriedade ao tipo de recurso Microsoft. Compute/virtualMachines no modelo. Além disso, a política de cache deve ser definida como `ReadOnly` para o disco do sistema operacional efêmero. 

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
Você pode refazer a imagem de uma instância de máquina virtual com o disco do sistema operacional efêmero usando a API REST, conforme descrito abaixo e por meio do portal do Azure, acessando o painel Visão geral da VM. Para conjuntos de dimensionamento, a recriação de imagens já está disponível por meio do PowerShell, da CLI e do Portal.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Qual é o tamanho dos discos do sistema operacional local?**

R: damos suporte a plataformas e imagens personalizadas, até o tamanho do cache da VM, onde todas as leituras/gravações no disco do sistema operacional serão locais no mesmo nó que a máquina virtual. 

**P: o disco do sistema operacional efêmero pode ser redimensionado?**

R: não, depois que o disco do sistema operacional efêmero é provisionado, o disco do sistema operacional não pode ser redimensionado. 

**P: posso anexar um Managed Disks a uma VM efêmera?**

R: Sim, você pode anexar um disco de dados gerenciado a uma VM que usa um disco do sistema operacional efêmero. 

**P: todos os tamanhos de VM terão suporte para discos do sistema operacional efêmero?**

R: não, há suporte para os tamanhos de VM de armazenamento mais Premium (DS, ES, FS, GS, M, etc.). Para saber se um determinado tamanho de VM dá suporte a discos do sistema operacional efêmero, você pode:

Chamar o `Get-AzComputeResourceSku` cmdlet do PowerShell
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**P: o disco do sistema operacional efêmero pode ser aplicado a VMs e conjuntos de dimensionamento existentes?**

R: não, o disco do sistema operacional efêmero só pode ser usado durante a criação da VM e do conjunto de dimensionamento. 

**P: você pode misturar discos do sistema operacional efêmero e normal em um conjunto de dimensionamento?**

R: não, você não pode ter uma mistura de instâncias de disco do sistema operacional efêmeras e persistentes dentro do mesmo conjunto de dimensionamento. 

**P: o disco do sistema operacional efêmero pode ser criado usando o PowerShell ou a CLI?**

R: Sim, você pode criar VMs com o disco do sistema operacional efêmero usando REST, modelos, PowerShell e CLI.

**P: quais recursos não têm suporte com o disco do sistema operacional efêmero?**

R: discos efêmeros não dão suporte A:
- Capturando imagens de VM
- Instantâneos de disco 
- Azure Disk Encryption 
- Serviço de Backup do Azure
- Azure Site Recovery  
- Permuta de disco do so 

> [!NOTE]
> 
> O disco efêmero não poderá ser acessado por meio do Portal. Você receberá o erro "recurso não encontrado" ou "404" ao acessar o disco efêmero que é esperado.
> 
 
## <a name="next-steps"></a>Próximas etapas
Você pode criar uma VM com um disco do sistema operacional efêmero usando o [CLI do Azure](/cli/azure/vm#az-vm-create).
