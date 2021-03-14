---
title: Benefício Híbrido do Azure para Windows Server
description: Saiba como maximizar seus benefícios do Windows Software Assurance para trazer licenças locais para o Azure.
author: xujing-ms
ms.service: virtual-machines
ms.subservice: azure-hybrid-benefit
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: c5a9386540f418c8f490ca146e250e780737e478
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668056"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Benefício Híbrido do Azure para Windows Server
Para clientes com o Software Assurance, o Benefício Híbrido do Azure para Windows Server permite usar as licenças locais do Windows Server e executar máquinas virtuais do Windows no Azure por um custo reduzido. Você pode usa o Benefício Híbrido do Azure para Windows Server para implantar novas máquinas virtuais com Windows OS. Este artigo percorre as etapas sobre como implantar novas VMs com o Benefício Híbrido do Azure para Windows Server e como você pode atualizar VMs existentes e em execução. Para saber mais sobre licenciamento e economia de custo do Benefício Híbrido do Azure para Windows Server, consulte a [página de licenciamento do Benefício Híbrido do Azure para Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Cada licença de 2 processadores ou cada conjunto de licenças de 16 núcleos tem direito a duas instâncias de até 8 núcleos ou uma instância de até 16 núcleos. O Benefício Híbrido do Azure para licenças Standard Edition só pode ser usado uma vez localmente ou no Azure. Os benefícios da Datacenter Edition permitem o uso simultâneo localmente e no Azure.

Usar o Benefício Híbrido do Azure para Windows Server com quaisquer VMs executando Windows Server OS agora têm suporte em todas as regiões, incluindo VMs com software adicional como SQL Server ou software do marketplace de terceiros. 


## <a name="classic-vms"></a>VMs clássicas

Para VMs clássicas, há suporte apenas para implantar uma nova VM a partir de imagens personalizadas locais. Para aproveitar os recursos com suporte neste artigo, você deve primeiro migrar as VMs clássicas para o modelo do Resource Manager.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]
 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Formas de usar o Benefício Híbrido do Azure para Windows Server
Há algumas maneiras de usar as máquinas virtuais do Windows com o Benefício Híbrido do Azure:

1. Você pode implantar VMs de uma das imagens do Windows Server fornecidos no Azure Marketplace
2. Você pode carregar uma VM personalizada e implantar usando um modelo do Gerenciador de Recursos ou do Azure PowerShell
3. Você pode alternar e converter uma VM existente entre executar com o Benefício Híbrido do Azure ou pagar o custo sob demanda para Windows Server
4. Você também pode implantar o Benefício Híbrido do Azure para Windows Server em conjunto de dimensionamento de máquina virtual também


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Crie uma VM com Benefício Híbrido do Azure para Windows Server
Todas as imagens baseadas em Windows Server OS têm suporte para Benefício Híbrido do Azure para Windows Server. Você pode usar imagens de suporte da plataforma do Azure ou carregar as suas próprias imagens personalizadas do Windows Server. 

### <a name="portal"></a>Portal
Para criar uma VM com o Benefício Híbrido do Azure para Windows Server, role até a parte inferior da guia **noções básicas** durante o processo de criação e, em **Licenciamento** , marque a caixa para usar uma licença existente do Windows Server. 

### <a name="powershell"></a>PowerShell

```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>CLI
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Modelo
Nos modelos do Resource Manager, um parâmetro adicional para `licenseType` deve ser especificado. Você pode ler mais sobre a [criação de modelos do Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md)
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Converter uma VM existente usando o Benefício Híbrido do Azure para Windows Server
Se houver uma VM existente que você deseja converter para aproveitar o Benefício Híbrido do Azure para Windows Server, atualize o tipo de licença da VM seguindo as instruções abaixo.

> [!NOTE]
> A alteração do tipo de licença na VM não faz com que o sistema seja reinicializado e não causa interrupção no serviço.  Ele é simplesmente uma atualização de um sinalizador de metadados.
> 

### <a name="portal"></a>Portal
Na folha VM do portal, você pode atualizar a VM para usar o Benefício Híbrido do Azure selecionando a opção de “Configuração” e alternar a opção “Benefício Híbrido do Azure”

### <a name="powershell"></a>PowerShell
- Converter VMs do Windows Server existentes para o Benefício Híbrido do Azure para Windows Server

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Converter VMs do Windows Server com o benefício pré-pago

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- Converter VMs do Windows Server existentes para o Benefício Híbrido do Azure para Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Como verificar se sua VM está utilizando o benefício de licenciamento
Depois de implantar sua VM por meio do PowerShell, modelo do Gerenciador de Recursos ou Portal, verifique a configuração nos métodos seguintes.

### <a name="portal"></a>Portal
No portal de folha de VM, você pode ver a alternância para o Benefício Híbrido do Azure para Windows Server, selecionando a guia "Configuração".

### <a name="powershell"></a>PowerShell
O exemplo a seguir mostra o tipo de licença para uma única VM
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Saída:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Esta saída contrasta com a seguinte VM implantada sem licenciamento do Benefício Híbrido do Azure para Windows Server:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>CLI
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> A alteração do tipo de licença na VM não faz com que o sistema seja reinicializado e não causa interrupção no serviço. Trata-se apenas de um sinalizador de licenciamento de metadados.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Listar todas as VMs com Benefício Híbrido do Azure para Windows Server em uma assinatura
Para ver e contar todas as máquinas virtuais implantadas com o Benefício Híbrido do Azure para Windows Server, execute o comando a seguir em sua assinatura:

### <a name="portal"></a>Portal
Na folha de recursos de conjuntos de dimensionamento de máquina Virtual ou Máquina Virtual, você pode exibir uma lista de todas as VMs e tipo de licenciamento, configurando a coluna da tabela para incluir o "Benefício Híbrido do Azure". A configuração da VM pode ser "Habilitado", "Não habilitado" ou "Sem suporte".

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Implantar um Conjunto de Dimensionamento de Máquinas Virtuais com o Benefício Híbrido do Azure para Windows Server
Nos modelos do Gerenciador de Recursos de seu conjunto de dimensionamento de máquinas virtuais, um parâmetro adicional `licenseType` deve ser especificado dentro da sua propriedade VirtualMachineProfile. Você pode fazer isso durante criar ou atualizar para seu conjunto de dimensionamento por meio do modelo ARM, PowerShell, CLI do Azure ou REST.

O exemplo a seguir usa um modelo ARM com uma imagem do Windows Server 2016 Datacenter:
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
Você também pode aprender mais sobre como [Modificar um conjunto de escala de máquinas virtuais](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) para outras maneiras de atualizar sua escala definida.

## <a name="next-steps"></a>Próximas etapas
- Leia mais sobre [como economizar dinheiro com o benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Leia sobre as [Perguntas frequentes sobre Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Leia mais sobre [Orientação detalhada sobre licenciamento do Benefício Híbrido do Azure para Windows Server](/windows-server/get-started/azure-hybrid-benefit)
- Saiba mais sobre como o [Benefício Híbrido do Azure para Windows Server e o Azure Site Recovery tornam a migração de aplicativos para o Azure ainda mais econômica](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Saiba mais sobre o [Windows 10 no Azure com Direitos de Hospedagem Multilocatário](./windows-desktop-multitenant-hosting-deployment.md)
- Saiba mais sobre como [usar modelos do Resource Manager](../../azure-resource-manager/management/overview.md)
