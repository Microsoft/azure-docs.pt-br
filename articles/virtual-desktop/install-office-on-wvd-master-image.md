---
title: Instalar o Office em uma imagem VHD mestre – Azure
description: Como instalar e personalizar o Office em uma imagem mestra de área de trabalho virtual do Windows para o Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6369013d605ae538ad611a28a90e9c099bb7d80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91326041"
---
# <a name="install-office-on-a-master-vhd-image"></a>Instalar o Office em uma imagem de VHD mestre

Este artigo mostra como instalar Microsoft 365 aplicativos para Enterprise, OneDrive e outros aplicativos comuns em uma imagem de disco rígido virtual (VHD) mestre para carregar no Azure. Se os usuários precisarem acessar determinados aplicativos de LOB (linha de negócios), recomendamos que você os instale depois de concluir as instruções neste artigo.

Este artigo pressupõe que você já criou uma VM (máquina virtual). Caso contrário, consulte [preparar e personalizar uma imagem mestre VHD](set-up-customize-master-image.md#create-a-vm)

Este artigo também pressupõe que você tenha acesso elevado na VM, seja provisionado no Azure ou no Gerenciador do Hyper-V. Caso contrário, consulte [elevar o acesso para gerenciar todos os grupos de gerenciamento e assinatura do Azure](../role-based-access-control/elevate-access-global-admin.md).

>[!NOTE]
>Essas instruções são referentes a uma configuração específica da Área de Trabalho Virtual do Windows que pode ser usada com os processos existentes de sua organização.

## <a name="install-office-in-shared-computer-activation-mode"></a>Instalar o Office no modo de ativação do computador compartilhado

A ativação de computador compartilhado permite que você implante Microsoft 365 aplicativos para empresas em um computador em sua organização que é acessado por vários usuários. Para obter mais informações sobre a ativação do computador compartilhado, consulte [visão geral da ativação do computador compartilhado para aplicativos Microsoft 365](/deployoffice/overview-shared-computer-activation).

Use a [ferramenta de implantação do Office](https://www.microsoft.com/download/details.aspx?id=49117) para instalar o Office. O Windows 10 Enterprise Multi-Session dá suporte apenas às seguintes versões do Office:

   - Microsoft 365 Apps para Grandes Empresas
   - Microsoft 365 aplicativos para negócios que acompanham uma assinatura do Microsoft 365 Business Premium

A ferramenta de implantação do Office requer um arquivo XML de configuração. Para personalizar o exemplo a seguir, consulte as [Opções de configuração para a ferramenta de implantação do Office](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/).

Este XML de configuração de exemplo que fornecemos fará o seguinte:

   - Instale o Office do canal corporativo mensal e forneça atualizações do canal corporativo mensal.
   - Use a arquitetura x64.
   - Desabilite as atualizações automáticas.
   - Remova todas as instalações existentes do Office e migre suas configurações.
   - Habilite a ativação do computador compartilhado.

>[!NOTE]
>O recurso de pesquisa de estêncil do Visio pode não funcionar conforme o esperado na área de trabalho virtual do Windows.

Veja o que este XML de configuração de exemplo não fará:

   - Instalar o Skype for Business
   - Instale o OneDrive no modo por usuário. Para saber mais, confira [instalar o onedrive no modo por máquina](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>A ativação do computador compartilhado pode ser configurada por meio de objetos de Política de Grupo (GPOs) ou configurações do registro. O GPO está localizado em **políticas de \\ configuração \\ do computador Modelos Administrativos \\ Microsoft Office 2016 (máquina) \\ configurações de licenciamento**

A ferramenta de implantação do Office contém setup.exe. Para instalar o Office, execute o seguinte comando em uma linha de comando:

```cmd
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>configuration.xml de exemplo

O exemplo de XML a seguir instalará a versão mensal do canal empresarial.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="MonthlyEnterprise">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level="Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>A equipe do Office recomenda usar a instalação de 64 bits para o parâmetro **OfficeClientEdition** .

Depois de instalar o Office, você pode atualizar o comportamento padrão do Office. Execute os comandos a seguir individualmente ou em um arquivo em lotes para atualizar o comportamento.

```cmd
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

## <a name="install-onedrive-in-per-machine-mode"></a>Instalar o OneDrive no modo por máquina

O OneDrive normalmente é instalado por usuário. Nesse ambiente, ele deve ser instalado por computador.

Veja como instalar o OneDrive no modo por máquina:

1. Primeiro, crie um local para preparar o instalador do OneDrive. Um local de pasta de disco local ou [ \\ \\ UNC] (file://UNC) está bem.

2. Baixe OneDriveSetup.exe em seu local de preparação com este link: <https://aka.ms/OneDriveWVD-Installer>

3. Se você instalou o Office com o OneDrive omitindo **\<ExcludeApp ID="OneDrive" /\>** , desinstale todas as instalações existentes do onedrive por usuário em um prompt de comando elevado executando o seguinte comando:

    ```cmd
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Execute este comando em um prompt de comando elevado para definir o valor do registro **AllUsersInstall** :

    ```cmd
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Execute este comando para instalar o OneDrive no modo por máquina:

    ```cmd
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Execute este comando para configurar o OneDrive para iniciar na entrada para todos os usuários:

    ```cmd
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Habilite **Configurar silenciosamente a conta de usuário** executando o comando a seguir.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Redirecione e mova as pastas conhecidas do Windows para o OneDrive executando o comando a seguir.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="microsoft-teams-and-skype-for-business"></a>Microsoft Teams e Skype for Business

A área de trabalho virtual do Windows não dá suporte ao Skype for Business.

Para obter ajuda com a instalação do Microsoft Teams, consulte [usar o Microsoft Teams na área de trabalho virtual do Windows](teams-on-wvd.md). A otimização de mídia para o Microsoft Teams no Windows Virtual Desktop está disponível em versão prévia.

## <a name="next-steps"></a>Próximas etapas

Agora que você adicionou o Office à imagem, você pode continuar a personalizar a imagem do VHD mestre. Consulte [preparar e personalizar uma imagem VHD mestre](set-up-customize-master-image.md).
