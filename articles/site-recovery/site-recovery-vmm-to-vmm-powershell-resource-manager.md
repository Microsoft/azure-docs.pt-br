---
title: "Replicar VMs do Hyper-V no VMM para um site secundário com o PowerShell (Azure Resource Manager) | Microsoft Docs"
description: "Descreve como implantar o Azure Site Recovery para orquestrar a replicação, o failover e a recuperação de VMs do Hyper-V em nuvens do VMM para um local de VMM secundário usando o PowerShell (Resource Manager)"
services: site-recovery
documentationcenter: 
author: sujaytalasila
manager: rochakm
editor: raynew
ms.assetid: 9d38e9c3-217c-4e44-830c-575e9a4141f2
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: sutalasi
ms.openlocfilehash: 5a6e00877b0a2b139d5322f610c1901ad76a710f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>Replicar máquinas virtuais do Hyper-V em nuvens de VMM para um site de VMM secundário usando PowerShell (Resource Manager)
> [!div class="op_single_selector"]
> * [Portal do Azure](site-recovery-vmm-to-vmm.md)
> * [Portal clássico](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

Bem-vindo ao Azure Site Recovery! Use este artigo se desejar replicar máquinas virtuais Hyper-V locais gerenciadas em nuvens do System Center VMM (Virtual Machine Manager) para um site secundário.

Este artigo mostra como usar o PowerShell para automatizar tarefas comuns que você precisa executar quando você configura o Azure Site Recovery para replicar máquinas virtuais Hyper-V em nuvens do System Center VMM para nuvens do VMM do System Center no site secundário.

O artigo inclui pré-requisitos para o cenário e mostra a você como:

* Configurar um cofre dos Serviços de Recuperação
* Instalar o Provedor do Azure Site Recovery no servidor VMM de origem e de destino
* Registrar os servidores do VMM no cofre
* Configurar a política de replicação para a nuvem do VMM. As configurações de replicação na política serão aplicadas a todas as máquinas virtuais protegidas
* Habilitar a proteção para as máquina virtuais.
* Testar o failover de VMs, individualmente ou como parte de um plano de recuperação para verificar se tudo está funcionando conforme o esperado.
* Executar um failover planejado ou não planejado de VMs, individualmente ou como parte de um plano de recuperação para verificar se tudo está funcionando conforme o esperado.

Se você enfrentar problemas ao configurar esse cenário, publique suas perguntas no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [!NOTE]
> O Azure tem dois [modelos de implantação](../azure-resource-manager/resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos: Azure Resource Manager e Clássico. O Azure também tem dois portais – o portal clássico do Azure, que dá suporte ao modelo de implantação clássica, e o portal do Azure, com suporte para ambos os modelos de implantação. Este artigo aborda o modelo de implantação do Gerenciador de Recursos.
>
>

## <a name="on-premises-prerequisites"></a>Pré-requisitos do local
Aqui está o que será necessário nos sites primário e secundário locais para implantar este cenário:

| **Pré-requisitos** | **Detalhes** |
| --- | --- |
| **VMM** |Recomendamos a implantação com um servidor VMM no site primário e de um servidor VMM no site secundário.<br/><br/> Você também pode [replicar entre nuvens em um único servidor VMM](site-recovery-vmm-to-vmm.md#prepare-for-single-server-deployment). Para fazer isso, você precisará de pelo menos duas nuvens configuradas no servidor VMM.<br/><br/> Os servidores VMM devem estar executando pelo menos o System Center 2012 SP1 com as atualizações mais recentes.<br/><br/> Cada servidor VMM deve ter uma ou mais nuvens configuradas, e todas as nuvens devem ter o perfil de Capacidade do Hyper-V definido. <br/><br/>As nuvens devem conter um ou mais grupos de hosts do VMM.<br/><br/>Saiba mais sobre como configurar nuvens VMM em [Configurando a malha de nuvem VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) e [Passo a passo: criando nuvens privadas com o VMM do System Center 2012 SP1](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Os servidores VMM devem ter acesso à internet. |
| **Hyper-V** |Os servidores Hyper-V devem estar executando pelo menos o Windows Server 2012 com a função Hyper-V e ter as últimas atualizações instaladas.<br/><br/> Um servidor Hyper-V deve conter uma ou mais VMs.<br/><br/>  Os servidores host Hyper-V devem estar localizados em grupos de hosts nas nuvens VMM primárias e secundárias.<br/><br/> Se você estiver executando o Hyper-V em um cluster no Windows Server 2012 R2, instale a [atualização 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Se estiver executando o Hyper-V em um cluster no Windows Server 2012, observe que o agente de cluster não será criado automaticamente se você tiver um cluster de baseados em endereço IP estático. Você precisará configurar o agente de cluster manualmente. [Leia mais](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Provedor** |Durante a implantação de Recuperação de Site, você instala o Provedor do Azure Site Recovery em servidores VMM. O Provedor se comunica com a Recuperação de Site por HTTPS 443 para orquestrar a replicação. A replicação de dados ocorre entre os servidores Hyper-V primário e secundário sobre a LAN ou uma conexão VPN.<br/><br/> O Provedor em execução no servidor VMM precisa de acesso a estas URLs: *.hypervrecoverymanager.windowsazure.com; *.accesscontrol.windows.net; *.backup.windowsazure.com; *.blob.core.windows.net; *.store.core.windows.net.<br/><br/> Além disso, permita a comunicação de firewall de servidores VMM para os [Intervalos IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e permita o protocolo HTTPS (443). |

### <a name="network-mapping-prerequisites"></a>Pré-requisitos de mapeamento de rede
Mapeamento de rede mapeiam entre redes de VM do VMM nos servidores primário e secundário do VMM para:

* Colocar de maneira ideal VMs de réplica em hosts secundários do Hyper-V após o failover.
* Conecte VMs de réplica às redes de VM apropriadas.
* Se você não configurar o mapeamento de rede, as VMs de réplica não serão conectadas a nenhuma rede após o failover.
* Se você quiser configurar o mapeamento de rede durante a implantação de Recuperação de Site, você precisará disto:

  * Verifique se as VMs do servidor host de origem do Hyper-V estão conectadas a uma rede de VMs do VMM. Essa rede deve ser vinculada a uma rede lógica que esteja associada à nuvem.
  * Verifique se a nuvem secundária que você usará para recuperação tem uma rede VM correspondente configurada. Essa rede de VM deverá ser vinculada a uma rede lógica associada à nuvem secundária.

Saiba mais sobre como configurar redes VMM nos artigos abaixo

* [Como configurar redes lógicas no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [Como configurar redes e gateways de VMs no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)

[Saiba mais](site-recovery-vmm-to-vmm.md#prepare-for-network-mapping) sobre o funcionamento do mapeamento de rede.

### <a name="powershell-prerequisites"></a>Pré-requisitos do PowerShell
Verifique se você tem o PowerShell do Azure pronto para uso. Se você já estiver usando o PowerShell, precisará atualizar para a versão 0.8.10 ou posterior. Para obter mais informações sobre como configurar o PowerShell, confira o [Guia de instalação e de configuração do Azure PowerShell](/powershell/azureps-cmdlets-docs). Depois de instalar e configurar o PowerShell, você poderá exibir todos os cmdlets disponíveis para o serviço [aqui](/powershell/azure/overview).

Para obter dicas que podem ajudar você a usar os cmdlets, como valores de parâmetro, entradas e saídas, que normalmente são tratados no Azure PowerShell, confira [Introdução aos cmdlets do Azure](/powershell/azure/get-started-azureps).

## <a name="step-1-set-the-subscription"></a>Etapa 1: definir a assinatura
1. No Azure PowerShell, faça logon na conta do Azure usando os seguintes cmdlets:

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Obtenha uma lista das suas assinaturas. Essa lista também incluirá as IDs de assinatura de cada uma das assinaturas. Anote a ID da assinatura na qual você quer criar o cofre dos serviços de recuperação    

        Get-AzureRmSubscription
3. Defina a assinatura na qual o cofre dos serviços de recuperação deverá ser criado mencionando a ID da assinatura

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="step-2-create-a-recovery-services-vault"></a>Etapa 2: Criar um cofre dos Serviços de Recuperação
1. Se você ainda não tiver um, crie um grupo de recursos do Azure Resource Manager

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Crie um novo cofre de Serviços de Recuperação e salve o objeto de cofre ASR criado em uma variável (será usado posteriormente). Você também pode recuperar o objeto de cofre ASR pós-criação usando o cmdlet Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Etapa 3: Configurar o contexto do Cofre dos Serviços de Recuperação
1. Se já houver um cofre criado, execute o comando abaixo para obter o cofre.

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. Defina o contexto de cofre, executando o comando abaixo.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Etapa 4: instalar o Provedor do Azure Site Recovery
1. Na máquina da VMM, crie um diretório executando o seguinte comando:

       New-Item c:\ASR -type directory
2. Extraia os arquivos usando o provedor baixado, executando o seguinte comando

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Instale o provedor usando os comandos a seguir:

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

   Aguarde a conclusão da instalação.
4. Registre o servidor no cofre usando o seguinte comando:

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="step-5-create-and-associate-a-replication-policy"></a>Etapa 5: Criar e associar uma política de replicação
1. Crie uma política de replicação do Hyper-V 2012 R2 executando o comando a seguir:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > A nuvem do VMM pode conter hosts Hyper-V executando versões diferentes do Windows Server (conforme mencionado nos pré-requisitos do Hyper-V), mas a política de replicação é específica à versão do sistema operacional. Se você tiver hosts diferentes em execução em versões diferentes do sistema operacional, crie políticas de replicação separadas para cada tipo de versão do sistema operacional. Por exemplo: se você tiver cinco hosts em execução no Windows Server 2012 e três no Windows Server 2012 R2, crie duas políticas de replicação – uma para cada tipo de versão do sistema operacional.

1. Obtenha o contêiner de proteção principal (nuvem VMM principal) e o contêiner de proteção de recuperação (nuvem do VMM de recuperação) executando os comandos a seguir:

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. Recuperar a política que você criou na etapa 1 usando o nome amigável da política

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Inicie a associação do contêiner de proteção (Nuvem do VMM) com a política de replicação:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. Aguarde o conclusão do trabalho de associação da política. Você pode verificar se o trabalho foi concluído usando o seguinte trecho do PowerShell.

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

   Após a conclusão do processamento do trabalho, execute o seguinte comando:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Para verificar a conclusão da operação, execute as etapas em [Monitorar a Atividade](#monitor).

## <a name="step-6-configure-network-mapping"></a>Etapa 6: Configurar o mapeamento de rede
1. O primeiro comando obtém servidores para o cofre atual do Azure Site Recovery. O comando armazena os servidores do Microsoft Azure Site Recovery na variável de matriz $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Os comandos a seguir obtêm a rede de recuperação de site para o servidor VMM de origem e o servidor VMM de destino.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > O servidor VMM de origem pode ser o primeiro ou o segundo na matriz de servidores. Verifique os nomes dos servidores VMM e obtenha as redes adequadamente


1. O cmdlet final cria um mapeamento entre a rede principal e a rede de recuperação. O cmdlet especifica a rede principal como o primeiro elemento da $PrimaryNetworks e a rede de recuperação como o primeiro elemento da $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-7-configure-storage-mapping"></a>Etapa 7: Configurar mapeamento de armazenamento
1. O comando abaixo coloca a lista de classificações de armazenamento na variável $storageclassifications.

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification
2. Os comandos a seguir colocam a classificação de origem na variável $SourceClassificaion e a classificação de destino na variável $TargetClassification.

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    > [!NOTE]
    > As classificações de origem e de destino podem ser qualquer elemento na matriz. Consulte a saída do comando abaixo para identificar o índice das classificações de origem e destino na matriz $storageclassifications.

    > Get-AzureRmSiteRecoveryStorageClassification | Select-Object -Property FriendlyName, Id | Format-Table


1. O cmdlet a seguir cria um mapeamento entre a classificação de origem e a classificação de destino.

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-8-enable-protection-for-virtual-machines"></a>Etapa 8: habilitar a proteção para máquinas virtuais
Depois que os servidores, nuvens e redes estiverem configurados corretamente, você poderá habilitar a proteção para máquinas virtuais na nuvem.

1. Para habilitar a proteção, execute o seguinte comando para obter o contêiner de proteção:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Obtenha a entidade de proteção (VM) executando o seguinte comando:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Habilite a replicação para a VM executando o seguinte comando:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="test-your-deployment"></a>Testar a implantação
Para testar sua implantação, você pode executar um failover de teste para uma única máquina virtual, ou criar um plano de recuperação consistente de várias máquinas virtuais e executar um failover de teste para o plano. O failover de teste simula o mecanismo de failover e recuperação em uma rede isolada.

> [!NOTE]
> Você pode criar um plano de recuperação para seu aplicativo no Portal do Azure.
>
>

Para verificar a conclusão da operação, execute as etapas em [Monitorar a Atividade](#monitor).

### <a name="run-a-test-failover"></a>Execute um teste de failover
1. Execute os cmdlets abaixo para obter a rede de VM na qual você deseja testar o failover de suas VMs.

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. Execute um teste de failover de uma VM fazendo o seguinte:

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
3. Execute um teste de failover de um plano de recuperação fazendo o seguinte:

       $recoveryplanname = "test-recovery-plan"

       $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

### <a name="run-a-planned-failover"></a>Executar um failover planejado
1. Execute um failover planejado de uma VM fazendo o seguinte:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
2. Execute um failover planejado de um plano de recuperação fazendo o seguinte:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>Executar um failover não planejado
1. Execute um failover não planejado de uma VM fazendo o seguinte:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. Execute um failover não planejado de um plano de recuperação fazendo o seguinte:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name=monitor></a> Monitorar a atividade
Use os seguintes comandos para monitorar a atividade. Observe que é necessário aguardar a conclusão do processamento entre os trabalhos.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
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



## <a name="next-steps"></a>Próximas etapas
[Leia mais](/powershell/module/azurerm.recoveryservices.backup/#recovery) sobre o Azure Site Recovery com cmdlets do PowerShell do Azure Resource Manager.
