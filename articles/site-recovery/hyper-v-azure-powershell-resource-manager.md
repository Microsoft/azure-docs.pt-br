---
title: Configurar a recuperação de desastre para o Azure para VMs do Hyper-V usando o PowerShell e o Azure Resource Manager | Microsoft Docs
description: Automatize a recuperação de desastre de VMs do Hyper-V para o Azure com o serviço Azure Site Recovery usando o PowerShell e o Azure Resource Manager.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 06/18/2019
ms.author: sutalasi
ms.openlocfilehash: bc1d52a1062d1848daaaeef7977f96cd270567c8
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203461"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Configurar a recuperação de desastres para o Azure para máquinas virtuais do Hyper-V usando o PowerShell e o Azure Resource Manager

O [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais (VMs) do Azure e servidores físicos e VMs locais.

Este artigo descreve como usar o Windows PowerShell, junto com o Azure Resource Manager para replicar máquinas virtuais do Hyper-V no Azure. O exemplo usado neste artigo mostra como replicar uma única VM em execução em um host Hyper-V no Azure.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

O Azure PowerShell fornece cmdlets para gerenciar o Azure usando o Windows PowerShell. Os cmdlets do PowerShell da Recuperação de Site disponíveis com o Azure PowerShell para o Azure Resource Manager permitem que você proteja e recupere seus servidores no Azure.

Não é preciso ser um especialista no PowerShell para usar este artigo, mas é necessário entender os conceitos básicos, como módulos, cmdlets e sessões. Leia [Introdução ao Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx) e [Uso do Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Os parceiros da Microsoft no programa CSP (Provedor de Solução na Nuvem) podem configurar e gerenciar a proteção dos servidores de clientes em suas respectivas assinaturas de CSP (assinaturas de locatário).
>
>

## <a name="before-you-start"></a>Antes de começar
Verifique se estes pré-requisitos estão em vigor:

* Uma conta do [Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). Além disso, você pode ler sobre [preços do Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* PowerShell do Azure. Para obter informações sobre essa versão e como instalá-lo, consulte [instalar o Azure PowerShell](/powershell/azure/install-az-ps).

Além disso, o exemplo específico descrito neste artigo tem os seguintes pré-requisitos:

* Um host Hyper-V que executa o Windows Server 2012 R2 ou o Microsoft Hyper-V Server 2012 R2 contendo uma ou mais máquinas virtuais. Os servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.
* As máquinas virtuais que você deseja replicar devem estar em conformidade com [esses pré-requisitos](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Etapa 1: Entre na sua conta do Azure

1. Abra um console do PowerShell e execute este comando para entrar em sua conta do Azure. Esse cmdlet abre uma página da Web que solicita suas credenciais de conta: **Connect-AzAccount**.
    - Como alternativa, é possível incluir as credenciais de conta como um parâmetro no cmdlet **Connect-AzAccount**, usando o parâmetro **-Credential**.
    - Se você é um parceiro CSP trabalhando em nome de um locatário, especifique o cliente como um locatário usando sua tenantID ou o nome de domínio primário do locatário. Por exemplo:  **Connect-AzAccount -Tenant "fabrikam.com"**
2. Associe a assinatura que deseja usar com a conta, uma vez que uma conta pode ter várias assinaturas:

    `Select-AzSubscription -SubscriptionName $SubscriptionName`

3. Verifique se sua assinatura está registrada para usar os provedores do Azure para os Serviços de Recuperação e o Site Recovery usando os seguintes comandos:

    `Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`

4. Se, na saída de comando, o **RegistrationState** estiver definido como **Registrado**, você poderá prosseguir para a Etapa 2. Caso contrário, você deverá registrar o provedor ausente em sua assinatura executando estes comandos:

    `Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Verifique se os provedores foram registrados com êxito usando os seguintes comandos:

    `Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`

## <a name="step-2-set-up-the-vault"></a>Etapa 2: Configurar o cofre

1. Crie um grupo de recursos do Azure Resource Manager no qual você criará o cofre ou use um grupo de recursos existente. Crie um novo grupo de recursos da seguinte maneira. A variável $ResourceGroupName contém o nome do grupo de recursos que você deseja criar e a variável $Geo contém a região do Azure na qual o grupo de recursos será criado (por exemplo, “Sul do Brasil”).

    `New-AzResourceGroup -Name $ResourceGroupName -Location $Geo`

2. Para obter uma lista de grupos de recursos em sua assinatura, execute as **Get-AzResourceGroup** cmdlet.
2. Crie um novo cofre dos Serviços de Recuperação do Azure da seguinte maneira:

        $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Você pode recuperar uma lista de cofres existentes com o **Get-AzRecoveryServicesVault** cmdlet.


## <a name="step-3-set-the-recovery-services-vault-context"></a>Etapa 3: Configurar o contexto do Cofre dos Serviços de Recuperação

Defina o contexto do cofre da seguinte maneira:

`Set-AsrVaultSettings -Vault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>Etapa 4: Criar um site Hyper-V

1. Crie um novo site do Hyper-V da seguinte maneira:

        $sitename = "MySite"                #Specify site friendly name
        New-AsrFabric -Type HyperVSite -Name $sitename

2. Este cmdlet inicia um trabalho da Recuperação de Site para a criação do site e retorna um objeto de trabalho da Recuperação de Site. Aguarde a conclusão do trabalho e verifique se ele foi concluído com êxito.
3. Use o **cmdlet Get-AsrJob** para recuperar o objeto de trabalho e verifique o status atual do trabalho.
4. Gere e baixe uma chave de registro para o site da seguinte maneira:

    ```
    $SiteIdentifier = Get-AsrFabric -Name $sitename | Select -ExpandProperty SiteIdentifier
    $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
    ```

5. Copie a chave baixada no host Hyper-V. Você precisa da chave para registrar o host Hyper-V no site.

## <a name="step-5-install-the-provider-and-agent"></a>Etapa 5: Instalar o Provedor e o agente

1. Baixe o instalador para obter a versão mais recente do provedor na [Microsoft](https://aka.ms/downloaddra).
2. Execute o instalador no host Hyper-V.
3. No final da instalação, vá para a etapa de registro.
4. Quando solicitado, forneça a chave baixada e conclua o registro do host Hyper-V.
5. Verifique se o host Hyper-V foi registrado no site da seguinte maneira:

        $server =  Get-AsrFabric -Name $siteName | Get-AsrServicesProvider -FriendlyName $server-friendlyname

Se você estiver executando um servidor de núcleo do Hyper-V, baixe o arquivo de instalação e siga estas etapas:
1. Extraia os arquivos do AzureSiteRecoveryProvider.exe para um diretório local executando este comando: ```AzureSiteRecoveryProvider.exe /x:. /q```
2. Executar ```.\setupdr.exe /i``` resultados são registrados em % Programdata%\ASRLogs\DRASetupWizard.log.

3. Registre o servidor executando este comando:

    ```cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"```


## <a name="step-6-create-a-replication-policy"></a>Etapa 6: Criar uma política de replicação

Antes de iniciar, observe que a conta de armazenamento especificada deve estar na mesma região do Azure que o cofre e deve ter a replicação geográfica habilitada.

1. Crie uma política de replicação da seguinte maneira:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Verifique o trabalho retornado para garantir que a criação da política de replicação foi bem-sucedida.

3. Recupere o contêiner de proteção corresponde ao site da seguinte maneira:

        $protectionContainer = Get-AsrProtectionContainer
3. Associe o contêiner de proteção à política de replicação da seguinte maneira:

     $Policy = get-AsrPolicy - FriendlyName $PolicyName $associationJob = New-AsrProtectionContainerMapping-nome $mappingName-política $Policy - PrimaryProtectionContainer $protectionContainer [0]

4. Aguarde o conclusão bem-sucedida do trabalho de associação.

## <a name="step-7-enable-vm-protection"></a>Etapa 7: Habilitar a proteção de VM

1. Recupere o item protegido que corresponde à VM que você deseja proteger, da seguinte maneira:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $ProtectableItem = Get-AsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Proteja a VM. Se a VM que você estiver protegendo tiver mais de um disco anexado, será necessário especificar o disco do sistema operacional usando o parâmetro *OSDiskName* .

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = New-AsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS Windows -RecoveryResourceGroupId

3. Aguarde até que as máquinas virtuais atinjam um estado protegido após a replicação inicial. Isso pode demorar um pouco, dependendo de fatores como a quantidade de dados a serem replicados e a largura de banda upstream disponível no Azure. Quando um estado protegido estiver implantado, o State e StateDescription do trabalho são atualizados da seguinte maneira:

        PS C:\> $DRjob = Get-AsrJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Atualize as propriedades de recuperação (como o tamanho da função VM) e a rede do Azure à qual o NIC da VM será anexado após o failover.

        PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AsrReplicationProtectedItem --InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AsrJob -Job $UpdateJob

        PS C:\> $UpdateJob| select -ExpandProperty state
        Get-AsrJob -Job $job | select -ExpandProperty state

        Succeeded



## <a name="step-8-run-a-test-failover"></a>Etapa 8: Execute um teste de failover
1. Execute o failover de teste da seguinte maneira:

        $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        $TFjob =Start-AsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Verifique se a VM de teste foi criada no Azure. O trabalho de failover de teste é suspenso após a criação da VM de teste no Azure.
3. Para limpar e concluir o failover de teste, execute:

        $TFjob = Start-AsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"

## <a name="next-steps"></a>Próximas etapas
[Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices) sobre o Azure Site Recovery com cmdlets do PowerShell do Azure Resource Manager.
