---
title: Automatizar a instalação do Serviço de Mobilidade do Azure Site Recovery para recuperação de desastre de VMs e servidores físicos VMware para o Azure usando o System Center Configuration Manager | Microsoft Docs
description: Este artigo ajuda a automatizar a instalação do Serviço de Mobilidade com o System Center Configuration Manager para recuperação de desastre de VMs e servidores físicos VMware para o Azure usando o Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: 35c317c4b73e9a22e3b0d6192abcfc2a596066b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598274"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>Automatizar a instalação do Serviço de Mobilidade com o System Center Configuration Manager

O serviço de Mobilidade está instalado em VMs VMware e servidores físicos que deseja replicar para o Azure usando o [Azure Site Recovery](site-recovery-overview.md)

Este artigo fornece um exemplo de como é possível usar o System Center Configuration Manager para implantar o Serviço de Mobilidade do Azure Site Recovery em VM VMware. O uso de uma ferramenta de implantação de software como o Configuration Manager traz as seguintes vantagens:

* Agendar atualizações e instalações recentes durante a janela de manutenção agendada para atualizações de software
* Escalar implantação em centenas de servidores simultaneamente

Este artigo usa o System Center Configuration Manager 2012 R2 para demonstrar a atividade de implantação. Vamos assumir que você está utilizando a versão **9.9.4510.1** ou superior do serviço de Mobilidade.

Como alternativa, você pode automatizar a instalação do serviço de mobilidade com [DSC de automação do Azure](vmware-azure-mobility-deploy-automation-dsc.md).

## <a name="prerequisites"></a>Pré-requisitos

1. Uma ferramenta de implantação de software, como o Gerenciador de Configurações, já está implantada no ambiente.
2. É necessário criar duas [coleções de dispositivos](https://technet.microsoft.com/library/gg682169.aspx), uma para todos os **servidores Windows** e outra para todos os **servidores Linux** que você quer proteger com o Site Recovery.
3. Um servidor de configuração que já está registrado no cofre dos Serviços de Recuperação.
4. Um compartilhamento de arquivos na rede seguro (compartilhamento no SMB) que pode ser acessado pelo computador do gerenciador de configurações.

## <a name="deploy-on-windows-machines"></a>Implantar em computadores Windows
> [!NOTE]
> Este artigo pressupõe que o endereço IP do servidor de configuração é 192.168.3.121 e que o compartilhamento de arquivos de rede segura é \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Preparar para a implantação
1. Crie uma pasta no compartilhamento de rede e nomeie-a **MobSvcWindows**.
2. Entre no servidor de configuração e abra um prompt de comando administrativo.
3. Execute os seguintes comandos para gerar um arquivo de frase secreta:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Copie o arquivo **MobSvc.passphrase** para a pasta **MobSvcWindows** no compartilhamento de rede.
5. Procure o repositório do instalador no servidor de configuração executando o seguinte comando:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Copie **Microsoft-ASR\_UA\_*version*\_Windows\_GA\_*date*\_Release.exe** para a pasta **MobSvcWindows** no compartilhamento de rede.
7. Copie o código a seguir e salve-o como **install.bat** na pasta **MobSvcWindows**.

   > [!NOTE]
   > Substitua os espaços reservados de [CSIP] neste script pelos valores reais do endereço IP do servidor de configuração.

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

### <a name="create-a-package"></a>Criar um pacote

1. Conecte-se ao console do Configuration Manager.
2. Navegue para **Biblioteca de Software** > **Gerenciamento de Aplicativos** > **Pacotes**.
3. Clique com o botão direito do mouse em **Pacotes** e selecione **Criar Pacote**.
4. Forneça valores para nome, descrição, fabricante, idioma e versão.
5. Marque a caixa de seleção **Este pacote contém os arquivos de origem**.
6. Clique em **Procurar** e selecione o compartilhamento de rede em que o instalador está armazenado (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

   ![Captura de tela do Assistente para Criar Pacote e Programa](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. Na página **Escolha o tipo de programa que você deseja criar**, selecione **Programa Padrão** e clique em **Avançar**.

   ![Captura de tela do Assistente para Criar Pacote e Programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Na página **Especificar informações sobre este programa padrão**, forneça as entradas a seguir e clique em **Avançar**. (As outras entradas podem usar seus valores padrão.)

   | **Nome do parâmetro** | **Valor** |
   |--|--|
   | NOME | Instalar o Serviço de Mobilidade do Microsoft Azure (Windows) |
   | Linha de comando | install.bat |
   | Programa pode ser executado | Se um usuário fez logon ou não |

   ![Captura de tela do Assistente para Criar Pacote e Programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. Na próxima página, selecione os sistemas operacionais de destino. O Serviço de Mobilidade pode ser instalado somente no Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2.

   ![Captura de tela do Assistente para Criar Pacote e Programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2.png)

10. Para concluir o assistente, clique em **Avançar** duas vezes.


> [!NOTE]
> O script dá suporte tanto a instalações novas dos agentes do Serviço de Mobilidade quanto a atualizações de agentes já instalados.

### <a name="deploy-the-package"></a>Implantar o pacote
1. No console do Configuration Manager, clique com o botão direito do mouse no pacote e selecione **Distribuir Conteúdo**.
   ![Captura de tela do console do Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Selecione os **[pontos de distribuição](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** para os quais os pacotes devem ser copiados.
3. Conclua o assistente. Em seguida, o pacote começa a ser replicado para os pontos de distribuição especificados.
4. Após a conclusão da distribuição do pacote, clique com o botão direito do mouse no pacote e selecione **Implantar**.
   ![Captura de tela do console do Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Selecione a coleção de dispositivos do Windows Server criada na seção de pré-requisitos como a coleção de destino para a implantação.

   ![Captura de tela do Assistente de Implantação de Software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. Na página **Especificar o destino de conteúdo**, selecione os **Pontos de Distribuição**.
7. Na página **Especificar as configurações para controlar como este software é implantado**, verifique se a finalidade é **Obrigatória**.

   ![Captura de tela do Assistente de Implantação de Software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Na página **Especificar o agendamento para esta implantação**, especifique um agendamento. Para obter mais informações, consulte [Agendando pacotes](https://technet.microsoft.com/library/gg682178.aspx).
9. Na página **Pontos de Distribuição**, configure as propriedades de acordo com as necessidades de seu datacenter. Em seguida, conclua o assistente.

> [!TIP]
> Para evitar reinicializações desnecessárias, agende a instalação do pacote durante a janela de manutenção mensal ou a janela de atualizações de software.

É possível monitorar o andamento da implantação usando o console do Configuration Manager. Acesse **Monitoramento** > **Implantações** > *[nome do pacote]*.

  ![Captura de tela da opção Configuration Manager para monitorar as implantações](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Implantar em computadores Linux
> [!NOTE]
> Este artigo pressupõe que o endereço IP do servidor de configuração é 192.168.3.121 e que o compartilhamento de arquivos de rede segura é \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Preparar para a implantação
1. Crie uma pasta no compartilhamento de rede e nomeie-a **MobSvcLinux**.
2. Entre no servidor de configuração e abra um prompt de comando administrativo.
3. Execute os seguintes comandos para gerar um arquivo de frase secreta:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Copie o arquivo **MobSvc.passphrase** para a pasta **MobSvcLinux** no compartilhamento de rede.
5. Procure o repositório do instalador no servidor de configuração executando o comando:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Copie os seguintes arquivos para a pasta **MobSvcLinux** no compartilhamento de rede:
   * Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. Copie o código a seguir e salve-o como **install_linux.sh** na pasta **MobSvcLinux**.
   > [!NOTE]
   > Substitua os espaços reservados de [CSIP] neste script pelos valores reais do endereço IP do servidor de configuração.

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

1. Conecte-se ao console do Configuration Manager.
2. Navegue para **Biblioteca de Software** > **Gerenciamento de Aplicativos** > **Pacotes**.
3. Clique com o botão direito do mouse em **Pacotes** e selecione **Criar Pacote**.
4. Forneça valores para nome, descrição, fabricante, idioma e versão.
5. Marque a caixa de seleção **Este pacote contém os arquivos de origem**.
6. Clique em **Procurar** e selecione o compartilhamento de rede em que o instalador está armazenado (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

   ![Captura de tela do Assistente para Criar Pacote e Programa](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. Na página **Escolha o tipo de programa que você deseja criar**, selecione **Programa Padrão** e clique em **Avançar**.

   ![Captura de tela do Assistente para Criar Pacote e Programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Na página **Especificar informações sobre este programa padrão**, forneça as entradas a seguir e clique em **Avançar**. (As outras entradas podem usar seus valores padrão.)

    | **Nome do parâmetro** | **Valor** |
   |--|--|
   | NOME | Instalar o Serviço de Mobilidade do Microsoft Azure (Linux) |
   | Linha de comando | ./install_linux.sh |
   | Programa pode ser executado | Se um usuário fez logon ou não |

   ![Captura de tela do Assistente para Criar Pacote e Programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. Na próxima página, selecione **Este programa pode ser executado em qualquer plataforma**.
   ![Captura de tela do Assistente para Criar Pacote e Programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. Para concluir o assistente, clique em **Avançar** duas vezes.

> [!NOTE]
> O script dá suporte tanto a instalações novas dos agentes do Serviço de Mobilidade quanto a atualizações de agentes já instalados.

### <a name="deploy-the-package"></a>Implantar o pacote
1. No console do Configuration Manager, clique com o botão direito do mouse no pacote e selecione **Distribuir Conteúdo**.
   ![Captura de tela do console do Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Selecione os **[pontos de distribuição](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** para os quais os pacotes devem ser copiados.
3. Conclua o assistente. Em seguida, o pacote começa a ser replicado para os pontos de distribuição especificados.
4. Após a conclusão da distribuição do pacote, clique com o botão direito do mouse no pacote e selecione **Implantar**.
   ![Captura de tela do console do Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Selecione a coleção de dispositivos do Linux Server criada na seção de pré-requisitos como a coleção de destino para implantação.

   ![Captura de tela do Assistente de Implantação de Software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. Na página **Especificar o destino de conteúdo**, selecione os **Pontos de Distribuição**.
7. Na página **Especificar as configurações para controlar como este software é implantado**, verifique se a finalidade é **Obrigatória**.

   ![Captura de tela do Assistente de Implantação de Software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Na página **Especificar o agendamento para esta implantação**, especifique um agendamento. Para obter mais informações, consulte [Agendando pacotes](https://technet.microsoft.com/library/gg682178.aspx).
9. Na página **Pontos de Distribuição**, configure as propriedades de acordo com as necessidades de seu datacenter. Em seguida, conclua o assistente.

O Serviço de Mobilidade é instalado na Coleção de Dispositivos do Linux Server de acordo com o agendamento configurado.


## <a name="uninstall-the-mobility-service"></a>Desinstalar o serviço de Mobilidade
Você pode criar pacotes do Configuration Manager para desinstalar o Serviço de Mobilidade. Use o seguinte script para fazer isso:

```
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
Agora você está pronto para [habilitar a proteção](vmware-azure-enable-replication.md) para as máquinas virtuais.
