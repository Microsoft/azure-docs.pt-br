---
title: "Instalar o Serviço de Mobilidade (VMware ou físico no Azure) | Microsoft Docs"
description: "Saiba como instalar o agente do Serviço de Mobilidade para proteger os computadores locais."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: eb2fbd16980eadfce15227b6ba07f00c47b672ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-mobility-service-vmware-or-physical-to-azure"></a>Instalar o Serviço de Mobilidade (VMware ou físico no Azure)
O Serviço de Mobilidade do Azure Site Recovery captura gravações de dados em um computador e, em seguida, encaminha-os para o servidor em processo. Implante o Serviço de Mobilidade em cada computador (VM VMware ou servidor físico) que você deseja replicar para o Azure. É possível implantar o Serviço de Mobilidade nos servidores que você deseja proteger usando os seguintes métodos:


* [Instalar o Serviço de Mobilidade usando as ferramentas de implantação de software, como o System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
* [Instalar o Serviço de Mobilidade usando a Automação do Azure e o DSC de Automação (Configuração de Estado Desejado)](site-recovery-automate-mobility-service-install.md)
* [Instalar o Serviço de Mobilidade manualmente usando a GUI (interface gráfica do usuário)](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)
* [Instalar o Serviço de Mobilidade manualmente em um prompt de comando](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt)
* [Instalar o Serviço de Mobilidade através da instalação por push a partir do Site Recovery](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> A partir da versão 9.7.0.0, em VMs (máquinas virtuais) Windows, o instalador do Serviço de Mobilidade também instala o último [agente de VM do Azure](../virtual-machines/windows/extensions-features.md#azure-vm-agent) disponível. Quando um computador faz failover no Azure, o computador atende ao pré-requisito da instalação do agente para usar qualquer extensão de VM.

## <a name="prerequisites"></a>Pré-requisitos
Conclua estas etapas de pré-requisito antes de instalar o Serviço de Mobilidade manualmente no servidor:
1. Entre no servidor de configuração e, em seguida, abra uma janela de Prompt de Comando como administrador.
2. Altere o diretório para a pasta bin e, em seguida, crie um arquivo de senha:

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Armazene o arquivo de senha em uma localização segura. É possível usar o arquivo durante a instalação do Serviço de Mobilidade.
4. Os instaladores do Serviço de Mobilidade para todos os sistemas operacionais com suporte estão na pasta %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>Mapeamento do instalador para o sistema operacional do Serviço de Mobilidade

| Nome do modelo do arquivo do instalador| Sistema operacional |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1 (64 bits) </br> Windows Server 2012 (64 bits) </br> Windows Server 2012 R2 (64 bits) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| RHEL (Red Hat Enterprise Linux) 6.4, 6.5, 6.6, 6.7, 6.8 (somente 64 bits) </br> CentOS 6.4, 6.5, 6.6, 6.7, 6.8 (somente 64 bits) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.1, 7.2 (somente 64 bits) </br> CentOS 7.0, 7.1, 7.2 (somente 64 bits)</br> CentOs 7.3 (somente migração) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (somente 64 bits)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 (somente 64 bits)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (somente 64 bits)|
|Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (somente 64 bits)|


## <a name="install-mobility-service-manually-by-using-the-gui"></a>Instalar o Serviço de Mobilidade manualmente usando a GUI

>[!IMPORTANT]
> Se você estiver usando um **Servidor de Configuração** para replicar **as máquinas virtuais Azure IaaS** a partir de uma Subscrição/Região da Azure para outra, **use o método de instalação baseado na linha de comando**

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Instalar o Serviço de Mobilidade manualmente em um prompt de comando

### <a name="command-line-installation-on-a-windows-computer"></a>Instalação de linha de comando em um computador Windows
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Instalação de linha de comando em um computador Linux
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Instalar o Serviço de Mobilidade usando a instalação por push por meio do Azure Site Recovery
Para fazer uma instalação por push do Serviço de Mobilidade usando o Site Recovery, todos os computadores de destino devem atender aos pré-requisitos a seguir:

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
Depois de instalar o Serviço de Mobilidade, no portal do Azure, selecione o botão **+Replicar** para começar a proteger essas VMs.

## <a name="update-mobility-service"></a>Atualizar serviço de mobilidade

> [!WARNING]
> Certifique-se de que o Servidor de Configuração, os Servidores de Processo de Expansão e quaisquer servidores de destino que não sejam parte da sua implantação sejam atualizados antes de você começar a atualizar o Serviço de Mobilidade nos servidores protegidos. Leia mais sobre [como atualizar seu servidor de configuração](site-recovery-vmware-to-azure-manage-configuration-server.md#upgrading-a-configuration-server) e [como atualizar seus servidores de processo de expansão](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#upgrading-a-scale-out-process-server)

1. No portal do Azure, navegue para <Your Vault> -> modo de exibição de itens replicados.
2. Se o **servidor de configuração** já tiver sido atualizado para a versão mais recente, você verá uma notificação que mostra que uma *atualização do agente de replicação do novo Site recovery está disponível. Clique para instalar*
3. Clique na notificação para abrir a página de seleção da máquina virtual.
4. Selecione as máquinas virtuais que você desejar para fazer o upgrade do serviço de mobilidade e clique no botão OK.
5. Isso inicia o trabalho de serviço de mobilidade de atualização para cada uma das máquinas virtuais selecionadas.

> [!NOTE]
> [Leia mais](site-recovery-vmware-to-azure-manage-configuration-server.md) sobre como atualizar a senha para a conta usada para instalar o serviço de mobilidade 

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Desinstalar o Serviço de Mobilidade de um computador Windows Server
Use um dos métodos a seguir para desinstalar o Serviço de Mobilidade em um computador Windows Server.

### <a name="uninstall-by-using-the-gui"></a>Desinstalar usando a GUI
1. No Painel de Controle, selecione **Programas**.
2. Selecione **Serviço de Mobilidade do Microsoft Azure Site Recovery/servidor de Destino Mestre** e, em seguida, **Desinstalar**.

### <a name="uninstall-at-a-command-prompt"></a>Desinstalar em um prompt de comando
1. Abra uma janela de Prompt de comando como administrador.
2. Para desinstalar o Serviço de Mobilidade, execute o seguinte comando:

```
MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Desinstalar o Serviço de Mobilidade de um computador Linux
1. No servidor Linux, entre como um usuário **raiz**.
2. Em um terminal, acesse /user/local/ASR.
3. Para desinstalar o Serviço de Mobilidade, execute o seguinte comando:

```
uninstall.sh -Y
```
