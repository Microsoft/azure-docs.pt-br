---
title: Migrar para o Resource Manager com o PowerShell | Microsoft Docs
description: Este artigo apresenta a migração de recursos IaaS com suporte da plataforma como VMs (máquinas virtuais), VNETs (redes virtuais) e contas de armazenamento do clássico ao ARM (Azure Resource Manager) usando os comandos do Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: dc88a7936c4ab4994bd9de168a682b1253c34e1f
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720234"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando o Azure PowerShell
Estas etapas mostram como usar os comandos do Azure PowerShell para migrar os recursos de IaaS (infraestrutura como serviço) do modelo de implantação clássico para o Modelo de implantação do Azure Resource Manager.

Se quiser, você também pode migrar recursos usando a [Interface de linha de comando do Azure (CLI do Azure)](../linux/migration-classic-resource-manager-cli.md).

* Para obter informações sobre cenários de migração com suporte, confira [Migração de recursos de IaaS com suporte da plataforma do clássico ao Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Para obter orientação e um passo a passo sobre a migração, confira [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico ao Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Examinar os erros de migração mais comuns](migration-classic-resource-manager-errors.md)

<br>
Este é um fluxograma para identificar a ordem em que as etapas precisam ser executadas durante um processo de migração

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="step-1-plan-for-migration"></a>Etapa 1: Planejar a migração
Veja a seguir algumas das práticas que recomendamos durante a avaliação de migração dos recursos de IaaS do modelo clássico para o Gerenciador de Recursos:

* Leia os [recursos e configurações com e sem suporte](migration-classic-resource-manager-overview.md). Caso você tenha máquinas virtuais que usam recursos ou configurações sem suporte, recomendamos aguardar até que o suporte do recurso/configuração seja anunciado. Como alternativa, se isso atender às suas necessidades, remova esse recurso ou mude a configuração para habilitar a migração.
* Se você tiver scripts automatizados que implantam sua infraestrutura e aplicativos atualmente, tente criar uma configuração de teste semelhante usando esses scripts para migração. Você também pode configurar ambientes de exemplo usando o portal do Azure.

> [!IMPORTANT]
> Atualmente não ha suporte para Gateways de Aplicativo para a migração do clássico para o Resource Manager. Para migrar uma rede virtual clássica com um Gateway de Aplicativo, remova o gateway antes de executar uma operação de Preparação para mover a rede. Depois de concluir a migração, reconecte o gateway no Azure Resource Manager.
>
>Não é possível migrar automaticamente gateways de ExpressRoute conectando-se a circuitos de ExpressRoute em outra assinatura. Nesses casos, remova o gateway de ExpressRoute, migre a rede virtual e recrie o gateway. Confira [Migrar circuitos de ExpressRoute e redes virtuais associadas do modelo de implantação clássico para o Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) para obter mais informações.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Etapa 2: Instale a versão mais recente do Azure PowerShell
Há duas opções principais para instalar o Azure PowerShell: [Galeria do PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) ou [Web Platform Installer (WebPI)](https://aka.ms/webpi-azps). WebPI recebe atualizações mensais. A Galeria do PowerShell receberá atualizações continuamente. Este artigo tem base no Azure PowerShell versão 2.1.0.

Para obter instruções de instalação, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Etapa 3: certifique-se de que você é um administrador da assinatura no portal do Azure
Para executar essa migração, você deve ser adicionado como um coadministrador da assinatura no [portal do Azure](https://portal.azure.com).

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Assinatura**. Caso não visualize essa opção, selecione **Todos os serviços**.
3. Localize a entrada de assinatura apropriada, em seguida, examine o campo **MINHA FUNÇÃO**. Para um coadministrador, o valor deve ser _administrador da conta_.

Se você não conseguir adicionar um coadministrador, contate um administrador de serviços ou o coadministrador da assinatura para ser adicionado.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Etapa 4: defina a assinatura e se inscreva para a migração
Primeiro, inicie um prompt do PowerShell. Para migração, é necessário instalar seu ambiente tanto para o modelo clássico quanto para o Resource Manager.

Entre em sua conta para o modelo do Gerenciador de Recursos.

```powershell
    Connect-AzAccount
```

Obtenha as assinaturas disponíveis usando o comando a seguir:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Defina sua assinatura do Azure para a sessão atual. Este exemplo define o nome da assinatura padrão como **Minha Assinatura do Azure**. Substitua o nome da assinatura de exemplo pelo nome da sua própria assinatura.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> O registro é uma etapa única, mas é preciso executá-lo uma vez antes de tentar a migração. Sem o registro, você verá a seguinte mensagem de erro:
>
> *BadRequest: a assinatura não está registrada para migração.*

Registre-se no provedor de recursos de migração usando o comando a seguir:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Aguarde cinco minutos para concluir o registro. É possível verificar o status da aprovação usando o comando a seguir:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Verifique se RegistrationState é `Registered` antes de continuar.

Agora, entre em sua conta para o modelo clássico.

```powershell
    Add-AzureAccount
```

Obtenha as assinaturas disponíveis usando o comando a seguir:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Defina sua assinatura do Azure para a sessão atual. Este exemplo define a assinatura padrão como **Minha Assinatura do Azure**. Substitua o nome da assinatura de exemplo pelo nome da sua própria assinatura.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Etapa 5: verifique se você tem uma quantidade suficiente de vCPUs de Máquina Virtual do Azure Resource Manager na região do Azure de sua implantação atual ou da VNET
Você pode usar o seguinte comando do PowerShell para verificar a quantidade atual de vCPUs no Azure Resource Manager. Para saber mais sobre cotas de vCPUs, veja [Limites e o Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager).

Este exemplo verifica a disponibilidade na região **Oeste dos EUA**. Substitua o nome da região de exemplo pelo nome da sua própria região.

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Etapa 6: execute comandos para migrar os recursos de IaaS
* [Migrar VMs em um serviço de nuvem (não em rede virtual)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrar VMs em uma rede virtual](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrar conta de armazenamento](#step-62-migrate-a-storage-account)

> [!NOTE]
> Todas as operações descritas aqui são idempotentes. Caso você tenha algum problema que não seja um recurso sem suporte ou um erro de configuração, recomendamos que repita a operação de preparação, anulação ou confirmação. Em seguida, a plataforma tentará novamente a ação.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Etapa 6.1: opção 1 - Migrar máquinas virtuais em um serviço de nuvem (não em uma rede virtual)
Obtenha a lista de serviços de nuvem usando o comando a seguir e escolha o serviço de nuvem que deseja migrar. Se as VMs no serviço de nuvem estiverem em uma rede virtual, ou se tiverem funções Web ou de trabalho, o comando retornará uma mensagem de erro.

```powershell
    Get-AzureService | ft Servicename
```

Obtenha o nome da implantação do serviço de nuvem. Neste exemplo, o nome do serviço é **Meu Serviço**. Substitua o nome do serviço de exemplo pelo nome de seu próprio serviço.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Prepare as máquinas virtuais no serviço de nuvem para migração. Você tem duas opções entre as quais escolher.

* **Opção 1. Migrar as VMs para uma rede virtual criada com uma plataforma**

    Primeiro, valide se você pode migrar o serviço de nuvem usando os seguintes comandos:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    O comando a seguir exibe todos os avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá passar para a etapa de **Preparação**:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opção 2. Migrar para uma rede virtual existente no modelo de implantação do Gerenciador de Recursos**

    Este exemplo define o nome do grupo de recursos como **myResourceGroup**, o nome de rede virtual como **myVirtualNetwork** e o nome da sub-rede como **mySubNet**. Substitua os nomes de exemplo pelos nomes de seus próprios recursos.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Primeiro, valide se você pode migrar a rede virtual usando o seguinte comando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    O comando a seguir exibe todos os avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá prosseguir com a seguinte etapa de Preparação:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Após a operação de Preparação ser bem-sucedida com uma das opções anteriores, consulte o estado de migração das VMs. Certifique-se de que elas estejam no estado `Prepared` .

Este exemplo define o nome da VM como **myVM**. Substitua o nome de exemplo pelo nome de sua própria VM.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Verifique a configuração dos recursos preparados usando o PowerShell ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Etapa 6.1: opção 2 - Migrar máquinas virtuais em uma rede virtual

Para migrar máquinas virtuais em uma rede virtual, migre a rede virtual. As máquinas virtuais são migradas automaticamente com a rede virtual. Selecione a rede virtual que você deseja migrar.
> [!NOTE]
> [Migre uma máquina virtual clássica individual](migrate-single-classic-to-resource-manager.md) criando uma nova máquina virtual do Resource Manager com Managed Disks usando os arquivos VHD (sistema operacional e dados) da máquina virtual.
<br>

> [!NOTE]
> O nome de rede virtual pode ser diferente do que é exibido no novo Portal. O novo Portal do Azure exibe o nome como `[vnet-name]` , mas o nome de rede virtual real é do tipo `Group [resource-group-name] [vnet-name]`. Antes de migrar, pesquisa o nome real de rede virtual usando o comando `Get-AzureVnetSite | Select -Property Name` ou exibi-lo no Portal do Azure antigo. 

Este exemplo define o nome de rede virtual como **myVnet**. Substitua o nome de exemplo pelo nome da sua própria rede virtual.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Se a rede virtual contiver funções Web ou de trabalho ou VMs com configurações sem suporte, você receberá uma mensagem de erro de validação.

Primeiro, valide se você pode migrar a rede virtual usando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

O comando a seguir exibe todos os avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá prosseguir com a seguinte etapa de Preparação:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Verifique a configuração para as máquinas virtuais preparadas usando o Azure PowerShell ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>Etapa 6.2 Migrar uma conta de armazenamento
Ao concluir a migração das máquinas virtuais, é recomendável executar as seguintes verificações de pré-requisitos antes de migrar as contas de armazenamento.

> [!NOTE]
> Se a conta de armazenamento não tiver dados de VM ou discos associados, você poderá ignorar e acessar diretamente a seção **Validar conta de armazenamento e iniciar migração**.

* **Os pré-requisitos verificam se você migrou qualquer VM ou se a conta de armazenamento possui recursos de Disco**
    * **Migrar máquinas virtuais clássicas cujos discos estão armazenados na conta de armazenamento**

        O comando a seguir retorna as propriedades RoleName e DiskName de todos os discos clássicos da VM na conta de armazenamento. RoleName é o nome da máquina virtual à qual um disco está anexado. Se esse comando retornar discos, certifique-se de que as máquinas virtuais às quais esses discos estão anexados sejam migradas antes de migrar a conta de armazenamento.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * **Excluir discos de VM clássica não anexados armazenados na conta de armazenamento**

        Localize discos de VM clássica não anexados na conta de armazenamento usando comando a seguir:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Se o comando acima retornar discos, exclua-os usando o seguinte comando:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * **Excluir imagens da VM armazenadas na conta de armazenamento**

        O comando a seguir retorna todas as imagens de VM com disco de SO armazenado na conta de armazenamento.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         O comando a seguir retorna todas as imagens de VM com discos de dados armazenados na conta de armazenamento.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Exclua todas as imagens de VM retornadas pelos comandos acima usando este comando:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* **Validar conta de armazenamento e iniciar migração**

    Valide cada conta de armazenamento para migração usando o comando a seguir. Neste exemplo, o nome da conta de armazenamento é **myStorageAccount**. Substitua o nome de exemplo pelo nome da sua própria conta de armazenamento.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    A próxima etapa é preparar a conta de armazenamento para migração

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Verifique a configuração da conta de armazenamento preparada usando o Azure PowerShell ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Próximas etapas
* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planejamento para a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas da comunidade para ajudar com a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Examinar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Confira as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
