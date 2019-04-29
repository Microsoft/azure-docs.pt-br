---
title: Remover os servidores e desabilitar a proteção | Microsoft Docs
description: Este artigo descreve como cancelar o registro de servidores de um cofre de Recuperação de Site e desabilitar a proteção para máquinas virtuais e servidores físicos.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: c22acb1ae82e5c1e781598e8545c7f1625cc1c09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61277154"
---
# <a name="remove-servers-and-disable-protection"></a>Remover os servidores e desabilitar a proteção

Este artigo descreve como cancelar o registro de servidores de um cofre dos Serviços de Recuperação, e como desabilitar a proteção de máquinas protegidas pelo Site Recovery.


## <a name="unregister-a--configuration-server"></a>Cancelar o registro de um servidor de configuração

Se você replicar VMs VMware ou servidores físicos com Windows/Linux no Azure, será possível cancelar o registro de um servidor de configuração não conectado em um cofre da seguinte maneira:

1. [Desabilitar a proteção de máquinas virtuais](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Desassociar ou excluir](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) uma política de replicação.
3. [Excluir o servidor de configuração](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Cancelar o registro de um servidor VMM

1. Pare de replicar máquinas virtuais em nuvens no servidor VMM que você quer remover.
2. Exclua qualquer mapeamento de rede usado por nuvens no servidor VMM que você quer excluir. Em **Infraestrutura do Site Recovery** > **Para VMM do System Center** > **Mapeamento de Rede**, clique com o botão direito no mapeamento de rede > **Excluir**.
3. Anote a ID do servidor VMM.
4. Desassocie políticas de replicação de nuvens no servidor VMM que você quer remover.  Em **Infraestrutura do Site Recovery** > **Para VMM do System Center** >  **Políticas de Replicação**, clique duas vezes na política associada. Clique com o botão direito na nuvem > **Desassociar**.
5. Exclua o servidor VMM ou o nó ativo. Em **Infraestrutura do Site Recovery** > **Para VMM do System Center** > **Servidores VMM**, clique com o botão direito do servidor > **Excluir**.
6. Se o servidor do VMM estiver em um estado Desconectado, baixe e execute o [script de limpeza](https://aka.ms/asr-cleanup-script-vmm) no servidor do VMM. Abra o PowerShell com a opção **Executar como Administrador** para alterar a política de execução para o escopo padrão (LocalMachine). No script, especifique a ID do servidor VMM que você quer remover. O script remove o registro e as informações de emparelhamento na nuvem do servidor.
5. Execute o script de limpeza no servidor VMM secundário.
6. Execute o script de limpeza em quaisquer outros nós de cluster passivos do VMM nos quais o Provedor está instalado.
7. Desinstalar manualmente o Provedor no servidor VMM. Se você tiver um cluster, remova todos os nós.
8. Se suas máquinas virtuais estiverem replicando no Azure, será necessário desinstalar o agente dos Serviços de Recuperação da Microsoft dos hosts Hyper-V nas nuvens excluídas.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Cancelar o registro de um host Hyper-V em um site do Hyper-V

Os hosts Hyper-V que não são gerenciados pelo VMM são reunidos em um site do Hyper-V. Remova um host de um site do Hyper-V da seguinte maneira:

1. Desabilite a replicação para VMs do Hyper-V localizadas no host.
2. Desassocie as políticas para o site do Hyper-V. Em **Infraestrutura do Site Recovery** > **Para sites do Hyper-V** >  **Políticas de Replicação**, clique duas vezes na política associada. Clique com o botão direito no site > **Desassociar**.
3. Exclua os hosts Hyper-V. Em **Infraestrutura do Site Recovery** > **Para Sites do Hyper-V** > **Hosts Hyper-V**, clique com o botão direito no servidor > **Excluir**.
4. Exclua site do Hyper-V depois que todos os hosts tiverem sido removidos dele. Em **Infraestrutura do Site Recovery** > **Para Sites do Hyper-V** > **Sites do Hyper-V**, clique com o botão direito no site > **Excluir**.
5. Se o seu host Hyper-V estava em um estado **Desconectado**, execute o seguinte script em cada host Hyper-V que você removeu. O script limpa as configurações no servidor e cancela o registro do cofre.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Desabilitar a proteção para uma VM do VMware ou servidor físico (VMware para Azure)

1. Em **Itens Protegidos** > **Itens Replicados**, clique com o botão direito na máquina > **Desabilitar replicação**.
2. Na página **Desabilitar replicação**, selecione uma destas opções:
    - **Desabilitar a replicação e remover (recomendado)** - Esta opção remove o item replicado do Azure Site Recovery e a replicação da máquina é interrompida. A configuração de replicação no Servidor de Configuração é limpa, e a cobrança do Site Recovery para este servidor protegido é interrompida. Observe que essa opção só pode ser usada quando o servidor de configuração está no estado conectado.
    - **Remover** - Essa opção deve ser usada somente se o ambiente de origem for excluído ou não está acessível (não conectado). Isso remove o item replicado do Azure Site Recovery (a cobrança é interrompida). A configuração de replicação no Servidor de Configuração **não será** limpo. 

> [!NOTE]
> Nas duas opções, o serviço de mobilidade não será desinstalado dos servidores protegidos, é necessário desinstalá-lo manualmente. Se você planeja proteger o servidor novamente usando o mesmo Servidor de configuração, ignore a desinstalação do serviço de mobilidade.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Desabilite a proteção para uma máquina virtual do Hyper-V (Hyper-V para Azure)

> [!NOTE]
> Use este procedimento se você estiver replicando VMs do Hyper-V para o Azure sem um servidor do VMM. Se estiver replicando suas máquinas virtuais usando o cenário **System Center VMM para Azure**, siga as instruções do cenário Desabilitar a proteção para uma replicação de máquina virtual Hyper-V usando o System Center VMM para Azure

1. Em **Itens Protegidos** > **Itens Replicados**, clique com o botão direito na máquina > **Desabilitar replicação**.
2. Em **Desabilitar replicação**, você pode selecionar as seguintes opções:
   - **Desabilitar a replicação e remover (recomendado)** - Esta opção remove o item replicado do Azure Site Recovery e a replicação da máquina é interrompida. A configuração de replicação na máquina virtual local será limpa, e a cobrança do Site Recovery para este servidor protegido é interrompida.
   - **Remover** - Essa opção deve ser usada somente se o ambiente de origem for excluído ou não está acessível (não conectado). Isso remove o item replicado do Azure Site Recovery (a cobrança é interrompida). A configuração de replicação na máquina virtual local **não será** limpa. 

     > [!NOTE]
     > Se você escolheu a opção **Remover**, execute o seguinte conjunto de scripts para limpar as configurações de replicação do servidor do Hyper-V local.
1. No servidor de host do Hyper-V de origem, para remover a replicação para a máquina virtual. Substituir SQLVM1 pelo nome de sua máquina virtual e executar o script de um PowerShell administrativo

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Desabilitar a proteção para uma máquina virtual Hyper-V que replica no Azure usando o cenário VMM do System Center para o Azure

1. Em **Itens Protegidos** > **Itens Replicados**, clique com o botão direito na máquina > **Desabilitar replicação**.
2. Em **Desabilitar replicação**, selecione uma destas opções:

   - **Desabilitar a replicação e remover (recomendado)** - Esta opção remove o item replicado do Azure Site Recovery e a replicação da máquina é interrompida. A configuração de replicação na máquina virtual local é limpa, e a cobrança do Site Recovery para este servidor protegido é interrompida.
   - **Remover** - Essa opção deve ser usada somente se o ambiente de origem for excluído ou não está acessível (não conectado). Isso remove o item replicado do Azure Site Recovery (a cobrança é interrompida). A configuração de replicação na máquina virtual local **não será** limpa. 

     > [!NOTE]
     > Se você escolheu a opção **Remover**, execute os seguintes scripts para limpar as configurações de replicação do servidor do VMM local.
3. Execute este script no servidor VMM de origem usando o PowerShell (é necessário ter privilégios de administrador) no console do VMM. Substitua o espaço reservado **SQLVM1** pelo nome da máquina virtual.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. As etapas acima limpam as configurações de replicação no servidor do VMM. Para interromper a replicação para a máquina virtual em execução no servidor do host Hyper-V, execute este script. Substitua SQLVM1 pelo nome de sua máquina virtual e host01.contoso.com pelo nome do servidor do host Hyper-V.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Desabilitar a proteção para uma máquina virtual Hyper-V que replica para um Servidor do VMM secundário usando o cenário VMM do System Center para VMM

1. Em **Itens Protegidos** > **Itens Replicados**, clique com o botão direito na máquina > **Desabilitar replicação**.
2. Em **Desabilitar replicação**, selecione uma destas opções:

   - **Desabilitar a replicação e remover (recomendado)** - Esta opção remove o item replicado do Azure Site Recovery e a replicação da máquina é interrompida. A configuração de replicação na máquina virtual local é limpa, e a cobrança do Site Recovery para este servidor protegido é interrompida.
   - **Remover** - Essa opção deve ser usada somente se o ambiente de origem for excluído ou não está acessível (não conectado). Isso remove o item replicado do Azure Site Recovery (a cobrança é interrompida). A configuração de replicação na máquina virtual local **não será** limpa. Execute o seguinte conjunto de scripts para limpar as configurações de replicação das máquinas virtuais de locais.
     > [!NOTE]
     > Se você escolheu a opção **Remover**, execute os seguintes scripts para limpar as configurações de replicação do servidor do VMM local.

3. Execute este script no servidor VMM de origem usando o PowerShell (é necessário ter privilégios de administrador) no console do VMM. Substitua o espaço reservado **SQLVM1** pelo nome da máquina virtual.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. No servidor VMM secundário, execute este script para limpar as configurações da máquina virtual secundária:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. No servidor do VMM secundário, atualize as máquinas virtuais no servidor do host Hyper-V para que a VM secundária seja novamente detectada no console do VMM.
6. As etapas acima limpam as configurações de replicação no servidor do VMM. Se você quiser interromper a replicação para a máquina virtual, execute o seguinte script nas VMs primárias e secundárias. Substitua SQLVM1 pelo nome da máquina virtual.

        Remove-VMReplication –VMName “SQLVM1”




