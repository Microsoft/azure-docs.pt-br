---
title: Sobre o serviço de mobilidade para recuperação de desastre de VMs VMware e servidores físicos com Azure Site Recovery | Microsoft Docs
description: Saiba mais sobre o agente do serviço de mobilidade para recuperação de desastre de VMs VMware e servidores físicos no Azure usando o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: 8c90427e2dd4ecf23cb803c4f75dbee34c38f992
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898572"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Sobre o serviço de Mobilidade para VMs do VMware e servidores físicos

Ao configurar a recuperação de desastre para VMs (máquinas virtuais) VMware e servidores físicos usando o [Azure site Recovery](site-recovery-overview.md), você instala o serviço de mobilidade de site Recovery em cada servidor físico e VM VMware local. O serviço Mobility captura gravações de dados na máquina e as encaminha para o servidor do processo de Recuperação do Site. O serviço de mobilidade é instalado pelo software do agente do serviço de mobilidade que você pode implantar usando os seguintes métodos:

- [Instalação por push](#push-installation): quando a proteção é habilitada por meio do portal do Azure, site Recovery instala o serviço de mobilidade no servidor.
- Instalação manual: você pode instalar o serviço de mobilidade manualmente em cada computador por meio da [interface do usuário](#install-the-mobility-service-using-ui) ou do [prompt de comando](#install-the-mobility-service-using-command-prompt).
- [Implantação automatizada](vmware-azure-mobility-install-configuration-mgr.md): você pode automatizar a instalação do serviço de mobilidade com ferramentas de implantação de software, como Configuration Manager.

> [!NOTE]
> O serviço de mobilidade usa aproximadamente 6% a 10% da memória em computadores de origem para VMs VMware ou máquinas físicas.

## <a name="antivirus-on-replicated-machines"></a>Antivírus em máquinas replicadas

Se os computadores que você deseja replicar estiverem executando um software antivírus, exclua a pasta de instalação do serviço de mobilidade _C:\ProgramData\ASR\agent_ de operações de antivírus. Essa exclusão garante que a replicação funcionará conforme o esperado.

## <a name="push-installation"></a>Instalação por push

A instalação por push é parte integrante do trabalho executado do portal do Azure para habilitar a [replicação](vmware-azure-enable-replication.md#enable-replication). Depois de escolher o conjunto de VMs que você deseja proteger e habilitar a replicação, o servidor de configuração envia por push o agente do serviço de mobilidade para os servidores, instala o agente e conclui o registro do agente com o servidor de configuração.

### <a name="prerequisites"></a>Pré-requisitos

- Verifique se todos os [pré-requisitos](vmware-azure-install-mobility-service.md) de instalação por push foram atendidos.
- Verifique se todas as configurações do servidor atendem aos critérios na [matriz de suporte para recuperação de desastre de VMs VMware e servidores físicos para o Azure](vmware-physical-azure-support-matrix.md).
- Da versão 9,36 em diante, para SUSE Linux Enterprise Server 11 SP3, RHEL 5, CentOS 5, Debian 7 Verifique se o instalador mais recente está [disponível no servidor de configuração e no servidor de processo de expansão](#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server)

O fluxo de trabalho de instalação por push é descrito nas seções a seguir:

### <a name="mobility-service-agent-version-923-and-higher"></a>Agente do serviço de mobilidade versão 9,23 e superior

Para obter mais informações sobre a versão 9,23, consulte [pacote cumulativo de atualizações 35 para Azure site Recovery](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery).

Durante uma instalação por push do serviço de mobilidade, as seguintes etapas são executadas:

1. O agente é enviado por push para o computador de origem. A cópia do agente para o computador de origem pode falhar devido a vários erros ambientais. Visite [nossas diretrizes](vmware-azure-troubleshoot-push-install.md) para solucionar falhas de instalação por push.
1. Depois que o agente é copiado com êxito para o servidor, uma verificação de pré-requisitos é executada no servidor.
   - Se todos os pré-requisitos forem atendidos, a instalação será iniciada.
   - A instalação falhará se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem atendidos.
1. Como parte da instalação do agente, o provedor de Serviço de Cópias de Sombra de Volume (VSS) para Azure Site Recovery está instalado. O provedor VSS é usado para gerar pontos de recuperação consistentes com o aplicativo. Se a instalação do provedor VSS falhar, essa etapa será ignorada e a instalação do agente continuará.
1. Se a instalação do agente for bem-sucedida, mas a instalação do provedor do VSS falhar, o status do trabalho será marcado como **aviso**. Isso não afeta a geração de ponto de recuperação consistente com falhas.

    - Para gerar pontos de recuperação consistentes com o aplicativo, consulte [nossas diretrizes](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) para concluir uma instalação manual do provedor de site Recovery VSS.
    - Se você não quiser gerar pontos de recuperação consistentes com o aplicativo, [modifique a política de replicação](vmware-azure-set-up-replication.md#create-a-policy) para desativar os pontos de recuperação consistentes com o aplicativo.

### <a name="mobility-service-agent-version-922-and-below"></a>Agente do serviço de mobilidade versão 9,22 e inferior

1. O agente é enviado por push para o computador de origem. A cópia do agente para o computador de origem pode falhar devido a vários erros ambientais. Consulte [nossas diretrizes](vmware-azure-troubleshoot-push-install.md) para solucionar problemas de falhas de instalação por push.
1. Depois que o agente é copiado com êxito para o servidor, uma verificação de pré-requisitos é executada no servidor.
   - Se todos os pré-requisitos forem atendidos, a instalação será iniciada.
   - A instalação falhará se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem atendidos.

1. Como parte da instalação do agente, o provedor de Serviço de Cópias de Sombra de Volume (VSS) para Azure Site Recovery está instalado. O provedor VSS é usado para gerar pontos de recuperação consistentes com o aplicativo.
   - Se a instalação do provedor do VSS falhar, a instalação do agente falhará. Para evitar a falha da instalação do agente, use a [versão 9,23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) ou superior para gerar pontos de recuperação consistentes com falhas e faça uma instalação manual do provedor VSS.

## <a name="install-the-mobility-service-using-ui"></a>Instalar o serviço de mobilidade usando a interface do usuário

### <a name="prerequisites"></a>Pré-requisitos

- Verifique se todas as configurações do servidor atendem aos critérios na [matriz de suporte para recuperação de desastre de VMs VMware e servidores físicos para o Azure](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) do sistema operacional do servidor.

>[!IMPORTANT]
> Não use o método de instalação da interface do usuário se estiver replicando uma VM de IaaS (infraestrutura como serviço) do Azure de uma região do Azure para outra. Use a instalação do [prompt de comando](#install-the-mobility-service-using-command-prompt) .

1. Copie o arquivo de instalação para a máquina e execute-o.
1. Na **Opção de Instalação**, selecione **Instalar o serviço de mobilidade**.
1. Escolha o local de instalação e selecione **instalar**.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Página opção de instalação do serviço de mobilidade.":::

1. Monitore a instalação em **Progresso da Instalação**. Após a conclusão da instalação, selecione **Prossiga para a Configuração** para registrar o serviço no servidor de configuração.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Captura de tela que mostra o progresso da instalação e o botão ativo ir para configuração quando a instalação é concluída.":::

1. Em **detalhes do servidor de configuração**, especifique o endereço IP e a senha que você configurou.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Página de registro do serviço de mobilidade.":::

1. Selecione **Registrar** para concluir o registro.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Página final de registro do serviço de mobilidade.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>Instalar o serviço de mobilidade usando o prompt de comando

### <a name="prerequisites"></a>Pré-requisitos

- Verifique se todas as configurações do servidor atendem aos critérios na [matriz de suporte para recuperação de desastre de VMs VMware e servidores físicos para o Azure](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) do sistema operacional do servidor.

### <a name="windows-machine"></a>Computador com Windows

- Em um prompt de comando, execute os seguintes comandos para copiar o instalador para uma pasta local, como _C:\temp_, no servidor que você deseja proteger. Substitua o nome do arquivo do instalador pelo nome real do arquivo.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- Execute este comando para instalar o agente.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- Execute estes comandos para registrar o agente no servidor de configuração.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Configurações de instalação

Setting | Detalhes
--- | ---
Syntax | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Logs de instalação | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Parâmetro de instalação obrigatório. Especifica se o serviço Mobility (MS) ou o destino mestre (MT) deve ser instalado.
`/InstallLocation`| Parâmetro opcional. Especifica o local de instalação do serviço Mobility (qualquer pasta).
`/Platform` | Mandatory. Especifica a plataforma na qual o serviço de mobilidade está instalado: <br/> **VMware** para VMs VMware/servidores físicos. <br/> **Azure** para VMs do Azure.<br/><br/> Se você estiver tratando VMs do Azure como máquinas físicas, especifique **VMware**.
`/Silent`| Opcional. Especifica se deve executar o instalador no modo silencioso.

#### <a name="registration-settings"></a>Configurações de registro

Setting | Detalhes
--- | ---
Syntax | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Logs de configuração do agente | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Parâmetro obrigatório. `<CSIP>` Especifica o endereço IP do servidor de configuração. Use qualquer endereço de IP válido.
`/PassphraseFilePath` |  Mandatory. Local da frase secreta. Use qualquer caminho UNC ou arquivo local válido.

### <a name="linux-machine"></a>Computador Linux

1. Em uma sessão de terminal, copie o instalador para uma pasta local, como _/tmp_ no servidor que você deseja proteger. Substitua o nome do arquivo do instalador pelo nome de arquivo real da sua distribuição do Linux e execute os comandos.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Instale da seguinte maneira:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. Depois que a instalação for concluída, o serviço de mobilidade deverá ser registrado no servidor de configuração. Execute o comando a seguir para registrar o serviço de mobilidade com o servidor de configuração.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Configurações de instalação

Setting | Detalhes
--- | ---
Syntax | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Parâmetro de instalação obrigatório. Especifica se o serviço Mobility (MS) ou o destino mestre (MT) deve ser instalado.
`-d` | Parâmetro opcional. Especifica o local de instalação do serviço de mobilidade: `/usr/local/ASR` .
`-v` | Mandatory. Especifica a plataforma na qual o serviço de mobilidade está instalado. <br/> **VMware** para VMs VMware/servidores físicos. <br/> **Azure** para VMs do Azure.
`-q` | Opcional. Especifica se deve executar o instalador no modo silencioso.

#### <a name="registration-settings"></a>Configurações de registro

Setting | Detalhes
--- | ---
Syntax | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Parâmetro obrigatório. `<CSIP>` Especifica o endereço IP do servidor de configuração. Use qualquer endereço de IP válido.
`-P` |  Mandatory. Caminho de arquivo completo do arquivo no qual a frase secreta é salvo. Use qualquer pasta válida.

## <a name="azure-virtual-machine-agent"></a>Agente de Máquina Virtual do Azure

- **VMs do Windows**: a partir da versão 9.7.0.0 do serviço de mobilidade, o [agente de VM do Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) é instalado pelo instalador do serviço de mobilidade. Isso garante que, quando o computador fizer failover no Azure, a VM do Azure atenderá ao pré-requisito de instalação do agente para usar qualquer extensão de VM.
- **VMs do Linux**: O [WALinuxAgent](../virtual-machines/extensions/update-linux-agent.md) deve ser instalado manualmente na VM do Azure após o failover.

## <a name="locate-installer-files"></a>Localizar arquivos do instalador

No servidor de configuração, acesse a pasta _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository_. Verifique qual instalador você precisa com base no sistema operacional. A tabela a seguir resume os arquivos do instalador para cada sistema operacional VMware VM e servidor físico. Antes de começar, você pode examinar os [sistemas operacionais com suporte](vmware-physical-azure-support-matrix.md#replicated-machines).

> [!NOTE]
> Os nomes de arquivo usam a sintaxe mostrada na tabela a seguir com a _versão_ e a _Data_ como espaços reservados para os valores reais. Os nomes de arquivo reais serão semelhantes a estes exemplos:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Arquivo de instalador | Sistema operacional (somente 64 bits)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
[Para ser baixado e colocado nesta pasta manualmente](#rhel-5-or-centos-5-server) | Red Hat Enterprise Linux (RHEL) 5 </br> CentOS 5
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_RHEL8-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 8 </br> CentOS 8
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> Inclui o SP2 e o SP3.
[Para ser baixado e colocado nesta pasta manualmente](#suse-11-sp3-server) | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_SLES15-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 15 
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6,4 </br> Oracle Enterprise Linux 6,5
`Microsoft-ASR_UA_version_OL7-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 7 
`Microsoft-ASR_UA_version_OL8-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 8
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Servidor do Ubuntu Linux 16.04 LTS
`Microsoft-ASR_UA_version_UBUNTU-18.04-64_GA_date_release.tar.gz` | Ubuntu Linux servidor LTS 18, 4
`Microsoft-ASR_UA_version_UBUNTU-20.04-64_GA_date_release.tar.gz` | Ubuntu Linux servidor LTS 20, 4
[Para ser baixado e colocado nesta pasta manualmente](#debian-7-server) | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8
`Microsoft-ASR_UA_version_DEBIAN9-64_GA_date_release.tar.gz` | Debian 9

## <a name="download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server"></a>Baixe o instalador do agente de mobilidade mais recente para SUSE 11 SP3, RHEL 5, servidor Debian 7

### <a name="suse-11-sp3-server"></a>Servidor SUSE 11 SP3

Como um **pré-requisito para atualizar ou proteger computadores SuSE Linux Enterprise Server 11 SP3** da versão 9,36 em diante:

1. Verifique se o instalador do agente de mobilidade mais recente foi baixado do centro de download da Microsoft e colocado no repositório do instalador de push no servidor de configuração e todos os servidores de processo de expansão
2. [Baixe](site-recovery-whats-new.md) o instalador do agente do SUSE Linux Enterprise Server 11 SP3 mais recente.
3. Navegue até servidor de configuração, copie o instalador do agente SUSE Linux Enterprise Server 11 SP3 no caminho-INSTALL_DIR \home\svsystems\pushinstallsvc\repository
1. Depois de copiar o instalador mais recente, reinicie o serviço InMage PushInstall. 
1. Agora, navegue até servidores de processo de expansão associados, repita a etapa 3 e a etapa 4.
1. **Por exemplo**, se o caminho de instalação for C:\Program Files (x86) \Microsoft Azure site Recovery, os diretórios mencionados acima serão
    1. C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\home\svsystems\pushinstallsvc\repository

### <a name="rhel-5-or-centos-5-server"></a>Servidor RHEL 5 ou CentOS 5

Como um **pré-requisito para atualizar ou proteger computadores RHEL 5** da versão 9,36 em diante:

1. Verifique se o instalador do agente de mobilidade mais recente foi baixado do centro de download da Microsoft e colocado no repositório do instalador de push no servidor de configuração e todos os servidores de processo de expansão
2. [Baixe](site-recovery-whats-new.md) o instalador do agente RHEL 5 ou CentOS 5 mais recente. 
3. Navegue até servidor de configuração, copie o instalador do agente RHEL 5 ou CentOS 5 no caminho-INSTALL_DIR \home\svsystems\pushinstallsvc\repository
1. Depois de copiar o instalador mais recente, reinicie o serviço InMage PushInstall. 
1. Agora, navegue até servidores de processo de expansão associados, repita a etapa 3 e a etapa 4.
1. **Por exemplo**, se o caminho de instalação for C:\Program Files (x86) \Microsoft Azure site Recovery, os diretórios mencionados acima serão
    1. C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\home\svsystems\pushinstallsvc\repository

## <a name="debian-7-server"></a>Servidor Debian 7

Como um **pré-requisito para atualizar ou proteger computadores Debian 7** da versão 9,36 em diante:

1. Verifique se o instalador do agente de mobilidade mais recente foi baixado do centro de download da Microsoft e colocado no repositório do instalador de push no servidor de configuração e todos os servidores de processo de expansão
2. [Baixe](site-recovery-whats-new.md) o instalador do agente Debian 7 mais recente.
3. Navegue até servidor de configuração, copie o instalador do agente Debian 7 no caminho-INSTALL_DIR \home\svsystems\pushinstallsvc\repository
1. Depois de copiar o instalador mais recente, reinicie o serviço InMage PushInstall. 
1. Agora, navegue até servidores de processo de expansão associados, repita a etapa 3 e a etapa 4.
1. **Por exemplo**, se o caminho de instalação for C:\Program Files (x86) \Microsoft Azure site Recovery, os diretórios mencionados acima serão
    1. C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\home\svsystems\pushinstallsvc\repository

## <a name="next-steps"></a>Próximas etapas

[Configurar instalação do Serviço de Mobilidade por push](vmware-azure-install-mobility-service.md).
