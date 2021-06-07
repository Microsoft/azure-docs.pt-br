---
title: Preparar a recuperação de desastre da VM do VMware com o Azure Site Recovery
description: Saiba como preparar os servidores VMware locais para recuperação de desastre para o Azure usando o serviço Azure Site Recovery.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.custom: MVC
ms.openlocfilehash: d3ba53908ae9ceccb651e1d9bf9b685d36d4c56c
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579938"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Preparar servidores VMware locais para recuperação de desastre para o Azure

Este artigo descreve como preparar os servidores VMware locais para recuperação de desastre para o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md). 

Este é o segundo tutorial em uma série que mostra como configurar a recuperação de desastre para o Azure para VMs VMware locais. No primeiro tutorial, [configuramos os componentes do Azure](tutorial-prepare-azure.md) necessários para a recuperação de desastres do VMware.


Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Prepare uma conta no servidor vCenter ou no host vSphere ESXi para automatizar a descoberta de VMs.
> * Preparar uma conta para a instalação automática do serviço de Mobilidade nas VMs VMware.
> * Examinar os requisitos e o suporte para VM e servidor do VMware.
> * Preparar para conectar VMs do Azure após o failover.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Eles usam opções padrão quando possível e não mostram todas as possíveis configurações e caminhos. Para obter instruções detalhadas, leia o artigo na seção Instruções do Sumário do Site Recovery.

## <a name="before-you-start"></a>Antes de começar

Verifique se você preparou o Azure conforme descrito no [primeiro tutorial desta série](tutorial-prepare-azure.md).

## <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática

O Site Recovery precisa de acesso aos servidores VMware para:

- Descobrir VMs automaticamente. Pelo menos uma conta somente leitura é necessária.
- Orquestrar a replicação, o failover e o failback. Você precisa de uma conta que possa executar operações como criar e remover discos e ativar VMs.

Crie a conta da seguinte maneira:

1. Para usar uma conta dedicada, crie uma função no nível do vCenter. Dê um nome à função como **Azure_Site_Recovery**.
2. Atribua à função as permissões resumidas na tabela a seguir.
3. Crie um usuário no vCenter Server ou no host do vSphere. Atribua a função ao usuário.

### <a name="vmware-account-permissions"></a>Permissões de conta do VMware

**Tarefa** | **Função/Permissões** | **Detalhes**
--- | --- | ---
**Descoberta VM** | Pelo menos um usuário somente leitura<br/><br/> Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).
**Replicação, failover, failback totais** |  Crie uma função (Azure_Site_Recovery) com as permissões necessárias e, em seguida, atribua a função a um usuário ou grupo do VMware<br/><br/> Objeto de Data Center –> Propagar para o Objeto Filho, função = Azure_Site_Recovery<br/><br/> Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual<br/><br/> Rede -> Atribuição de rede<br/><br/> Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder à pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registrar, cancelar registro<br/><br/> Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de Mobilidade deve ser instalado em todas as VMs que você deseja replicar. O Site Recovery pode fazer uma instalação por push do serviço quando você habilitar a replicação para uma máquina, ou você pode instalá-lo manualmente, ou usando ferramentas de instalação.

- Neste tutorial, vamos instalar o serviço de Mobilidade com a instalação por push.
- Para esta instalação por push, você precisará preparar uma conta que o Site Recovery possa usar para acessar a VM. Você deve especificar esta conta quando configurar a recuperação de desastre no console do Azure.

Prepare a conta da seguinte maneira:

Prepare um domínio ou uma conta local com permissões para instalar na VM.

- **VMs do Windows**: Para instalar em VMs do Windows, se você não estiver usando uma conta de domínio, desabilite o controle de Acesso de Usuários Remoto no computador local. Para fazer isso, no registro > **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy** com um valor de 1.
- **VMs do Linux**: Para instalar em VMs do Linux, prepare uma conta raiz no servidor Linux de origem.


## <a name="check-vmware-requirements"></a>Verificar requisitos de VMware

Certifique-se de que os servidores e VMs de VMware estejam em conformidade com os requisitos.

1. [Verifique](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers) os requisitos do servidor VMware.
2. Para VMs do Linux, [verifique](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) os requisitos de armazenamento e sistema de arquivos. 
3. Verifique o suporte a [rede](vmware-physical-azure-support-matrix.md#network) local e [armazenamento](vmware-physical-azure-support-matrix.md#storage). 
4. Verifique o que há de suporte para [rede do Azure](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [armazenamento](vmware-physical-azure-support-matrix.md#azure-storage) e [computação](vmware-physical-azure-support-matrix.md#azure-compute) após o failover.
5. Suas VMs locais replicadas no Azure devem atender aos [requisitos de VM do Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
6. Em Máquinas Virtuais do Linux, o nome do dispositivo ou o nome do ponto de montagem deve ser exclusivo. Assegure que não existam dois pontos de montagem/dispositivos com os mesmos nomes. Observe que os nomes não diferenciam maiúsculas de minúsculas. Por exemplo, nomear dois dispositivos para a mesma VM como _device1_ e _Device1_ não é permitido.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Após o failover, conecte-se às VMs do Azure a partir de sua rede local.

Para se conectar a VMs do Windows usando o RDP após o failover, faça o seguinte:

- **Acesso à Internet**. Antes do failover, habilite o RDP na VM local. Certifique-se de que as regras TCP e UDP são adicionadas ao perfil **Público** e que o RDP é permitido no **Firewall do Windows** > **Aplicativos Permitidos** para todos os perfis.
- **Acesso VPN site a site**:
    - Antes do failover, habilite o RDP no computador local.
    - O RDP deve ser permitido no **Firewall do Windows** -> **Aplicativos e recursos permitidos** para redes de **Domínio e Privadas**.
    - Verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).
- Não deve haver nenhuma atualização pendente do Windows na VM quando você dispara um failover. Se houver, não será possível entrar na máquina virtual até que a atualização seja concluída.
- Na VM do Microsoft Azure após o failover, verifique o **Diagnóstico de inicialização** para exibir uma captura de tela da VM. Se você não puder se conectar, verifique se a VM está em execução e examine estas [dicas de solução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Para conectar-se a VMs do Linux usando SSH após o failover, faça o seguinte:

- No computador local antes do failover, verifique se o serviço Secure Shell está definido para iniciar automaticamente na inicialização do sistema.
- Verifique se as regras de firewall permitem uma conexão SSH.
- Na VM do Azure após o failover, permita as conexões de entrada com a porta SSH para as regras do grupo de segurança de rede na VM com failover e para a sub-rede do Azure à qual ela está conectada.
- [Adicione um endereço IP público](./site-recovery-monitor-and-troubleshoot.md) para a VM.
- Você pode verificar o **Diagnóstico de inicialização** para exibir uma captura de tela da VM.


## <a name="failback-requirements"></a>Requisitos de failback
Se você planeja realizar o failback para seu site local, há alguns [pré-requisitos para failback](vmware-azure-reprotect.md#before-you-begin). Você pode prepará-los agora, mas você não precisa fazer isso. Você pode preparar após o failover para o Azure.



## <a name="next-steps"></a>Próximas etapas

Configurar a recuperação de desastre. Se você estiver replicando várias VMs, planeje a capacidade.
> [!div class="nextstepaction"]
> [Configurar a recuperação de desastres para o Azure para VMs VMware](vmware-azure-tutorial.md)
> [Realizar planejamento de capacidade](site-recovery-deployment-planner.md).
