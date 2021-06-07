---
title: Gerenciar o agente de mobilidade para servidores VMware/físicos com Azure Site Recovery
description: Gerencie o agente do serviço de mobilidade para recuperação de desastre de VMs VMware e servidores físicos no Azure usando o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: d921bddf90c415cb244e2cc9ad98354392a537ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90530141"
---
# <a name="manage-the-mobility-agent"></a>Gerenciar o agente Mobilidade 

Você configura o agente de mobilidade no servidor quando usa Azure Site Recovery para recuperação de desastre de VMs VMware e servidores físicos para o Azure. O agente de mobilidade coordena as comunicações entre o computador protegido, o servidor de configuração/servidor de processo de expansão e gerencia a replicação de dados. Este artigo resume as tarefas comuns para gerenciar o agente de mobilidade após sua implantação.

>[!TIP]
>Para baixar o instalador para um sistema operacional/distribuição Linux específico, consulte as diretrizes [aqui](vmware-physical-mobility-service-overview.md#locate-installer-files). Para atualizar automaticamente do portal, não é necessário baixar o instalador. O [ASR busca automaticamente o instalador do servidor de configuração e atualiza o agente](#update-mobility-service-from-azure-portal).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Atualizar o serviço de mobilidade de portal do Azure

1. Antes de iniciar, certifique-se de que o servidor de configuração, os servidores de processo de expansão e quaisquer servidores de destino que não façam parte da sua implantação sejam atualizados antes de você atualizar o Serviço de Mobilidade nos computadores protegidos.
    1. Da versão 9,36 em diante, para SUSE Linux Enterprise Server 11 SP3, RHEL 5, CentOS 5, Debian 7 Verifique se o instalador mais recente está [disponível no servidor de configuração e no servidor de processo de expansão](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server).
1. No portal, abra o cofre > **Itens replicados**.
1. Se o servidor de configuração estiver na versão mais recente, você verá uma notificação que mostra "Nova atualização do agente de replicação de recuperação do site está disponível. Clique para instalar."

     ![Janela Itens replicados](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Clique na notificação e, em **Atualização do agente**, selecione os computadores nos quais você deseja atualizar o serviço de mobilidade. Em seguida, clique em **OK**.

     ![Lista de VMs de itens replicados](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. O trabalho Atualizar o Serviço de Mobilidade é iniciado para cada um dos computadores selecionados. O agente de mobilidade é atualizado para a versão do servidor de configuração. Por exemplo, se o servidor de configuração estiver na versão 9,33, o agente de mobilidade em uma VM protegida também será atualizado para a versão 9,33.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Atualizar o serviço de mobilidade por meio do script do PowerShell no Windows Server

Antes de iniciar, certifique-se de que o servidor de configuração, os servidores de processo de expansão e quaisquer servidores de destino que não façam parte da sua implantação sejam atualizados antes de você atualizar o Serviço de Mobilidade nos computadores protegidos.

Use o script a seguir para atualizar o serviço de mobilidade em um servidor por meio do cmdlet do PowerShell

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Atualizar o serviço de mobilidade manualmente em cada servidor protegido

1. Antes de iniciar, certifique-se de que o servidor de configuração, os servidores de processo de expansão e quaisquer servidores de destino que não façam parte da sua implantação sejam atualizados antes de você atualizar o Serviço de Mobilidade nos computadores protegidos.

2. [Localize o instalador do agente](vmware-physical-mobility-service-overview.md#locate-installer-files) com base no sistema operacional do servidor.

>[!IMPORTANT]
> Se você estiver replicando a VM IaaS do Azure de uma região do Azure para outra, não use esse método. Consulte [nossas diretrizes](azure-to-azure-autoupdate.md) para obter informações sobre todas as opções disponíveis.

3. Copie o arquivo de instalação no computador protegido e execute-o para atualizar o agente de mobilidade.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Atualizar a conta usada para a instalação por push do serviço de mobilidade

Quando você implantou o Site Recovery para habilitar a instalação por push do serviço de mobilidade, especificou uma conta que o servidor de processo do Site Recovery usa para acessar os computadores e instalar o serviço quando a replicação é habilitada para o computador. Se você quiser atualizar as credenciais dessa conta, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Desinstalar o serviço de mobilidade

### <a name="on-a-windows-machine"></a>Em uma máquina Windows

Desinstale pela interface do usuário ou por um prompt de comando.

- **Pela interface de usuário**: no Painel de Controle do computador, selecione **Programas**. Selecione **Microsoft Azure site Recovery serviço de mobilidade/desinstalação do servidor de destino mestre**  >  .
- **Por um prompt de comando**: Abra uma janela de prompt de comando como administrador no computador local. Execute o comando a seguir: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Em um computador Linux
1. No computador Linux, entre como um usuário **raiz**.
2. Em um terminal, vá para/usr/local/ASR.
3. Execute o comando a seguir:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Instalar Site Recovery provedor VSS no computador de origem

Azure Site Recovery provedor de VSS é necessário no computador de origem para gerar pontos de consistência de aplicativo. Se a instalação do provedor não tiver sido realizada com a instalação por push, siga as diretrizes abaixo para instalá-lo manualmente.

1. Abra a janela do administrador cmd.
2. Navegue até o local de instalação do serviço de mobilidade. (Por exemplo, C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent)
3. Execute o script InMageVSSProvider_Uninstall. cmd. Isso desinstalará o serviço, se ele já existir.
4. Execute o script InMageVSSProvider_Install. cmd para instalar o provedor VSS manualmente.

## <a name="next-steps"></a>Próximas etapas

- [Configurar a recuperação de desastre para VMs VMware](vmware-azure-tutorial.md)
- [Configure a recuperação de desastre para servidores físicos](physical-azure-disaster-recovery.md)
