---
title: Replicar VMs do Hyper-V com PowerShell e o Azure Resource Manager | Microsoft Docs
description: "Automatize a replicação de VMs do Hyper-V no Azure com o Azure Site Recovery usando o PowerShell e o Azure Resource Manager."
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhiag
editor: 
ms.assetid: 05e0d76e-c3f5-4845-8052-094019b6d102
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/31/2017
ms.author: bsiva
ms.openlocfilehash: dbd562b73b0caecd0feb993bd6fb796dddb0438c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>Replicar entre máquinas virtuais locais do Hyper-V e o Azure usando o PowerShell e o Azure Resource Manager
> [!div class="op_single_selector"]
> * [Portal do Azure](site-recovery-hyper-v-site-to-azure.md)
> * [PowerShell – Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
> * [Portal clássico](site-recovery-hyper-v-site-to-azure-classic.md)
>
>

## <a name="overview"></a>Visão geral
O Azure Site Recovery contribui para sua estratégia de continuidade dos negócios e recuperação de desastre orquestrando a replicação, o failover e a recuperação de máquinas virtuais em diversos cenários de implantação. Para obter uma lista completa de cenários de implantação, confira a [visão geral do Azure Site Recovery](site-recovery-overview.md).

O PowerShell no Azure é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell. Ele pode trabalhar com dois tipos de módulos: o módulo do Perfil do Azure ou o módulo do Azure Resource Manager.

Os cmdlets do PowerShell da Recuperação de Site disponíveis com o Azure PowerShell para o Azure Resource Manager permitem que você proteja e recupere seus servidores no Azure.

Este artigo descreve como usar o Windows PowerShell, juntamente com o Azure Resource Manager, na implantação da Recuperação de Site para configurar e orquestrar a proteção de servidor no Azure. O exemplo usado neste artigo mostra como proteger, executar failover e recuperar máquinas virtuais em um host Hyper-V no Azure usando o Azure PowerShell com o Azure Resource Manager.

> [!NOTE]
> Atualmente, os cmdlets do PowerShell da Recuperação de Site permitem configurar o seguinte: um site do Virtual Machine Manager em outro, um site do Virtual Machine Manager no Azure e um site do Hyper-V no Azure.
>
>

Não é preciso ser um especialista no PowerShell para usar este artigo, mas é necessário entender os conceitos básicos, como módulos, cmdlets e sessões. Para obter mais informações sobre o Windows PowerShell, consulte [Introdução ao PowerShell do Microsoft Azure (a página pode estar em inglês)](http://technet.microsoft.com/library/hh857337.aspx).

Leia mais sobre isso em [Usando o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Os parceiros da Microsoft que fazem parte do programa CSP (Provedor de Solução na Nuvem) podem configurar e gerenciar a proteção dos servidores de seus clientes em suas respectivas assinaturas de CSP dos clientes (assinaturas de locatário).
>
>

## <a name="before-you-start"></a>Antes de começar
Verifique se estes pré-requisitos estão em vigor:

* Uma conta do [Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). Além disso, você pode ler sobre [preços do Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell 1.0. Para obter informações sobre essa versão e como instalá-la, confira [Azure PowerShell 1.0.](https://azure.microsoft.com/)
* Os módulos [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) e [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/). É possível obter as versões mais recentes desses módulos na [Galeria do PowerShell](https://www.powershellgallery.com/)

Este artigo ilustra como usar o Azure PowerShell com o Azure Resource Manager para configurar e gerenciar a proteção de seus servidores. O exemplo usado neste artigo mostra como proteger uma máquina virtual, em execução em um host Hyper-V, no Azure. Os pré-requisitos a seguir são específicos a esse exemplo. Para obter um conjunto mais abrangente dos requisitos para os vários cenários da Recuperação de Site, consulte a documentação referente a um cenário específico.

* Um host Hyper-V que executa o Windows Server 2012 R2 ou o Microsoft Hyper-V Server 2012 R2 contendo uma ou mais máquinas virtuais.
* Servidores Hyper-V conectados à Internet, diretamente ou por meio de um proxy.
* As máquinas virtuais que você deseja proteger devem estar em conformidade com os [Pré-requisitos para Máquinas Virtuais](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

## <a name="step-1-sign-in-to-your-azure-account"></a>Etapa 1: Entrar em sua conta do Azure
1. Abra um console do PowerShell e execute este comando para entrar em sua conta do Azure. O cmdlet abre uma página da Web que solicitará suas credenciais de conta.

        Login-AzureRmAccount

    Como alternativa, você também poderá incluir suas credenciais de conta como parâmetro para o cmdlet `Login-AzureRmAccount` usando o parâmetro `-Credential`.

    Se você é um parceiro CSP trabalhando em nome de um locatário, especifique o cliente como um locatário usando sua tenantID ou o nome de domínio primário do locatário.

        Login-AzureRmAccount -Tenant "fabrikam.com"
2. Uma conta pode ter várias assinaturas, de modo que você deverá associar à conta a assinatura que deseja usar.

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName
3. Verifique se sua assinatura está registrada para usar os provedores do Azure para os Serviços de Recuperação e o Site Recovery usando os seguintes comandos:

   * `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
   * `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

   Na saída desses comandos, se o **RegistrationState** estiver definido como **Registrado**, você poderá seguir para a Etapa 2. Caso contrário, você deverá registrar o provedor ausente em sua assinatura.

   Para registrar o provedor do Azure para a Recuperação de Site e os Serviços de Recuperação, execute os seguintes comandos:

       Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
       Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

   Verifique se os provedores foram registrados com êxito usando os seguintes comandos: `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` e `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.

## <a name="step-2-set-up-the-recovery-services-vault"></a>Etapa 2: configurar o cofre dos Serviços de Recuperação
1. Crie um grupo de recursos do Azure Resource Manager no qual você criará o cofre ou use um grupo de recursos existente. Você pode criar um novo grupo de recursos usando o seguinte comando:

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    em que a variável $ResourceGroupName contém o nome do grupo de recursos que você deseja criar e a variável $Geo contém a região do Azure na qual o grupo de recursos será criado (por exemplo, “Sul do Brasil”).

    É possível obter uma lista de grupos de recursos em sua assinatura usando o cmdlet `Get-AzureRmResourceGroup` .
2. Crie um novo cofre dos Serviços de Recuperação do Azure da seguinte maneira:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    É possível recuperar uma lista de cofres existentes usando o cmdlet `Get-AzureRmRecoveryServicesVault`.

> [!NOTE]
> Se quiser executar operações nos cofres da Recuperação de Site criados usando o portal clássico ou o módulo do PowerShell do Gerenciamento de Serviços do Azure, você poderá recuperar uma lista de cofres desse tipo usando o cmdlet `Get-AzureRmSiteRecoveryVault`. É necessário criar um novo cofre dos Serviços de Recuperação para todas as novas operações. Há suporte para os cofres da Recuperação de Site criados anteriormente, mas eles não têm os recursos mais recentes.
>
>

## <a name="step-3-set-the-recovery-services-vault-context"></a>Etapa 3: Configurar o contexto do Cofre dos Serviços de Recuperação
1. Defina o contexto de cofre executando o seguinte comando:

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>Etapa 4: Criar um site do Hyper-V e gerar uma nova chave de registro do cofre para o site.
1. Crie um novo site do Hyper-V da seguinte maneira:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    Este cmdlet inicia um trabalho da Recuperação de Site para a criação do site e retorna um objeto de trabalho da Recuperação de Site. Aguarde a conclusão do trabalho e verifique se ele foi concluído com êxito.

    É possível recuperar o objeto de trabalho e verificar o status atual do trabalho usando o cmdlet Get-AzureRmSiteRecoveryJob.
2. Gere e baixe uma chave de registro para o site da seguinte maneira:

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    Copie a chave baixada no host Hyper-V. Você precisa da chave para registrar o host Hyper-V no site.

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>Etapa 5: Instalar o provedor do Azure Site Recovery e o Agente dos Serviços de Recuperação do Azure no host Hyper-V
1. Baixe o instalador para obter a versão mais recente do provedor na [Microsoft](https://aka.ms/downloaddra).
2. Execute o instalador no host Hyper-V e, ao final da instalação, siga para a etapa de registro.
3. Quando solicitado, forneça a chave de registro do site baixada e conclua o registro do host Hyper-V no site.
4. Verifique se o host Hyper-V foi registrado no site usando o seguinte comando:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>Etapa 6: Criar uma política de replicação e associá-la ao contêiner de proteção
1. Crie uma política de replicação da seguinte maneira:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    Verifique o trabalho retornado para garantir que a criação da política de replicação foi bem-sucedida.

   > [!IMPORTANT]
   > A conta de armazenamento especificada deve estar na mesma região do Azure que o cofre dos Serviços de Recuperação e deve ter a replicação geográfica habilitada.
   >
   > * Se a conta de armazenamento de Recuperação especificada for do tipo Armazenamento do Azure (Clássico), o failover dos computadores protegidos recuperará o computador no Azure IaaS (Clássico).
   > * Se a conta de armazenamento de Recuperação especificada for do tipo Armazenamento do Azure (Azure Resource Manager), o failover dos computadores protegidos recuperará o computador no Azure IaaS (Azure Resource Manager).
   >
   >
2. Obtenha o contêiner de proteção corresponde ao site da seguinte maneira:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. Inicie a associação do contêiner de proteção à política de replicação da seguinte maneira:

     $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName   $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

   Aguarde a conclusão do trabalho de associação e certifique-se de que ele foi concluído com êxito.

## <a name="step-7-enable-protection-for-virtual-machines"></a>Etapa 7: habilitar a proteção para máquinas virtuais
1. Obtenha a entidade de proteção correspondente à VM que você deseja proteger, da seguinte maneira:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Inicie a proteção da máquina virtual, conforme mostrado abaixo:

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

   > [!IMPORTANT]
   > A conta de armazenamento especificada deve estar na mesma região do Azure que o cofre dos Serviços de Recuperação e deve ter a replicação geográfica habilitada.
   >
   > * Se a conta de armazenamento de Recuperação especificada for do tipo Armazenamento do Azure (Clássico), o failover dos computadores protegidos recuperará o computador no Azure IaaS (Clássico).
   > * Se a conta de armazenamento de Recuperação especificada for do tipo Armazenamento do Azure (Azure Resource Manager), o failover dos computadores protegidos recuperará o computador no Azure IaaS (Azure Resource Manager).
   >
   > Se a VM que você estiver protegendo tiver mais de um disco anexado, será necessário especificar o disco do sistema operacional usando o parâmetro *OSDiskName* .
   >
   >
3. Aguarde até que as máquinas virtuais atinjam um estado protegido após a replicação inicial. Isso pode demorar um pouco, dependendo de fatores como a quantidade de dados a serem replicados e a largura de banda upstream disponível no Azure. O State e StateDescription do trabalho são atualizados da seguinte maneira, depois que a VM atingir um estado protegido.

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Atualize propriedades de recuperação, como o tamanho da função VM, e a rede do Azure que será usada para anexar placas de interface de rede da máquina virtual após o failover.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob

        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Etapa 8: executar um failover de teste
1. Execute um trabalho de failover de teste, da seguinte maneira:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Verifique se a VM de teste foi criada no Azure. (O trabalho de failover de teste é suspenso após a criação da VM de teste no Azure. O trabalho será concluído com a limpeza dos artefatos criados ao retomar o trabalho, conforme ilustrado na próxima etapa.)
3. Conclua o failover de teste da seguinte maneira:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>Próximas etapas
[Leia mais](https://msdn.microsoft.com/library/azure/mt637930.aspx) sobre o Azure Site Recovery com cmdlets do PowerShell do Azure Resource Manager.
