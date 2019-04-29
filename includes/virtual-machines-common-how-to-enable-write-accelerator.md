---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 02/22/2019
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 72d9ec52732a78e39f6481e2cb2d40f17f86f028
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478118"
---
# <a name="enable-write-accelerator"></a>Habilitar acelerador de gravação

O Acelerador de Gravação é uma capacidade de disco máquinas virtuais (VMs) da Série M no Armazenamento Premium com Azure Managed Disks exclusivamente. Como o nome indica, o objetivo da funcionalidade é melhorar a latência de E/S das gravações no Armazenamento Premium do Azure. O Acelerador de Gravação é ideal para quando as atualizações do arquivo de log são necessárias para manter em disco em um modo de alto desempenho para bancos de dados modernos.

O Acelerador de Gravação está disponível para as VMs da Série M na nuvem pública.

## <a name="planning-for-using-write-accelerator"></a>Planejar a utilização do Acelerador de Gravação

O Acelerador de Gravação deve ser utilizado para os volumes, os quais contêm o log de transações ou logs de recuperação de um DBMS. Não é recomendável usar Aceleradores de Gravação para os volumes de dados de um DBMS porque o recurso foi otimizado para ser usado em discos de log.

O Acelerador de Gravação funciona somente em conjunto com o [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Habilitar o Acelerador de Gravação para disco do sistema operacional da VM reiniciará a VM.
>
> Para habilitar o Acelerador de Gravação para um disco do Azure existente que NÃO faça parte de um volume compilado de vários discos com gerenciadores de volume ou discos do Windows, SOFS (Servidor de Arquivos de Escalabilidade Horizontal do Windows), LVM do Linux ou MDADM, a carga de trabalho que acessa o disco do Azure precisa ser desligada. Os aplicativos de banco de dados utilizando o disco do Azure devem ser desligados.
>
> Se você quiser habilitar ou desabilitar o Acelerador de Gravação para um volume existente que é compilado de vários discos do Armazenamento Premium do Azure e distribuído utilizando gerenciadores de volume ou discos do Windows, SOFS (Servidor de Arquivos de Escalabilidade Horizontal do Windows), LVM do Linux ou MDADM, todos os discos compilando o volume devem ser habilitados ou desabilitados para o Acelerador de Gravação em etapas separadas. **Antes de habilitar ou desabilitar o Acelerador de Gravação nessa configuração, desligue a VM do Azure**.

Habilitar o Acelerador e Gravação para discos do sistema operacional não deve ser necessária para configurações de VM relacionadas ao SAP.

### <a name="restrictions-when-using-write-accelerator"></a>Restrições ao utilizar o Acelerador de Gravação

Ao utilizar o Acelerador de Gravação para um VHD/disco do Azure, estas restrições são aplicáveis:

- O armazenamento em cache do disco Premium deve ser definido como 'Nenhum' ou ‘Somente leitura’. Todos os outros modos de armazenamento em cache não têm suporte.
- Instantâneo não têm suporte atualmente para discos do Acelerador de Gravação habilitados. Durante o backup, o serviço de Backup do Microsoft Azure exclui automaticamente os discos habilitados do Acelerador de Gravação anexados à VM.
- Somente E/S menores (<=512 KiB) estão tomando o caminho mais rápido. Em situações de carga de trabalho em que os dados estão sendo carregados em massa ou quando buffers de log de transação dos diferentes DBMS são preenchidos a um maior grau antes de serem retidos no armazenamento, é provável que a E/S gravada em disco não esteja fazendo o caminho mais rápido.

Há limites de VHDs de Armazenamento Premium do Azure por VM que podem ter suporte pelo Acelerador de Gravação. Os limites atuais são:

| SKU da VM | Número de discos do Acelerador de Gravação | IOPS do Disco Acelerador de Gravação por VM |
| --- | --- | --- |
| M128ms, 128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5.000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

São os limites de IOPS por VM e *não* por disco. Todos os discos do Acelerador de Gravação compartilham o mesmo limite IOPS por VM.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Habilitar o Acelerador de Gravação em um disco específico

As próximas seções descreverão como o Acelerador de Gravação pode ser habilitado nos VHDs do Armazenamento Premium do Azure.

### <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos a seguir são aplicáveis ao uso do Acelerador de Gravação neste momento:

- Os discos aos quais você deseja aplicar o Acelerador de Gravação do Azure precisam ser [discos gerenciados do Azure](https://azure.microsoft.com/services/managed-disks/) no Armazenamento Premium.
- Você deve estar usando uma VM de Série M

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Habilitar o Acelerador de Gravação do Azure usando o Microsoft Azure PowerShell

O módulo do PowerShell do Azure da versão 5.5.0 inclui as alterações nos cmdlets relevantes para habilitar ou desabilitar o Acelerador de Gravação para discos específicos do Armazenamento Premium do Azure.
Para habilitar ou implantar discos com suporte pelo Acelerador de Gravação, os seguintes comandos do PowerShell foram alterados e estendidos para aceitar um parâmetro para o Acelerador de Gravação.

Um novo parâmetro de opção, **-WriteAccelerator** foi adicionado aos cmdlets a seguir:

- [Set-AzVMOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Não fornecer o parâmetro define a propriedade como falso e implantará discos que não tenham suporte pelo Acelerador de Gravação.

Um novo parâmetro de opção, **-OsDiskWriteAccelerator** foi adicionado aos cmdlets a seguir:

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Não especificar o parâmetro define a propriedade como falso, retornando os discos que não alavancam o Acelerador de Gravação.

Um novo parâmetro booliano (não anulável) parâmetro, **OsDiskWriteAccelerator -** foi adicionado aos cmdlets a seguir:

- [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Especifique se $true ou $false para controlar o suporte do Aelerador de Gravação do Azure com os discos.

Exemplos de comandos podem ser semelhantes a:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Dois principais cenários podem ser em script, conforme mostrado nas seções a seguir.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Adicionar novo disco com suporte pelo Acelerador de Gravação usando o PowerShell

É possível utilizar esse script para adicionar um novo disco à sua VM. O disco criado com esse script usará o Acelerador de Gravação.

Substitua `myVM`, `myWAVMs`, `log001`, tamanho do disco e LunID do disco com valores apropriados para sua implantação específica.

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Habilitar o Acelerador de Gravação do Azure em um disco do Azure existente usando o PowerShell

Você pode usar este script para habilitar o Acelerador de Gravação em um disco existente. Substitua `myVM`, `myWAVMs`, e `test-log001` com valores apropriados para sua implantação específica. O script acima adiciona o Acelerador de Gravação a um disco existente, onde o valor **$newstatus** é definido como '$true'. Usar o valor '$false' desabilitará o Acelerador de Gravação em um determinado disco.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> Executar o script acima desanexará o disco especificado, habilitará o Acelerador de Gravação no disco e, em seguida, anexará o disco novamente

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Habilitar o Acelerador de Gravação usando o Portal do Azure

Você pode habilitar o Acelerador de Gravação por meio do portal onde pode especificar suas configurações de cache de disco:

![Acelerador de Gravação no Portal do Azure](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Habilitar o Acelerador de Gravação usando a CLI do Azure

Você pode usar a [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para habilitar o Acelerador de Gravação.

Para habilitar o Acelerador de Gravação em um disco existente, use [atualização az vm](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), você pode usar os seguintes exemplos, se você substituir o diskName, VMName e ResourceGroup para seus próprios valores: `az vm update -g group1 -n vm1 -write-accelerator 1=true`

Para anexar um disco com o Acelerador de Gravação habilitado, use [anexar disco de vm az](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach), você pode usar o exemplo a seguir, se você substituir em seus próprios valores: `az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator` 

Para desativar a Aceleração de Gravação, use [atualização de vm az](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), definindo as propriedades como falsas: `az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Habilitar o Acelerador de Gravação usando as APIS Rest

Para implantar por meio da API REST do Azure, é necessário instalar o armclient do Azure.

### <a name="install-armclient"></a>Instalar o armclient

Para executar o armclient, é necessário instalá-lo por meio do Chocolatey. É possível instalá-lo por meio do cmd.exe ou Powershell. Utilize direitos elevados para esses comandos (“Executar como Administrador”).

Utilizando cmd.exe, execute o comando a seguir: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Utilizando o Power Shell execute o comando a seguir: `Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Agora você pode instalar o armclient usando o seguinte comando em cmd.exe ou o PowerShell `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Obter a configuração atual da VM

Para alterar os atributos da configuração do disco, primeiro é necessário obter a configuração atual em um arquivo JSON. Você pode obter a configuração atual executando o comando a seguir: `armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Substitua os termos dentro de '<<   >>' pelos seus dados, incluindo o nome do arquivo que o arquivo JSON deve ter.

A saída pode ser semelhante a:

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Em seguida, atualize o arquivo JSON e habilitar o Acelerador de Gravação no disco chamado 'log1'. Essa etapa pode ser realizada adicionando esse atributo no arquivo JSON após a entrada do cache do disco.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Em seguida, atualize a implantação existente com este comando: `armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

A saída deve ser semelhante à seguinte. É possível ver esse Acelerador de Gravação habilitado para um disco.

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

Ao realizar essa alteração, a unidade deve ser compatível com o Acelerador de Gravação.
