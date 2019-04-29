---
title: Preparar e personalizar uma imagem de VHD mestre - Azure
description: Como preparar, personalizar e carregar uma imagem mestre de visualização de área de trabalho Virtual do Windows no Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2019
ms.author: helohr
ms.openlocfilehash: aff96931f95442c67d08521e72952dd79dad44e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870172"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Preparar e personalizar uma imagem de VHD mestre

Este artigo informa como preparar uma imagem de disco de rígido virtual (VHD) mestre para carregar no Azure, incluindo como criar máquinas virtuais (VMs) e instale e configure o software neles. Essas instruções são para uma configuração específica de visualização de área de trabalho Virtual do Windows que pode ser usada com os processos existentes da sua organização.

## <a name="create-a-vm"></a>Criar uma máquina virtual

Várias sessões do Windows 10 Enterprise está disponível na Galeria de imagens do Azure. Há duas opções para personalizar essa imagem.

A primeira opção é provisionar uma máquina virtual (VM) no Azure, seguindo as instruções em [criar uma VM de uma imagem gerenciada](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)e, em seguida, vá direto para [instalação e preparação de Software](set-up-customize-master-image.md#software-preparation-and-installation).

A segunda opção é criar a imagem localmente por baixar a imagem, o provisionamento de uma VM do Hyper-V e personalizá-la para atender às suas necessidades, que descreveremos na seção a seguir.

### <a name="local-image-creation"></a>Criação de imagem local

Depois de baixar a imagem para uma localização local, abra **Gerenciador do Hyper-V** para criar uma VM com o VHD que você acabou de copiar. O seguinte é a versão simple, mas você pode encontrar instruções mais detalhadas [criar uma máquina virtual no Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).

Para criar uma VM com o VHD copiado:

1. Abra o **Assistente de nova máquina Virtual**.

2. Na página especificar geração, selecione **geração 1**.

    ![Uma captura de tela da página especificar geração. A opção "Geração 1" está selecionada.](media/a41174fd41302a181e46385e1e701975.png)

3. Em tipo de ponto de verificação, desabilite os pontos de verificação, desmarcando a caixa de seleção.

    ![Uma captura de tela da seção de tipo de ponto de verificação da página pontos de verificação.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Você também pode executar o seguinte cmdlet do PowerShell para desabilitar os pontos de verificação.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Disco fixo

Se você criar uma VM de um VHD existente, ele cria um disco dinâmico por padrão. Ele pode ser alterado para um disco fixo, selecionando **Editar disco...**  conforme mostrado na imagem a seguir. Para obter instruções mais detalhadas, consulte [preparar um VHD do Windows ou o VHDX para carregar no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

![Uma captura de tela da opção Editar disco.](media/35772414b5a0f81f06f54065561d1414.png)

Você também pode executar o seguinte cmdlet do PowerShell para alterar o disco em um disco fixo.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>A instalação e preparação de software

Esta seção aborda como preparar e instalar o Office 365 ProPlus, OneDrive, FSLogix, o Windows Defender e outros aplicativos comuns. Se os usuários precisam acessar determinados aplicativos de LOB, é recomendável que instalá-los depois de concluir as instruções desta seção.

Esta seção pressupõe que você tenha o acesso elevado na VM, se ela é provisionada no Azure ou o Gerenciador do Hyper-V.

### <a name="install-office-in-shared-computer-activation-mode"></a>Instalar o Office no modo de ativação de computador compartilhado

Use o [ferramenta de implantação do Office](https://www.microsoft.com/download/details.aspx?id=49117) para instalar o Office. Várias sessões do Windows 10 Enterprise só dá suporte ao Office 365 ProPlus, não o Office 2019 perpétua.

A ferramenta de implantação do Office requer um arquivo XML de configuração. Para personalizar o exemplo a seguir, consulte a [opções de configuração para a ferramenta de implantação do Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Este exemplo de configuração XML que fornecemos fará o seguinte:

- Instalar o Office do canal Insiders e fornecer atualizações do canal Insiders quando forem executados.
- Arquitetura de uso x64.
- Desabilite as atualizações automáticas.
- Instale o Visio e o projeto.
- Remova quaisquer instalações existentes do Office e migrar suas configurações.
- Habilite o licenciamento para a operação em um ambiente de servidor de terminal de computador compartilhado.

Aqui está o que não fazer esse exemplo de configuração XML:

- Instalar o Skype for Business
- Instale o OneDrive em modo por usuário. Para obter mais informações, consulte [OneDrive instalar no modo de por máquina](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Licenciamento de computador compartilhado pode ser configurado por meio de configurações do registro ou objetos de diretiva de grupo (GPOs). O GPO está localizado em **configuração do computador\\diretivas\\modelos administrativos\\(máquina) do Microsoft Office 2016\\configurações de licenciamento**

A ferramenta de implantação do Office contém setup.exe. Para instalar o Office, execute o seguinte comando em uma linha de comando:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>XML de exemplo

O exemplo XML a seguir instalará a versão de especialistas, também conhecido como Fast Insiders ou Insiders principal.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>A equipe do Office recomenda o uso de instalação de 64 bits para o **OfficeClientEdition** parâmetro.

Depois de instalar o Office, você pode atualizar o comportamento do Office padrão. Execute os seguintes comandos individualmente ou em um arquivo em lotes para atualizar o comportamento.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

### <a name="disable-automatic-updates"></a>Desabilitar as atualizações automáticas

Para desabilitar atualizações automáticas por meio da diretiva de grupo local:

1. Abra **Editor de diretiva de Grupo Local\\modelos administrativos\\componentes do Windows\\Windows Update**.
2. Clique com botão direito **configurar a atualização automática** e defina-o como **desabilitado**.

Você também pode executar o seguinte comando no prompt de comando para desabilitar as atualizações automáticas.

```batch
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Especifique o layout inicial para PCs com Windows 10 (opcional)

Execute este comando para especificar um layout de início para PCs com Windows 10.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="install-onedrive-in-per-machine-mode"></a>Instalar o OneDrive no modo de por máquina

OneDrive é normalmente instalado por usuário. Nesse ambiente, ele deve ser instalado por máquina.

Aqui está como instalar o OneDrive no modo de por máquina:

1. Primeiro, crie um local para preparar o instalador do OneDrive. Uma pasta de disco local ou [\\\\unc] (file://unc) local está tudo bem.

2. Baixe OneDriveSetup.exe para seu local de preparo com este link: <https://aka.ms/OneDriveWVD-Installer>

3. Se você instalar o office com o OneDrive, omitindo  **\<ExcludeApp ID = "OneDrive" /\>**, desinstale qualquer existente OneDrive as instalações por usuário em um prompt de comando elevado executando o seguinte comando:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Execute este comando em um prompt de comando elevado para definir a **AllUsersInstall** valor do registro:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Execute este comando para instalar o OneDrive no modo de por máquina:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Execute este comando para configurar o OneDrive para iniciar na entrada para todos os usuários:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Habilitar **silenciosamente configurar conta de usuário** executando o comando a seguir.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Redirecionar e mover conhecido pastas no OneDrive, executando o seguinte comando do Windows.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

### <a name="teams-and-skype"></a>As equipes e o Skype

Área de trabalho Virtual do Windows não oferece suporte oficialmente Skype para empresas e equipes.

### <a name="set-up-user-profile-container-fslogix"></a>Configurar o contêiner de perfil do usuário (FSLogix)

Para incluir o contêiner FSLogix como parte da imagem, siga as instruções em [configurar um compartilhamento de perfil de usuário para um pool de host](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Você pode testar a funcionalidade do contêiner com FSLogix [este guia de início rápido](https://docs.fslogix.com/display/20170529/Profile+Containers+-+Quick+Start).

### <a name="configure-windows-defender"></a>Configurar o Windows Defender

Se o Windows Defender está configurado na VM, verifique se que ele foi configurado para verificação não todo o conteúdo dos arquivos VHD e VHDX durante o anexo do mesmo.

Essa configuração só remove a verificação de arquivos VHD e VHDX durante o anexo, mas não afetará a verificação em tempo real.

Para obter mais instruções sobre como configurar o Windows Defender no Windows Server, consulte [exclusões de configurar o Windows Defender antivírus no Windows Server](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus).

Para saber mais sobre como configurar o Windows Defender para excluir determinados arquivos da verificação, consulte [configurar e validar as exclusões com base no local do arquivo de extensão e a pasta](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus).

### <a name="configure-session-timeout-policies"></a>Configurar políticas de tempo limite de sessão

As políticas de sessão remota podem ser aplicadas em nível de política de grupo, pois todas as VMs em um pool de hosts fazem parte do mesmo grupo de segurança.

Para configurar as políticas de sessão remota:

1. Navegue até **modelos administrativos** > **componentes do Windows** > **serviços de área de trabalho remota**  >  **Host de sessão da área de trabalho remota** > **limites de tempo de sessão**.
2. No painel à direita, selecione a **definir o limite de tempo para sessões de serviços de área de trabalho remota ativas, mas ociosas** política.
3. Depois que a janela modal aparece, altere a opção de política de **não configurado** à **habilitado** para ativar a política.
4. No menu suspenso sob a opção de política, defina a quantidade de tempo para **4 horas**.

Você também pode configurar políticas de sessão remota manualmente, executando os comandos a seguir:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fResetBroken /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxConnectionTime /t REG_DWORD /d 10800000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxDisconnectionTime /t REG_DWORD /d 5000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxIdleTime /t REG_DWORD /d 7200000 /f
```

### <a name="set-up-time-zone-redirection"></a>Configurar o redirecionamento de fuso horário

Redirecionamento de fuso horário pode ser aplicado em nível de política de grupo, pois todas as VMs em um pool de hosts fazem parte do mesmo grupo de segurança.

Para redirecionar os fusos horários:

1. No servidor do Active Directory, abra o **Console de gerenciamento de diretiva de grupo**.
2. Expanda seu domínio e objetos de diretiva de grupo.
3. Clique com botão direito do **o objeto de diretiva de grupo** que você criou para as configurações de diretiva de grupo e selecione **editar**.
4. No **Editor de gerenciamento de diretiva de grupo**, navegue até **configuração do computador** > **políticas** > **administrativo Modelos** > **componentes do Windows** > **dos serviços de área de trabalho remota** > **Host de sessão da área de trabalho remota**   >  **Dispositivo e o redirecionamento de recursos**.
5. Habilitar o **permitir o redirecionamento de fuso horário** configuração.

Você também pode executar esse comando na imagem mestre para redirecionar os fusos horários:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Desabilitar o sensor de armazenamento

Para o host de sessão de área de trabalho Virtual do Windows que usam o Windows 10 Enterprise ou várias sessões do Windows 10 Enterprise, é recomendável desabilitar o sensor de armazenamento. Você pode desabilitar o sensor de armazenamento no menu Configurações, sob **armazenamento**, conforme mostrado na seguinte captura de tela:

![Uma captura de tela do menu em configurações de armazenamento. A opção "Sentido de armazenamento" é desativada.](media/storagesense.png)

Você também pode alterar a configuração com o registro, executando o seguinte comando:

```batch
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Incluir suporte a idiomas adicionais

Este artigo não aborda como configurar o suporte idiomático e regional. Para obter mais informações, consulte os seguintes artigos:

- [Adicionar idiomas às imagens do Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/add-language-packs-to-windows)
- [Recursos sob demanda](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities)
- [Recursos de idioma e região sob demanda (FOD)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-language-fod)

### <a name="other-applications-and-registry-configuration"></a>Outros aplicativos e a configuração do registro

Esta seção aborda a configuração de aplicativo e sistema operacional. Todas as configurações desta seção é feito por meio de entradas do registro que podem ser executadas pela linha de comando e ferramentas regedit.

>[!NOTE]
>Você pode implementar as práticas recomendadas na configuração com objetos de política geral (GPOs) ou importações de registro. O administrador pode escolher uma das opções com base nos requisitos da sua organização.

Para a coleção de hub de comentários de dados de telemetria em várias sessões do Windows 10 Enterprise, execute este comando:

```batch
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection "AllowTelemetry"=dword:00000003
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection /v AllowTelemetry /d 3
```

Execute o comando a seguir para corrigir falhas de Watson:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Insira os comandos a seguir no editor do registro para corrigir o suporte de resolução de 5 mil. Você deve executar os comandos antes de habilitar a pilha de lado a lado.

```batch
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Preparar a imagem para upload no Azure

Depois de concluir a configuração e todos os aplicativos instalados, siga as instruções em [preparar um VHD do Windows ou o VHDX para carregar no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) para preparar a imagem.

Depois de preparar a imagem para upload, verifique se que a VM permanece no estado desativado ou desalocado.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Carregar imagem mestra para uma conta de armazenamento do Azure

Esta seção se aplica somente quando a imagem mestra foi criada localmente.

As instruções a seguir descrevem como carregar sua imagem mestre em uma conta de armazenamento do Azure. Se você ainda não tiver uma conta de armazenamento do Azure, siga as instruções em [deste artigo](https://code.visualstudio.com/tutorials/static-website/create-storage) criá-lo.

1. Converta a imagem VM (VHD) como Fixed se você ainda não fez isso. Se você não converter a imagem como fixo, não é possível criar a imagem com êxito.

2. Carregar o VHD em um contêiner de blob na sua conta de armazenamento. Você pode carregar rapidamente com o [ferramenta de Gerenciador de armazenamento](https://azure.microsoft.com/features/storage-explorer/). Para saber mais sobre a ferramenta de Gerenciador de armazenamento, consulte [deste artigo](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![Uma captura de tela da janela de pesquisa da ferramenta do Microsoft Azure Storage Explorer. A caixa de seleção "Carregar arquivos. vhd ou vhdx como blobs de página (recomendados)" está selecionada.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Em seguida, vá para o portal do Azure no seu navegador e procure "Imagens". A pesquisa deve levar você a **criar imagem** página, conforme mostrado na seguinte captura de tela:

    ![Uma captura de tela da página Criar imagem do portal do Azure, preenchido com valores de exemplo para a imagem.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Depois de criar a imagem, você verá uma notificação, como o mostrado na seguinte captura de tela:

    ![Uma captura de tela da notificação de "imagem criada com êxito".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma imagem, você pode criar ou atualizar grupos de host. Para saber mais sobre como criar e atualizar grupos de host, consulte os seguintes artigos:

- [Criar um pool de host com um modelo do Azure Resource Manager](create-host-pools-arm-template.md)
- [Tutorial: Criar um pool de host com o Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Criar um pool de host com o PowerShell](create-host-pools-powershell.md)
- [Configure um compartilhamento de perfil de usuário para um pool de host](create-host-pools-user-profile.md)
- [Configurar o método de balanceamento de carga de área de trabalho Virtual do Windows](configure-host-pool-load-balancing.md)
