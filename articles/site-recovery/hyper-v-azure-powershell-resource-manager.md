---
title: Recuperação de desastre de VM Hyper-V usando Azure Site Recovery e PowerShell
description: Automatize a recuperação de desastre de VMs do Hyper-V para o Azure com o serviço Azure Site Recovery usando o PowerShell e o Azure Resource Manager.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4140a0b22f7ca8164d50cf60fe57c861f826eb4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86132509"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Configurar a recuperação de desastres para o Azure para máquinas virtuais do Hyper-V usando o PowerShell e o Azure Resource Manager

O [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais (VMs) do Azure e servidores físicos e VMs locais.

Este artigo descreve como usar o Windows PowerShell, junto com o Azure Resource Manager para replicar máquinas virtuais do Hyper-V no Azure. O exemplo usado neste artigo mostra como replicar uma única VM em execução em um host Hyper-V no Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

O Azure PowerShell fornece cmdlets para gerenciar o Azure usando o Windows PowerShell. Os cmdlets do PowerShell da Recuperação de Site disponíveis com o Azure PowerShell para o Azure Resource Manager permitem que você proteja e recupere seus servidores no Azure.

Não é preciso ser um especialista no PowerShell para usar este artigo, mas é necessário entender os conceitos básicos, como módulos, cmdlets e sessões. Para obter mais informações, consulte a [documentação do PowerShell](/powershell) e [usando Azure PowerShell com Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

> [!NOTE]
> Os parceiros da Microsoft no programa CSP (Provedor de Solução na Nuvem) podem configurar e gerenciar a proteção dos servidores de clientes em suas respectivas assinaturas de CSP (assinaturas de locatário).

## <a name="before-you-start"></a>Antes de começar

Verifique se estes pré-requisitos estão em vigor:

- Uma conta de [Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). Além disso, você pode ler sobre [preços do Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
- PowerShell do Azure. Para obter informações sobre esta versão e como instalá-la, consulte [install Azure PowerShell](/powershell/azure/install-az-ps).

Além disso, o exemplo específico descrito neste artigo tem os seguintes pré-requisitos:

- Um host Hyper-V que executa o Windows Server 2012 R2 ou o Microsoft Hyper-V Server 2012 R2 contendo uma ou mais máquinas virtuais. Os servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.
- As máquinas virtuais que você deseja replicar devem estar em conformidade com [esses pré-requisitos](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Etapa 1: Entrar em sua conta do Azure

1. Abra um console do PowerShell e execute este comando para entrar em sua conta do Azure. O cmdlet abre uma página da Web que solicita suas credenciais de conta: `Connect-AzAccount` .
   - Como alternativa, você pode incluir suas credenciais de conta como um parâmetro no `Connect-AzAccount` cmdlet, usando o parâmetro **Credential** .
   - Se você for um parceiro CSP trabalhando em nome de um locatário, especifique o cliente como um locatário, usando seu nome de domínio de locatárioid ou de locatário primário. Por exemplo: `Connect-AzAccount -Tenant "fabrikam.com"`
1. Associe a assinatura que deseja usar com a conta, uma vez que uma conta pode ter várias assinaturas:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Verifique se sua assinatura está registrada para usar os provedores do Azure para os Serviços de Recuperação e o Site Recovery usando os seguintes comandos:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Se, na saída de comando, o **RegistrationState** estiver definido como **Registrado**, você poderá prosseguir para a Etapa 2. Caso contrário, você deverá registrar o provedor ausente em sua assinatura executando estes comandos:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Verifique se os provedores foram registrados com êxito usando os seguintes comandos:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>Etapa 2: configurar o cofre

1. Crie um grupo de recursos do Azure Resource Manager no qual você criará o cofre ou use um grupo de recursos existente. Crie um novo grupo de recursos da seguinte maneira. A `$ResourceGroupName` variável contém o nome do grupo de recursos que você deseja criar e a variável $geo contém a região do Azure na qual criar o grupo de recursos (por exemplo, "sul do Brasil").

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Para obter uma lista de grupos de recursos em sua assinatura, execute o `Get-AzResourceGroup` cmdlet.
1. Crie um novo cofre dos Serviços de Recuperação do Azure da seguinte maneira:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

Você pode recuperar uma lista de cofres existentes com o `Get-AzRecoveryServicesVault` cmdlet.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Etapa 3: Configurar o contexto do Cofre dos Serviços de Recuperação

Defina o contexto do cofre da seguinte maneira:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>Etapa 4: criar um site do Hyper-V

1. Crie um novo site do Hyper-V da seguinte maneira:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. Este cmdlet inicia um trabalho da Recuperação de Site para a criação do site e retorna um objeto de trabalho da Recuperação de Site. Aguarde a conclusão do trabalho e verifique se ele foi concluído com êxito.
1. Use o `Get-AzRecoveryServicesAsrJob` cmdlet para recuperar o objeto de trabalho e verificar o status atual do trabalho.
1. Gere e baixe uma chave de registro para o site da seguinte maneira:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Copie a chave baixada no host Hyper-V. Você precisa da chave para registrar o host Hyper-V no site.

## <a name="step-5-install-the-provider-and-agent"></a>Etapa 5: instalar o Provedor e o agente

1. Baixe o instalador para obter a versão mais recente do provedor da [Microsoft](https://aka.ms/downloaddra).
1. Execute o instalador no host do Hyper-V.
1. No final da instalação, vá para a etapa de registro.
1. Quando solicitado, forneça a chave baixada e conclua o registro do host Hyper-V.
1. Verifique se o host Hyper-V foi registrado no site da seguinte maneira:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Se você estiver executando um servidor de núcleo do Hyper-V, baixe o arquivo de instalação e siga estas etapas:

1. Extraia os arquivos do _AzureSiteRecoveryProvider.exe_ para um diretório local executando este comando:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Execute o comando a seguir:

   ```console
   .\setupdr.exe /i
   ```

   Os resultados são registrados em _%ProgramData%\ASRLogs\DRASetupWizard.log_.

1. Registre o servidor executando este comando:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>Etapa 6: criar uma política de replicação

Antes de começar, a conta de armazenamento especificada deve estar na mesma região do Azure que o cofre e deve ter a replicação geográfica habilitada.

1. Crie uma política de replicação da seguinte maneira:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. Verifique o trabalho retornado para garantir que a criação da política de replicação foi bem-sucedida.

1. Recupere o contêiner de proteção corresponde ao site da seguinte maneira:

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. Associe o contêiner de proteção à política de replicação da seguinte maneira:

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. Aguarde o conclusão bem-sucedida do trabalho de associação.

1. Recupere o mapeamento de contêiner de proteção.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>Etapa 7: habilitar a proteção de VM

1. Recupere o item protegido que corresponde à VM que você deseja proteger, da seguinte maneira:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. Proteja a VM. Se a VM que você está protegendo tiver mais de um disco anexado a ela, especifique o disco do sistema operacional usando o parâmetro **OSDiskName** .

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Aguarde até que as máquinas virtuais atinjam um estado protegido após a replicação inicial. Isso pode demorar um pouco, dependendo de fatores como a quantidade de dados a serem replicados e a largura de banda upstream disponível no Azure. Quando um estado protegido estiver implantado, o State e StateDescription do trabalho são atualizados da seguinte maneira:

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. Atualize as propriedades de recuperação (como o tamanho da função da VM) e a rede do Azure à qual o NIC da VM será anexado após o failover.

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> Se você quiser replicar em discos gerenciados habilitados para o CMK no Azure, execute as seguintes etapas usando AZ PowerShell 3.3.0 em diante:
>
> 1. Habilitar o failover para discos gerenciados atualizando as propriedades da VM
> 1. Use o `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet para buscar a ID do disco de cada disco do item protegido
> 1. Crie um objeto Dictionary usando `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` o cmdlet para conter o mapeamento da ID do disco para o conjunto de criptografia de disco. Esses conjuntos de criptografia de disco devem ser criados previamente por você na região de destino.
> 1. Atualize as propriedades da VM usando `Set-AzRecoveryServicesAsrReplicationProtectedItem` o cmdlet, passando o objeto Dictionary no parâmetro **DiskIdToDiskEncryptionSetMap** .

## <a name="step-8-run-a-test-failover"></a>Etapa 8: Execute um teste de failover

1. Execute o failover de teste da seguinte maneira:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Verifique se a VM de teste foi criada no Azure. O trabalho de failover de teste é suspenso após a criação da VM de teste no Azure.
1. Para limpar e concluir o failover de teste, execute:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](/powershell/module/az.recoveryservices) sobre o Azure Site Recovery com cmdlets do PowerShell do Azure Resource Manager.
