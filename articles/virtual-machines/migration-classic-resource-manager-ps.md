---
title: Migrar para o Gerenciador de recursos com o PowerShell
description: Este artigo percorre a migração de recursos de IaaS com suporte da plataforma, como VMs (máquinas virtuais), redes virtuais e contas de armazenamento do clássico para Azure Resource Manager usando comandos Azure PowerShell
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c750e34e8081cf5a8b3d41cc8c52584a4353a336
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695151"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migrar recursos de IaaS do clássico para o Azure Resource Manager usando o PowerShell

> [!IMPORTANT]
> Hoje, cerca de 90% das VMs de IaaS estão usando [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). A partir de 28 de fevereiro de 2020, as VMs clássicas foram preteridas e serão totalmente desativadas em 1º de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre essa reprovação e [como ela afeta você](classic-vm-deprecation.md#how-does-this-affect-me).

Estas etapas mostram como usar os comandos do Azure PowerShell para migrar os recursos de IaaS (infraestrutura como serviço) do modelo de implantação clássico para o Modelo de implantação do Azure Resource Manager.

Se desejar, você também pode migrar recursos usando o [CLI do Azure](migration-classic-resource-manager-cli.md).

* Para obter informações sobre cenários de migração com suporte, confira [Migração de recursos de IaaS com suporte da plataforma do clássico ao Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Para obter orientação e um passo a passo sobre a migração, confira [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico ao Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Examine os erros de migração mais comuns](migration-classic-resource-manager-errors.md).

<br>
Aqui está um fluxograma para identificar a ordem em que as etapas precisam ser executadas durante um processo de migração.

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Etapa 1: Planejar a migração
Aqui estão algumas práticas recomendadas que recomendamos à medida que você avaliar se deseja migrar os recursos de IaaS do clássico para o Resource Manager:

* Leia os [recursos e configurações com e sem suporte](migration-classic-resource-manager-overview.md). Se você tiver máquinas virtuais que usam recursos ou configurações sem suporte, aguarde até que a configuração ou o suporte a recursos sejam anunciados. Como alternativa, se isso atender às suas necessidades, remova esse recurso ou mude a configuração para habilitar a migração.
* Se você tiver scripts automatizados que implantam sua infraestrutura e aplicativos atualmente, tente criar uma configuração de teste semelhante usando esses scripts para migração. Você também pode configurar ambientes de exemplo usando o portal do Azure.

> [!IMPORTANT]
> Atualmente, os gateways de aplicativo não têm suporte para migração do clássico para o Gerenciador de recursos. Para migrar uma rede virtual com um gateway de aplicativo, remova o gateway antes de executar uma operação de preparação para mover a rede. Depois de concluir a migração, reconecte o gateway no Azure Resource Manager.
>
> Gateways do Azure ExpressRoute que se conectam a circuitos do ExpressRoute em outra assinatura não podem ser migrados automaticamente. Nesses casos, remova o gateway de ExpressRoute, migre a rede virtual e recrie o gateway. Para obter mais informações, consulte [migrar circuitos de ExpressRoute e redes virtuais associadas do modelo de implantação clássico para o Gerenciador de recursos](../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="step-2-install-the-latest-version-of-powershell"></a>Etapa 2: instalar a versão mais recente do PowerShell
Há duas opções principais para instalar o Azure PowerShell, a [Galeria do PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) e o [WebPI (Web Platform Installer)](https://aka.ms/webpi-azps). WebPI recebe atualizações mensais. A Galeria do PowerShell receberá atualizações continuamente. Este artigo tem base no Azure PowerShell versão 2.1.0.

Para obter instruções de instalação, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/).

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Etapa 3: Verifique se você é um administrador da assinatura
Para executar essa migração, você deve ser adicionado como um coadministrador para a assinatura no [portal do Azure](https://portal.azure.com).

1. Entre no [portal do Azure](https://portal.azure.com).
2. No menu **Hub** , selecione **assinatura**. Caso não visualize essa opção, selecione **Todos os serviços**.
3. Localize a entrada de assinatura apropriada e, em seguida, examine o campo **minha função** . Para um coadministrador, o valor deve ser _administrador da conta_.

Se não for possível adicionar um coadministrador, entre em contato com um administrador de serviços ou coadministrador da assinatura para se tornar adicionado.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Etapa 4: definir sua assinatura e inscrever-se para a migração
Primeiro, inicie um prompt do PowerShell. Para a migração, configure seu ambiente para o clássico e o Resource Manager.

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
> O registro é uma etapa única, mas você deve fazê-lo uma vez antes de tentar a migração. Sem o registro, você verá a seguinte mensagem de erro:
>
> *BadRequest: a assinatura não está registrada para migração.*

Registre-se no provedor de recursos de migração usando o comando a seguir:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Aguarde cinco minutos para que o registro seja concluído. Verifique o status da aprovação usando o seguinte comando:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Verifique se RegistrationState é `Registered` antes de continuar.

Antes de alternar para o modelo de implantação clássico, certifique-se de que você tenha Azure Resource Manager de máquina virtual suficiente para o vCPUs na região do Azure da sua implantação atual ou rede virtual. Você pode usar o seguinte comando do PowerShell para verificar a quantidade atual de vCPUs no Azure Resource Manager. Para saber mais sobre cotas de vCPUs, veja [Limites e o Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

Este exemplo verifica a disponibilidade na região **Oeste dos EUA**. Substitua o nome da região de exemplo pelo nome da sua própria região.

```powershell
    Get-AzVMUsage -Location "West US"
```

Agora, entre em sua conta para o modelo de implantação clássico.

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


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Etapa 5: Executar comandos para migrar os recursos de IaaS
* [Migrar VMs em um serviço de nuvem (não em uma rede virtual)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrar VMs em uma rede virtual](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrar uma conta de armazenamento](#step-52-migrate-a-storage-account)

> [!NOTE]
> Todas as operações descritas aqui são idempotentes. Caso você tenha algum problema que não seja um recurso sem suporte ou um erro de configuração, recomendamos que repita a operação de preparação, anulação ou confirmação. Em seguida, a plataforma tentará novamente a ação.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Etapa 5,1: opção 1-migrar máquinas virtuais em um serviço de nuvem (não em uma rede virtual)
Obtenha a lista de serviços de nuvem usando o comando a seguir. Em seguida, escolha o serviço de nuvem que você deseja migrar. Se as VMs no serviço de nuvem estiverem em uma rede virtual, ou se tiverem funções Web ou de trabalho, o comando retornará uma mensagem de erro.

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

* **Opção 1: migre as VMs para uma rede virtual criada por plataforma.**

    Primeiro, valide que você pode migrar o serviço de nuvem usando os seguintes comandos:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    O comando a seguir exibe todos os avisos e erros que bloqueiam a migração. Se as mensagens de validação não contiverem a mensagem do tipo erro, você poderá passar para a etapa de preparação.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opção 2: migrar para uma rede virtual existente no modelo de implantação do Gerenciador de recursos.**

    Este exemplo define o nome do grupo de recursos como **MyResource** Group, o nome da rede virtual como **myVirtualNetwork** e o nome da sub-rede como **mysubnet**. Substitua os nomes de exemplo pelos nomes de seus próprios recursos.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Primeiro, valide que você pode migrar a rede virtual usando o seguinte comando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    O comando a seguir exibe todos os avisos e erros que bloqueiam a migração. Se as mensagens de validação não contiverem erros, você poderá prosseguir com a seguinte etapa de preparação:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Após a operação de Preparação ser bem-sucedida com uma das opções anteriores, consulte o estado de migração das VMs. Verifique se eles estão no `Prepared` estado.

Este exemplo define o nome da VM como **myVM**. Substitua o nome de exemplo pelo nome de sua própria VM.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Verifique a configuração dos recursos preparados usando o PowerShell ou o Portal do Azure. Se você não estiver pronto para a migração e quiser voltar ao estado antigo, use o seguinte comando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Etapa 5,1: opção 2-migrar máquinas virtuais em uma rede virtual

Para migrar máquinas virtuais em uma rede virtual, migre a rede virtual. As máquinas virtuais são migradas automaticamente com a rede virtual. Selecione a rede virtual que você deseja migrar.
> [!NOTE]
> [Migre uma única máquina virtual](./windows/create-vm-specialized-portal.md) criada usando o modelo de implantação clássico criando uma nova máquina virtual do Resource Manager com Managed disks usando os arquivos VHD (so e dados) da máquina virtual.
<br>

> [!NOTE]
> O nome da rede virtual pode ser diferente do que é mostrado no novo Portal. O novo portal do Azure exibe o nome como `[vnet-name]` , mas o nome real da rede virtual é do tipo `Group [resource-group-name] [vnet-name]` . Antes de iniciar a migração, procure o nome da rede virtual real usando o comando `Get-AzureVnetSite | Select -Property Name` ou exiba-o no portal do Azure antigo. 

Este exemplo define o nome de rede virtual como **myVnet**. Substitua o nome de exemplo pelo nome da sua própria rede virtual.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Se a rede virtual contiver funções Web ou de trabalho ou VMs com configurações sem suporte, você receberá uma mensagem de erro de validação.

Primeiro, valide que você pode migrar a rede virtual usando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

O comando a seguir exibe todos os avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá prosseguir com a seguinte etapa de preparação:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Verifique a configuração para as máquinas virtuais preparadas usando o Azure PowerShell ou o Portal do Azure. Se você não estiver pronto para a migração e quiser voltar ao estado antigo, use o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>Etapa 5,2: migrar uma conta de armazenamento
Depois de terminar de migrar as máquinas virtuais, execute as seguintes verificações de pré-requisitos antes de migrar as contas de armazenamento.

> [!NOTE]
> Se sua conta de armazenamento não tiver discos associados ou dados de VM, você poderá pular diretamente para a seção "validar contas de armazenamento e iniciar a migração". Observe também que a exclusão dos discos clássicos, imagens de VM ou imagens do sistema operacional não remove os arquivos VHD de origem na conta de armazenamento. No entanto, ele interrompe a concessão nesses arquivos VHD para que eles possam ser reutilizados para criar discos ou imagens ARM após a migração.

* As verificações de pré-requisitos se você migrou qualquer VM ou sua conta de armazenamento tem recursos de disco:
    * Migre máquinas virtuais cujos discos estejam armazenados na conta de armazenamento.

        O comando a seguir retorna as propriedades RoleName e diskname de todos os discos de VM na conta de armazenamento. RoleName é o nome da máquina virtual à qual um disco está anexado. Se esse comando retornar discos, verifique se as máquinas virtuais para as quais esses discos estão anexados são migradas antes de migrar a conta de armazenamento.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Exclua os discos de VM desanexados armazenados na conta de armazenamento.

        Localize discos de VM desconectados na conta de armazenamento usando o seguinte comando:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Se o comando anterior retornar discos, exclua esses discos usando o seguinte comando:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Exclua as imagens de VM armazenadas na conta de armazenamento.

        O comando a seguir retorna todas as imagens de VM com discos de sistema operacional armazenados na conta de armazenamento.
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
        Exclua todas as imagens de VM retornadas pelos comandos anteriores usando este comando:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Valide as contas de armazenamento e inicie a migração.

    Valide cada conta de armazenamento para migração usando o comando a seguir. Neste exemplo, o nome da conta de armazenamento é **myStorageAccount**. Substitua o nome de exemplo pelo nome da sua própria conta de armazenamento.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    A próxima etapa é preparar a conta de armazenamento para a migração.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Verifique a configuração da conta de armazenamento preparada usando o Azure PowerShell ou o Portal do Azure. Se você não estiver pronto para a migração e quiser voltar ao estado antigo, use o seguinte comando:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Próximas etapas
* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planejamento para a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Ferramentas da comunidade para ajudar com a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Examinar os erros de migração mais comuns](migration-classic-resource-manager-errors.md)
* [Confira as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md)
