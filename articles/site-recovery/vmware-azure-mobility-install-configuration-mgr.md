---
title: Automatizar o serviço de mobilidade para recuperação de desastre de instalação no Azure Site Recovery
description: Como instalar automaticamente o serviço de mobilidade para a recuperação de desastre do VMware/Physical Server com o Azure Site Recovery.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: 2159ab8c2639f0f87fd53e8559dad518a3daa663
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92544810"
---
# <a name="automate-mobility-service-installation"></a>Automatizar a instalação do serviço de mobilidade

Este artigo descreve como automatizar a instalação e as atualizações para o agente do serviço de mobilidade no [Azure site Recovery](site-recovery-overview.md).

Ao implantar Site Recovery para recuperação de desastre de VMs VMware locais e servidores físicos no Azure, você instala o agente do serviço de mobilidade em cada computador que deseja replicar. O serviço de mobilidade captura gravações de dados no computador e as encaminha para o servidor de processo de Site Recovery para replicação. Você pode implantar o serviço de mobilidade de algumas maneiras:

- **Instalação por push**: permita que site Recovery instale o agente do serviço de mobilidade quando você habilitar a replicação para um computador no portal do Azure.
- **Instalação manual**: Instale o serviço de mobilidade manualmente em cada computador. [Saiba mais](vmware-physical-mobility-service-overview.md) sobre a instalação manual e por push.
- **Implantação automatizada**: automatize a instalação com ferramentas de implantação de software, como o Microsoft Endpoint Configuration Manager ou ferramentas de terceiros, como JetPatch.

A instalação automatizada e a atualização fornece uma solução se:

- Sua organização não permite a instalação por push em servidores protegidos.
- A política de sua empresa requer que as senhas sejam alteradas periodicamente. Você precisa especificar uma senha para a instalação por push.
- Sua política de segurança não permite adicionar exceções de firewall para computadores específicos.
- Você está atuando como um provedor de serviços de hospedagem e não deseja fornecer as credenciais do computador do cliente que são necessárias para a instalação por push com o Site Recovery.
- Você precisa dimensionar as instalações do agente para vários servidores simultaneamente.
- Você deseja agendar instalações e atualizações durante as janelas de manutenção planejadas.

## <a name="prerequisites"></a>Pré-requisitos

Para automatizar a instalação, você precisa dos seguintes itens:

- Uma solução de instalação de software implantada, como [Configuration Manager](/configmgr/) ou [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/).
- Os pré-requisitos de implantação estão em vigor no [Azure](tutorial-prepare-azure.md) e [no local](vmware-azure-tutorial-prepare-on-premises.md) para a recuperação de desastres do VMware ou para a recuperação de desastres do [servidor físico](physical-azure-disaster-recovery.md) . Examine os [requisitos de suporte](vmware-physical-azure-support-matrix.md) para recuperação de desastre.

## <a name="prepare-for-automated-deployment"></a>Preparar para implantação automatizada

A tabela a seguir resume as ferramentas e os processos para automatizar a implantação do serviço de mobilidade.

**Ferramenta** | **Detalhes** | **Instruções**
--- | --- | ---
**Configuration Manager** | 1. Verifique se você tem os [pré-requisitos](#prerequisites) listados acima em vigor. <br/><br/> 2. implante a recuperação de desastres Configurando o ambiente de origem, incluindo o download de um arquivo OVA para implantar o servidor de configuração de Site Recovery como uma VM VMware usando um modelo OVF.<br/><br/> 3. Registre o servidor de configuração com o serviço de Site Recovery, configure o ambiente de destino do Azure e configure uma política de replicação.<br/><br/> 4. para a implantação automatizada do serviço de mobilidade, você cria um compartilhamento de rede que contém a senha do servidor de configuração e os arquivos de instalação do serviço de mobilidade.<br/><br/> 5. você cria um pacote de Configuration Manager que contém a instalação ou as atualizações e prepara para a implantação do serviço de mobilidade.<br/><br/> 6. você pode habilitar a replicação para o Azure para os computadores que têm o serviço de mobilidade instalado. | [Automatizar com o Configuration Manager](#automate-with-configuration-manager)
**JetPatch** | 1. Verifique se você tem os [pré-requisitos](#prerequisites) listados acima em vigor. <br/><br/> 2. implante a recuperação de desastres Configurando o ambiente de origem, incluindo baixar e implantar JetPatch Gerenciador de Agentes para Azure Site Recovery em seu ambiente de Site Recovery, usando um modelo OVF.<br/><br/> 3. você registra o servidor de configuração com Site Recovery, configura o ambiente de destino do Azure e configura uma política de replicação.<br/><br/> 4. para a implantação automatizada, inicialize e conclua a configuração de Gerenciador de Agentes JetPatch.<br/><br/> 5. no JetPatch, você pode criar uma política de Site Recovery para automatizar a implantação e a atualização do agente do serviço de mobilidade. <br/><br/> 6. você pode habilitar a replicação para o Azure para os computadores que têm o serviço de mobilidade instalado. | [Automatizar com o JetPatch Gerenciador de Agentes](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Solucionar problemas de instalação do agente no JetPatch](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatizar com o Configuration Manager

### <a name="prepare-the-installation-files"></a>Preparar os arquivos de instalação

1. Verifique se você tem os pré-requisitos em vigor.
1. Crie um compartilhamento de arquivos de rede segura (compartilhamento SMB) que possa ser acessado pelo computador que executa o servidor de configuração.
1. Em Configuration Manager, [categorize os servidores](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) nos quais você deseja instalar ou atualizar o serviço de mobilidade. Uma coleção deve conter todos os servidores Windows, os outros servidores Linux.
1. No compartilhamento de rede, crie uma pasta:

   - Para a instalação em computadores Windows, crie uma pasta chamada _MobSvcWindows_.
   - Para a instalação em computadores Linux, crie uma pasta chamada _MobSvcLinux_.

1. Conecte-se ao computador do servidor de configuração.
1. Na máquina do servidor de configuração, abra um prompt de comando administrativo.
1. Para gerar o arquivo de senha, execute este comando:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Copie o arquivo _arquivo mobsvc. passphrase_ para a pasta do Windows e a pasta do Linux.
1. Para navegar até a pasta que contém os arquivos de instalação, execute este comando:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Copie esses arquivos de instalação para o compartilhamento de rede:

   - Para o Windows, copie _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ para _MobSvcWindows_.
   - Para o Linux, copie os seguintes arquivos para _MobSvcLinux_:
     - _Microsoft-ASR_UARHEL6 -64release. tar. gz_
     - _Microsoft-ASR_UARHEL7 -64release. tar. gz_
     - _Microsoft-ASR_UASLES11-SP3-64release. tar. gz_
     - _Microsoft-ASR_UASLES11-SP4-64release. tar. gz_
     - _Microsoft-ASR_UAOL6 -64release. tar. gz_
     - _Microsoft-ASR_UAUBUNTU-14.04 -64release. tar. gz_

1. Conforme descrito nos procedimentos a seguir, copie o código para as pastas do Windows ou do Linux. Estamos supondo que:

   - O endereço IP do servidor de configuração é `192.168.3.121` .
   - O compartilhamento de arquivos de rede segura é `\\ContosoSecureFS\MobilityServiceInstallers` .

### <a name="copy-code-to-the-windows-folder"></a>Copiar o código para a pasta do Windows

Copie o código a seguir:

- Salve o código na pasta _MobSvcWindows_ como _install.bat_.
- Substitua os `[CSIP]` espaços reservados neste script pelos valores reais do endereço IP do seu servidor de configuração.
- O script dá suporte a novas instalações do agente do serviço de mobilidade e atualizações para agentes que já estão instalados.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Copiar o código para a pasta do Linux

Copie o código a seguir:

- Salve o código na pasta _MobSvcLinux_ como _install_linux. sh_.
- Substitua os `[CSIP]` espaços reservados neste script pelos valores reais do endereço IP do seu servidor de configuração.
- O script dá suporte a novas instalações do agente do serviço de mobilidade e atualizações para agentes que já estão instalados.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Criar um pacote

1. Entre no console do Configuration Manager e vá para **biblioteca de software**  >  **Gerenciamento de aplicativos**  >  **pacotes**.
1. Clique com o botão direito do mouse em **pacotes**  >  **criar pacote**.
1. Forneça detalhes do pacote, incluindo nome, descrição, fabricante, idioma e versão.
1. Selecione **Este pacote contém arquivos de origem**.
1. Clique em **procurar** e selecione o compartilhamento de rede e a pasta que contém o instalador relevante (_MobSvcWindows_ ou _MobSvcLinux_). Em seguida, selecione **Avançar**.

   ![Captura de tela do Assistente para Criar Pacote e Programa](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. Na página **escolha o tipo de programa que você deseja criar** , selecione **programa padrão**  >  **Avançar**.

   ![Captura de tela do assistente para criar pacote e programa que mostra a opção programa padrão.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. Em **especificar informações sobre esta página de programa padrão** , especifique os seguintes valores:

    **Parâmetro** | **Valor do Windows** | **Valor do Linux**
    --- | --- | ---
    **Nome** | Instalar o Serviço de Mobilidade do Microsoft Azure (Windows) | Instale o serviço de mobilidade de Microsoft Azure (Linux).
    **Linha de comando** | install.bat | ./install_linux.sh
    **O programa pode ser executado** | Se um usuário fez logon ou não | Se um usuário fez logon ou não
    **Outros parâmetros** | Usar configuração padrão | Usar configuração padrão

   ![Captura de tela que mostra as informações que você pode especificar para o programa padrão.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. Em **especificar os requisitos para este programa padrão**, execute as seguintes tarefas:

   - Para computadores Windows, selecione **este programa pode ser executado somente em plataformas especificadas**. Em seguida, selecione os [sistemas operacionais Windows com suporte](vmware-physical-azure-support-matrix.md#replicated-machines) e selecione **Avançar**.
   - Para computadores Linux, selecione **este programa pode ser executado em qualquer plataforma**. Em seguida, selecione **Avançar**.

1. Conclua o assistente.

### <a name="deploy-the-package"></a>Implantar o pacote

1. No console do Configuration Manager, clique com o botão direito do mouse no pacote e selecione **distribuir conteúdo**.

   ![Captura de tela do console do Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Selecione os pontos de distribuição para os quais os pacotes devem ser copiados. [Saiba mais](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Conclua o assistente. Em seguida, o pacote começa a ser replicado para os pontos de distribuição especificados.
1. Depois que a distribuição do pacote for concluída, clique com o botão direito do mouse no pacote > **implantar**.

   ![Captura de tela do console de Configuration Manager que mostra a opção de menu implantar.](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Selecione a coleção de dispositivos Windows ou Linux que você criou anteriormente.
1. Na página **especificar o destino do conteúdo** , selecione **pontos de distribuição**.
1. Na página **especificar configurações para controlar como este software é implantado** , defina a **finalidade** como **obrigatória**.

   ![Captura de tela do Assistente de Implantação de Software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. Em **especificar o agendamento para essa implantação**, configure uma agenda. [Saiba mais](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - O serviço de mobilidade é instalado de acordo com o agendamento que você especificar.
   - Para evitar reinicializações desnecessárias, agende a instalação do pacote durante a janela de manutenção mensal ou a janela de atualizações de software.

1. Na página **pontos de distribuição** , defina as configurações e conclua o assistente.
1. Monitore o progresso da implantação no console do Configuration Manager. Vá para **monitoramento**  >  **implantações**  >  _\<your package name\>_ .

### <a name="uninstall-the-mobility-service"></a>Desinstalar o serviço de mobilidade

Você pode criar pacotes de Configuration Manager para desinstalar o serviço de mobilidade. Por exemplo, o script a seguir desinstala o serviço de mobilidade:

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>Próximas etapas

[Habilite a replicação](vmware-azure-enable-replication.md) para VMs.
