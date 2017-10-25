---
title: "Solucionar problemas de falha de Backup do Azure: indisponível no status de agente convidado | Microsoft Docs"
description: "Sintomas, causas e resoluções para falhas de Backup do Azure relacionados ao agente, extensão e discos"
services: backup
documentationcenter: 
author: genlin
manager: cshepard
editor: 
keywords: Backup do Azure; agente da VM; conectividade de rede;
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: genli;markgal;
ms.openlocfilehash: f3195fa83479986a3e605abce618c78bcdb64dac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-backup-failure-issues-with-agent-andor-extension"></a>Solucionar problemas de falha de Backup do Azure: problemas com o agente e/ou extensão

Este artigo fornece etapas de solução de problemas para ajudar você a resolver falhas de backup relacionadas a problemas de comunicação com o agente da VM e a extensão.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>Agente de VM não consegue se comunicar com o Backup do Azure
Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com o agente de backup de VM para obter um instantâneo pontual. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado, o que, por sua vez, pode levar a falhas de backup. Siga as etapas de solução de problemas abaixo na ordem em que são fornecidas e repita a operação.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Causa 1: [A VM tem sem acesso à Internet](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Causa 2: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 3: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Causa 4: [O status do instantâneo não pode ser recuperado ou não é possível obter um instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Causa 5: [A extensão de backup falha ao ser atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Houve falha na operação de instantâneo por falta de conectividade à rede na máquina virtual
Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado, o que, por sua vez, pode levar a falhas de backup. Siga as etapas de solução de problemas abaixo na ordem em que são fornecidas e repita a operação.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Causa 1: [A VM tem sem acesso à Internet](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Causa 2: [Não é possível recuperar o status de instantâneos ou não é possível obter os instantâneos](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-3-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Causa 3: [A extensão de backup falha ao ser atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)

## <a name="vmsnapshot-extension-operation-failed"></a>Falha na operação da extensão de VMSnapshot

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado, o que, por sua vez, pode levar a falhas de backup. Siga as etapas de solução de problemas abaixo na ordem em que são fornecidas e repita a operação.
##### <a name="cause-1-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Causa 1: [Não é possível recuperar o status de instantâneos ou não é possível obter os instantâneos](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-2-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Causa 2: [A extensão de backup falha ao ser atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Causa 3: [A VM não tem acesso à Internet](#the-vm-has-no-internet-access)
##### <a name="cause-4-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Causa 4: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-5-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 5: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)

## <a name="unable-to-perform-the-operation-as-the-vm-agent-is-not-responsive"></a>Não é possível executar a operação porque o Agente de VM não está respondendo

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado, o que, por sua vez, pode levar a falhas de backup. Siga as etapas de solução de problemas abaixo na ordem em que são fornecidas e repita a operação.
##### <a name="cause-1-the-agent-is-installed-in-the-vm-but-is-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Causa 1: [o agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-2-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 2: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-3-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Causa 3: [A VM não tem acesso à Internet](#the-vm-has-no-internet-access)

## <a name="backup-failed-with-an-internal-error---please-retry-the-operation-in-a-few-minutes"></a>Falha no backup com um erro interno – tente novamente a operação dentro de alguns minutos

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado, o que, por sua vez, pode levar a falhas de backup. Siga as etapas de solução de problemas abaixo na ordem em que são fornecidas e repita a operação.
##### <a name="cause-1-the-vm-has-no-internet-accessthe-vm-has-no-internet-access"></a>Causa 1: [A VM tem sem acesso à Internet](#the-vm-has-no-internet-access)
##### <a name="cause-2-the-agent-installed-in-the-vm-but-unresponsive-for-windows-vmsthe-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Causa 2: [O agente está instalado na VM, mas sem resposta (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
##### <a name="cause-3-the-agent-installed-in-the-vm-is-out-of-date-for-linux-vmsthe-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 3: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
##### <a name="cause-4-the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-takenthe-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Causa 4: [O status do instantâneo não pode ser recuperado ou não é possível obter um instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)
##### <a name="cause-5-the-backup-extension-fails-to-update-or-loadthe-backup-extension-fails-to-update-or-load"></a>Causa 5: [A extensão de backup falha ao ser atualizada ou carregada](#the-backup-extension-fails-to-update-or-load)

## <a name="the-specified-disk-configuration-is-not-supported"></a>Não há suporte para a Configuração de disco especificada

Atualmente, o Backup do Azure não dá suporte a tamanhos de disco [maiores que 1.023 GB](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm). 
- Se você tiver discos maiores que 1 TB, [conecte novos discos](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) que sejam inferiores a 1 TB <br>
- Em seguida, copie os dados do disco maior que 1 TB para o(s) disco(s) recém-criado(s) de tamanho menor que 1 TB. <br>
- Certifique-se de que todos os dados foram copiados e remova os discos maiores que 1 TB
- Inicie o backup

## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-vm-has-no-internet-access"></a>A VM não tem acesso à Internet
Pelo requisito de implantação, a VM não tem acesso à Internet ou tem restrições em vigor que impedem o acesso à infraestrutura do Azure.

Para funcionar corretamente, a extensão de backup exige conectividade com os endereços IP públicos do Azure. A extensão envia comandos para um ponto de extremidade do Armazenamento do Azure (URL de HTTP) para gerenciar os instantâneos da VM. Se a extensão não tiver acesso à Internet pública, eventualmente falha de Backup.

####  <a name="solution"></a>Solução
Para resolver o problema, tente um dos seguintes métodos listados aqui.
##### <a name="allow-access-to-the-azure-datacenter-ip-ranges"></a>Permitir o acesso para os intervalos IP do datacenter do Azure

1. Obtenha a [lista de IPs do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) para os quais o acesso será permitido.
2. Desbloquear o IPs executando o **New-NetRoute** cmdlet na VM do Azure em uma janela elevada do PowerShell. Execute o cmdlet como um administrador.
3. Para permitir o acesso aos IPs, adicione regras ao grupo de segurança de rede, se você tiver um.

##### <a name="create-a-path-for-http-traffic-to-flow"></a>Criar um caminho para a transmissão do tráfego HTTP

1. Se você tiver alguma restrição de rede no local (um grupo de segurança de rede, por exemplo), implante um servidor proxy HTTP para rotear o tráfego.
2. Para permitir o acesso à Internet por meio do proxy HTTP, adicione regras ao grupo de segurança de rede, se você tiver uma.

Para saber como configurar um proxy HTTP para backups VM, veja [preparar seu ambiente para fazer backup de máquinas virtuais do Azure](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

Caso você esteja usando o Managed Disks, talvez seja necessário uma porta adicional (8443) aberta nos firewalls.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente está instalado na VM, mas sem resposta (para VMs do Windows)

#### <a name="solution"></a>Solução
O agente da VM pode ter sido corrompido ou o serviço deve ter sido interrompido. Reinstalar o agente da VM ajudaria a obter a versão mais recente e reiniciar a comunicação.

1. Verifique se o serviço de agente de convidado do Windows em execução nos serviços (services.msc) da máquina Virtual. Tente reiniciar o serviço de agente de convidado do Windows e iniciar o Backup<br>
2. Se não estiver visível em serviços, verifique em Programas e Recursos se o serviço de agente Convidado do Windows está instalado.
4. Se você for capaz de exibir em programas e recursos, desinstale o Agente convidado do Windows.
5. Baixe e instale a [versão mais recente do agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa de privilégios de Administrador para concluir a instalação.
6. Em seguida, você deve ser capaz de exibir os serviços do agente de convidado do Windows nos serviços
7. Tente executar um backup sob demanda/adhoc clicando em "Fazer Backup agora" no portal.

Verifique também se sua máquina Virtual possui o **[.NET 4.5 instalado no sistema](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)**. Ele é necessário para o agente da VM se comunicar com o serviço

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado na VM está desatualizado (para VMs Linux)

#### <a name="solution"></a>Solução
A maioria das falhas relacionadas ao agente ou relacionadas à extensão para VMs do Linux é causada por problemas que afetam um agente de VM desatualizado. Para solucionar esse problema, siga estas diretrizes gerais:

1. Siga as instruções para [atualizar o agente de VM do Linux ](../virtual-machines/linux/update-agent.md).

 >[!NOTE]
 >Estamos *altamente recomendável* que você atualizar o agente apenas por meio de um repositório de distribuição. Não é recomendável baixar o código do agente diretamente do GitHub e atualizá-lo. Se o agente mais recente está disponível para a sua distribuição, contate o suporte de distribuição para obter instruções sobre como instalá-lo. Para verificar o agente mais recente, vá para a página [agente Linux do Windows Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório do GitHub.

2. Certifique-se de que o agente do Azure está em execução na máquina virtual executando o seguinte comando:`ps -e`

 Se o processo não está em execução, reinicie-o usando os seguintes comandos:

 * Para o Ubuntu: `service walinuxagent start`
 * Para outras distribuições: `service waagent start`

3. [Configure o agente de reinicialização automática](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Execute um novo backup de teste. Se a falha persistir, reúna os seguintes logs da VM do cliente:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Se precisarmos do registro detalhado para waagent, siga estas etapas:

1. No arquivo /etc/waagent.conf, localize a seguinte linha: **habilitar o log detalhado (y | n)**
2. Altere o valor **Logs.Verbose** de *n* para *y*.
3. Salve a alteração e reinicie o waagent seguindo as etapas anteriores nesta seção.

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo
O backup da máquina virtual depende de emitir um comando de instantâneo para a conta de armazenamento subjacente. Backup pode falhar porque ele não tem acesso à conta de armazenamento ou atrasar a execução da tarefa de instantâneo.

#### <a name="solution"></a>Solução
As condições a seguir podem causar a falha da tarefa do instantâneo:

| Causa | Solução |
| --- | --- |
| A máquina virtual tem o backup do SQL Server configurado. | Por padrão, o backup de VM executa um backup completo VSS em VMs do Windows. Em VMs que executam servidores baseados no SQL Server e em que o backup do SQL Server está configurado, podem ocorrer atrasos na execução do instantâneo.<br><br>Se houver uma falha de Backup devido a um problema instantâneo, defina a seguinte chave do registro:<br><br>**[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE"** |
| O status da VM é informado incorretamente porque a VM está desligada em RDP. | Se você desligar a VM no protocolo de área de trabalho remota (RDP), verifique o portal para determinar se o status da VM está correto. Se não estiver correto, desligue a VM no portal usando a opção de **Desligar** no painel de VM. |
| Várias VMs do mesmo serviço de nuvem são configuradas para backup simultaneamente. | É uma prática recomendada distribuir as agendas de backup para VMs do mesmo serviço de nuvem. |
| A VM está em execução com alto uso de CPU ou memória. | Se a VM estiver em execução com alta utilização de CPU (mais de 90%) ou alto uso de memória, a tarefa do instantâneo será enfieirada e postergada e, eventualmente, atingirá o tempo limite. Nessa situação, tente um backup sob demanda. |
| A VM não pode obter o endereço do host/malha do DHCP. | O DHCP deve estar habilitado no convidado para que o backup da VM IaaS funcione.  Se a VM não puder obter o endereço do host/malha da resposta DHCP 245, ela não poderá baixar ou executar qualquer extensão. Se você precisar de um endereço IP privado estático, deverá configurá-lo usando a plataforma. A opção DHCP na VM deve ser ativada. Para saber mais, veja [Definição de um IP interno estático privado](../virtual-network/virtual-networks-reserved-private-ip.md). |

### <a name="the-backup-extension-fails-to-update-or-load"></a>A extensão de backup não pode ser atualizada ou carregada
Se as extensões não puderem ser carregadas, o Backup falhará porque não é possível obter um instantâneo.

#### <a name="solution"></a>Solução

**Para convidados do Windows:** verifique se o serviço iaasvmprovider está habilitado e tem um tipo de inicialização *automática*. Se o serviço não está configurado dessa forma, habilitá-la determinar se o próximo backup for bem-sucedido.

**Para convidados do Linux:** verifique a versão mais recente do VMSnapshot para Linux (a extensão usada pelo Backup) é a 1.0.91.0.<br>


Se a extensão de backup ainda não conseguir ser atualizada ou carregada, você poderá forçar a extensão VMSnapshot ser recarregada desinstalando a extensão. A próxima tentativa de backup recarregará a extensão.

Para desinstalar a extensão, faça o seguinte:

1. Vá para o [Portal do Azure](https://portal.azure.com/).
2. Localize a VM com problemas de backup.
3. Clique em **Configurações**.
4. Clique em **Extensões**.
5. Clique em **Extensão Vmsnapshot**.
6. Clique em **Desinstalar**.

Este procedimento faz com que a extensão seja reinstalada durante o próximo backup.

