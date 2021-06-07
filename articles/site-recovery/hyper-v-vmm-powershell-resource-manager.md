---
title: Configurar a recuperação de desastre do Hyper-V (com VMM) em um site secundário com Azure Site Recovery/PowerShell
description: Descreve como configurar a recuperação de desastre de VMs Hyper-V em nuvens do VMM para um site secundário do VMM usando o Azure Site Recovery e o PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4f7d330db9a4a0d9b435ebe7527e55e37c254e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87086210"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Configurar a recuperação de desastre de VMs Hyper-V para um site secundário usando PowerShell (Resource Manager)

Este artigo mostra como automatizar as etapas para a replicação de VMs do Hyper-V em nuvens de System Center Virtual Machine Manager para uma nuvem de Virtual Machine Manager em um site local secundário usando o [Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Examine os [componentes e a arquitetura do cenário](hyper-v-vmm-architecture.md).
- Examine os [requisitos de suporte](./vmware-physical-secondary-support-matrix.md) de todos os componentes.
- Verifique se os servidores do Virtual Machine Manager e os hosts do Hyper-V estão em conformidade com [os requisitos de suporte](./vmware-physical-secondary-support-matrix.md).
- Verifique se as VMs que você deseja replicar estão em conformidade com o [suporte ao computador replicado](./vmware-physical-secondary-support-matrix.md).

## <a name="prepare-for-network-mapping"></a>Prepare-se para o mapeamento de rede

O [mapeamento de rede](hyper-v-vmm-network-mapping.md) mapeia entre as redes de VM do Virtual Machine Manager locais de origem e as nuvens de destino. Mapeamento faz o seguinte:

- Conecta as VMs às redes de VM de destino apropriadas depois do failover.
- Quando possível, coloca VMs de réplica nos servidores host do Hyper-V de destino.
- Se você não configurar o mapeamento de rede, as VMs de réplica não serão conectadas a uma rede de VM após o failover.

Prepare o Virtual Machine Manager da seguinte maneira:

- Verifique se você tem [redes lógicas do Virtual Machine Manager](/system-center/vmm/network-logical) nos servidores de origem e de destino do Virtual Machine Manager:
  - A rede lógica no servidor de origem deve ser associada à nuvem de origem na qual os hosts do Hyper-V estão localizados.
  - A rede lógica no servidor de destino deve ser associada com a nuvem de destino.
- Verifique se você tem [redes de VM](/system-center/vmm/network-virtual) nos servidores de origem e de destino do Virtual Machine Manager. As redes de VM devem ser vinculadas à rede lógica em cada local.
- Conecte as VMs nos hosts do Hyper-V de origem à rede de VM de origem.

## <a name="prepare-for-powershell"></a>Preparar para o PowerShell

Verifique se você tem o PowerShell do Azure pronto para uso:

- Se você já usa o PowerShell, atualize para a versão 0.8.10 ou posterior. [Saiba mais](/powershell/azure/) sobre como configurar o PowerShell.
- Depois de instalar e configurar o PowerShell, revise os [cmdlets de serviço](/powershell/azure/).
- Para saber mais sobre como usar os valores, as entradas e as saídas do parâmetro no PowerShell, leia o guia [Introdução](/powershell/azure/get-started-azureps).

## <a name="set-up-a-subscription"></a>Configurar uma assinatura

1. No PowerShell, entre na sua conta do Azure.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. Recupere uma lista de suas assinaturas com as IDs de assinatura. Anote a ID da assinatura na qual você deseja criar o cofre dos Serviços de Recuperação.

   ```azurepowershell
   Get-AzSubscription
   ```

1. Configure a assinatura para o cofre.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

1. Se você não tiver um, crie um grupo de recursos do Azure Resource Manager.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. Crie um novo cofre dos Serviços de Recuperação. Salve o objeto de cofre em uma variável a ser usado mais tarde.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   Você pode recuperar o objeto de cofre depois de criá-lo usando o `Get-AzRecoveryServicesVault` cmdlet.

## <a name="set-the-vault-context"></a>Definir o contexto do cofre

1. Recupere um cofre existente.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. Definir o contexto do cofre.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>Instalar o provedor de Recuperação do Site

1. Na máquina da Virtual Machine Manager, crie um diretório executando o seguinte comando:

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. Extraia os arquivos usando o arquivo de instalação baixado do provedor.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Instale o provedor e aguarde a conclusão da instalação.

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. Registre o servidor no cofre.

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>Criar e associar uma política de replicação

1. Crie uma política de replicação, nesse caso para Hyper-V 2012 R2, como a seguir:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > A nuvem de Virtual Machine Manager pode conter hosts Hyper-V executando versões diferentes do Windows Server, mas a política de replicação é específica para uma versão do sistema operacional. Se você tiver hosts diferentes em execução em diferentes sistemas operacionais, crie políticas de replicação separadas para cada sistema. Por exemplo: se você tiver cinco hosts em execução no Windows Server 2012 e três hosts em execução no Windows Server 2012 R2, crie duas políticas de replicação. Você cria uma para cada tipo de sistema operacional.

1. Recupere o contêiner de proteção principal (nuvem de Virtual Machine Manager principal) e o contêiner de proteção de recuperação (nuvem de Virtual Machine Manager de recuperação).

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. Recupere a política de replicação criada usando o nome amigável.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. Inicie a associação do contêiner de proteção (nuvem do Virtual Machine Manager) com a política de replicação.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. Aguarde o conclusão do trabalho de associação da política. Para verificar se o trabalho foi concluído, use o seguinte snippet do PowerShell:

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. Após a conclusão do processamento do trabalho, execute o seguinte comando:

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

Para verificar a conclusão da operação, siga as etapas em [Monitorar a atividade](#monitor-activity).

##  <a name="configure-network-mapping"></a>Configurar o mapeamento de rede

1. Use este comando para recuperar os servidores para o cofre atual. O comando armazena os servidores de Site Recovery na `$Servers` variável de matriz.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. Execute este comando para recuperar as redes para o servidor de origem do Virtual Machine Manager e o servidor de destino do Virtual Machine Manager.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > O servidor de Virtual Machine Manager de origem pode ser o primeiro ou o segundo na matriz do servidor. Verifique os nomes de servidor de Virtual Machine Manager e recupere as redes adequadamente.

1. O cmdlet cria um mapeamento entre a rede principal e a rede de recuperação. Ele especifica a rede principal como o primeiro elemento de `$PrimaryNetworks` . Ele especifica a rede de recuperação como o primeiro elemento de `$RecoveryNetworks` .

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>Habilitar a proteção para VMs

Depois que os servidores, nuvens e redes estiverem configurados corretamente, habilite a proteção para VMs na nuvem.

1. Para habilitar a proteção, execute o seguinte comando para recuperar o contêiner de proteção:

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. Obtenha a entidade de proteção (VM) da seguinte maneira:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. Habilite a replicação para a VM.

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Se você quiser replicar em discos gerenciados habilitados para o CMK no Azure, execute as seguintes etapas usando AZ PowerShell 3.3.0 em diante:
>
> 1. Habilitar o failover para discos gerenciados atualizando as propriedades da VM
> 1. Use o `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet para buscar a ID do disco de cada disco do item protegido
> 1. Crie um objeto Dictionary usando `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` o cmdlet para conter o mapeamento da ID do disco para o conjunto de criptografia de disco. Esses conjuntos de criptografia de disco devem ser criados previamente por você na região de destino.
> 1. Atualize as propriedades da VM usando `Set-AzRecoveryServicesAsrReplicationProtectedItem` o cmdlet, passando o objeto Dictionary no parâmetro **DiskIdToDiskEncryptionSetMap** .

## <a name="run-a-test-failover"></a>Execute um teste de failover

Para testar sua implantação, execute um failover de teste para uma única máquina virtual. Você também pode criar um plano de recuperação que contém várias VMs e executar um failover de teste para o plano. O failover de teste simula o mecanismo de failover e recuperação em uma rede isolada.

1. Recupere a VM na qual as VMs irão falhar.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. Execute um failover de teste.

   Para uma única VM:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   Para um plano de recuperação:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

Para verificar a conclusão da operação, siga as etapas em [Monitorar a atividade](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Execute failovers planejados e não planejados

1. Execute um failover planejado.

   Para uma única VM:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Para um plano de recuperação:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. Realizar um failover não planejado.

   Para uma única VM:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Para um plano de recuperação:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>Monitorar a atividade

Use os comandos a seguir para monitorar a atividade de failover. Aguarde o processamento para concluir entre trabalhos.

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
    Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
    if($job -eq $null -or $job.StateDescription -ne "Completed")
    {
        $isJobLeftForProcessing = $true;
    }

if($isJobLeftForProcessing)
    {
        Start-Sleep -Seconds 60
    }
}While($isJobLeftForProcessing)
```

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](/powershell/module/az.recoveryservices) sobre o Site Recovery com cmdlets do PowerShell do Resource Manager.
