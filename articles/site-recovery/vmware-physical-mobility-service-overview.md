---
title: Sobre o Serviço de Mobilidade para recuperação de desastre de VMs VMware e servidores físicos com Azure Site Recovery | Microsoft Docs
description: Saiba mais sobre o agente do Serviço de Mobilidade para recuperação de desastre de VMs do VMware e servidores físicos no Azure usando o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: c692b1c5b77b95e5487a847b46473906135c3d86
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261143"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Sobre o serviço de Mobilidade para VMs do VMware e servidores físicos

Ao configurar a recuperação de desastres para VMs do VMware e servidores físicos usando o [Azure Site Recovery](site-recovery-overview.md), instale o serviço de mobilidade do Site Recovery em cada VM do VMware local e servidor físico.  O Serviço de mobilidade captura gravações de dados no computador e as encaminha para o servidor de processo do Site Recovery. Você pode implantar o Serviço de Mobilidade usando os seguintes métodos:

- [Logs de instalação por push](#push-installation): Site Recovery instala o agente de mobilidade no servidor quando a proteção é habilitada via portal do Azure.
- Instalar manualmente: Você pode instalar o serviço de mobilidade manualmente em cada computador por meio [da interface do usuário ou do](#install-mobility-agent-through-ui) [prompt de comando](#install-mobility-agent-through-command-prompt).
- [Implantação automatizada](vmware-azure-mobility-install-configuration-mgr.md): Você pode automatizar a instalação com ferramentas de implantação de software, como o System Center Configuration Manager.

## <a name="anti-virus-on-replicated-machines"></a>Antivírus em computadores replicados

Se as máquinas que você deseja replicar tiverem o software antivírus ativo em execução, exclua a pasta de instalação do serviço Mobility das operações de antivírus (*C:\ProgramData\ASR\agent*). Isso garante que a replicação funciona conforme o esperado.

## <a name="push-installation"></a>Instalação por push

A instalação por push é parte integrante do trabalho de "[habilitar replicação](vmware-azure-enable-replication.md#enable-replication)" disparado no Portal. Depois de escolher o conjunto de máquinas virtuais que você deseja proteger e disparar "Habilitar replicação", o servidor de configuração envia por push o agente de mobilidade para os servidores, instala o agente e completa o registro do agente com o servidor de configuração. Para a conclusão bem-sucedida dessa operação,

- Verifique se todos os [pré-requisitos](vmware-azure-install-mobility-service.md) de instalação por push foram atendidos.
- Verifique se todas as configurações de servidores enquadram-se na [matriz de suporte do VMware para o cenário de Dr do Azure](vmware-physical-azure-support-matrix.md).

Os detalhes do fluxo de trabalho de instalação por push foram descritos nas seções a seguir.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>Da [versão 9,23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) em diante

Durante a instalação por push do agente de mobilidade, as etapas a seguir são executadas

1. Envia o agente para o computador de origem. A cópia do agente no computador de origem pode falhar devido a vários erros ambientais. Visite [nossas diretrizes](vmware-azure-troubleshoot-push-install.md) para solucionar falhas de instalação por push.
2. Depois que o agente é copiado com êxito para as verificações de pré-requisitos do servidor são executadas no servidor. A instalação falhará se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem atendidos. Se todos os pré-requisitos forem atendidos, a instalação será disparada.
3. Azure Site Recovery provedor VSS está instalado no servidor como parte da instalação do agente de mobilidade. Esse provedor é usado para gerar pontos consistentes do aplicativo. Se a instalação do provedor VSS falhar, esta etapa será ignorada e a instalação do agente continuará.
4. Se a instalação do agente for bem-sucedida, mas a instalação do provedor do VSS falhar, o status do trabalho será marcado como "aviso". Isso não afeta a geração de pontos de consistência de falha.

    a. Para gerar pontos consistentes do aplicativo, consulte [nossas diretrizes](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) para concluir a instalação do provedor de site Recovery VSS manualmente. </br>
    b.  Se você não quiser que os pontos consistentes do aplicativo sejam gerados, [modifique a política de replicação](vmware-azure-set-up-replication.md#create-a-policy) para desativar os pontos consistentes do aplicativo.

### <a name="before-922-versions"></a>Antes de 9,22 versões

1. Envia o agente para o computador de origem. A cópia do agente no computador de origem pode falhar devido a vários erros ambientais. Visite [nossas diretrizes](vmware-azure-troubleshoot-push-install.md) para solucionar falhas de instalação por push.
2. Depois que o agente é copiado com êxito para as verificações de pré-requisitos do servidor são executadas no servidor. A instalação falhará se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem atendidos. Se todos os pré-requisitos forem atendidos, a instalação será disparada.
3. Azure Site Recovery provedor VSS está instalado no servidor como parte da instalação do agente de mobilidade. Esse provedor é usado para gerar pontos consistentes do aplicativo. Se a instalação do provedor VSS falhar, a instalação do agente falhará. Para evitar a falha da instalação do agente de mobilidade, use a [versão 9,23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) ou superior para gerar pontos consistentes de falha e instalar o provedor do VSS manualmente.

## <a name="install-mobility-agent-through-ui"></a>Instalar o agente de mobilidade por meio da interface do usuário

### <a name="prerequisite"></a>Pré-requisito

- Verifique se todas as configurações de servidores enquadram-se na [matriz de suporte do VMware para o cenário de Dr do Azure](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) com base no sistema operacional do servidor.

>[!IMPORTANT]
> Se você estiver replicando a VM IaaS do Azure de uma região do Azure para outra, não use esse método. Em vez disso, use o método de instalação baseado em linha de comando.

1. Copie o arquivo de instalação para a máquina e execute-o.
2. Na **Opção de Instalação**, selecione **Instalar o serviço de mobilidade**.
3. Selecione o local de instalação > **instalar**.

    ![Página de opção de instalação do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Monitore a instalação em **Progresso da Instalação**. Após a conclusão da instalação, selecione **Prossiga para a Configuração** para registrar o serviço no servidor de configuração.

    ![Página de registro do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. Em **detalhes do servidor de configuração**, especifique o endereço IP e a senha que você configurou.  

    ![Página de registro do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Selecione **Registrar** para concluir o registro.

    ![Página final de registro do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Instalar o agente de mobilidade por meio do prompt de comando

### <a name="prerequisite"></a>Pré-requisito

- Verifique se todas as configurações de servidores enquadram-se na [matriz de suporte do VMware para o cenário de Dr do Azure](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) com base no sistema operacional do servidor.

### <a name="on-a-windows-machine"></a>Em uma máquina Windows

- Copie o instalador para uma pasta local (digamos, C:\Temp) no servidor que você deseja proteger.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Instale da seguinte maneira:

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Registre o agente no servidor de configuração.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Configurações de instalação
**Configuração** | **Detalhes**
--- | ---
Uso | UnifiedAgent. exe/role \<MS/MT > local \<de instalação do/InstallLocation >/Platform "VMware"/Silent
Logs de instalação | Sob %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Parâmetro de instalação obrigatório. Especifica se o serviço Mobility (MS) ou o destino mestre (MT) deve ser instalado.
/InstallLocation| Parâmetro opcional. Especifica o local de instalação do serviço Mobility (qualquer pasta).
/Platform | Obrigatório. Especifica a plataforma onde o Serviço de Mobilidade está instalado. **VMware** para VMs do Mware/servidores físicos; **Azure** para VMs do Azure.
/Silent| Opcional. Especifica se deve executar o instalador no modo silencioso.

#### <a name="registration-settings"></a>Configurações de registro
**Configuração** | **Detalhes**
--- | ---
Uso | UnifiedAgentConfigurator. exe/CSEndPoint \<CSIP >/PassphraseFilePath \<PassphraseFilePath >
Logs de configuração do agente | Sob %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parâmetro obrigatório. Especifica o endereço de IP do servidor de configuração. Use qualquer endereço de IP válido.
/PassphraseFilePath |  Obrigatório. Local da frase secreta. Use qualquer caminho UNC ou arquivo local válido.

### <a name="on-a-linux-machine"></a>Em um computador Linux

1. Copie o instalador para uma pasta local (digamos, /tmp) no servidor que você deseja proteger. Execute os seguintes comandos em um terminal:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Instale da seguinte maneira:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Após concluir a instalação, o Serviço de Mobilidade precisa ser registrado no servidor de configuração. Execute o seguinte comando para registrar o Serviço de Mobilidade com o servidor de configuração:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Configurações de instalação
**Configuração** | **Detalhes**
--- | ---
Uso | ./Install-d \<local de instalação >- \<r MS/MT >-v VMware-q
-r | Parâmetro de instalação obrigatório. Especifica se o serviço Mobility (MS) ou o destino mestre (MT) deve ser instalado.
-d | Parâmetro opcional. Especifica o local de instalação do serviço Mobility: / usr / local / ASR.
-v | Obrigatório. Especifica a plataforma onde o Serviço de Mobilidade está instalado. **VMware** para VMs do Mware/servidores físicos; **Azure** para VMs do Azure.
-q | Opcional. Especifica se deve executar o instalador no modo silencioso.

#### <a name="registration-settings"></a>Configurações de registro
**Configuração** | **Detalhes**
--- | ---
Uso | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh-i \<CSIP >-P \<PassphraseFilePath >
-i | Parâmetro obrigatório. Especifica o endereço de IP do servidor de configuração. Use qualquer endereço de IP válido.
-P |  Obrigatório. Caminho de arquivo completo do arquivo no qual a frase secreta é salvo. Use qualquer pasta válida.

## <a name="azure-virtual-machine-agent"></a>Agente de Máquina Virtual do Azure

- **VMs do Windows**: A partir da versão 9.7.0.0 do serviço de mobilidade, o [agente de VM do Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) é instalado pelo instalador do serviço de mobilidade. Isso garante que, quando um computador fizer failover no Azure, a VM do Azure atenderá ao pré-requisito da instalação do agente para usar qualquer extensão de VM.
- **VMs Linux**: O [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) deve ser instalado manualmente na VM do Azure após o failover.

## <a name="locate-installer-files"></a>Localizar arquivos do instalador

Vá para a pasta%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository no servidor de configuração. Verifique qual instalador é necessário com base no sistema operacional. A tabela a seguir resume os arquivos do instalador para cada sistema operacional VMware VM e servidor físico. Você pode revisar os [sistemas operacionais compatíveis](vmware-physical-azure-support-matrix.md#replicated-machines) antes de começar.

**Arquivo de instalador** | **Sistema operacional (somente 64 bits)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1,SP2,SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Servidor do Ubuntu Linux 16.04 LTS
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Próximas etapas

[Configurar instalação do Serviço de Mobilidade por push](vmware-azure-install-mobility-service.md).
