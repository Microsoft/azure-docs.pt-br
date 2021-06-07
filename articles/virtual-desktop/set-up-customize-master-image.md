---
title: Preparar e personalizar uma imagem VHD mestre – Azure
description: Como preparar, personalizar e carregar uma imagem mestra de área de trabalho virtual do Windows no Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 01/19/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: ca92e6e888ead6bbd79c7a8d524516ee95e8c45d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092936"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Preparar e personalizar uma imagem de VHD mestre

Este artigo mostra como preparar uma imagem de VHD (disco rígido virtual) mestre para carregar no Azure, incluindo como criar máquinas virtuais (VMs) e instalar software neles. Essas instruções são referentes a uma configuração específica da Área de Trabalho Virtual do Windows que pode ser usada com os processos existentes de sua organização.

>[!IMPORTANT]
>Recomendamos que você use uma imagem da Galeria de imagens do Azure. No entanto, se você precisar usar uma imagem personalizada, certifique-se de que você ainda não tem o agente de área de trabalho virtual do Windows instalado em sua VM. O uso de uma imagem personalizada com o agente de área de trabalho virtual do Windows pode causar problemas com a imagem, como o bloqueio de registro e a prevenção de conexões de sessão de usuário.  

## <a name="create-a-vm"></a>Criar uma máquina virtual

O Windows 10 Enterprise Multi-Session está disponível na Galeria de imagens do Azure. Há duas opções para personalizar essa imagem.

A primeira opção é provisionar uma VM (máquina virtual) no Azure seguindo as instruções em [criar uma VM com base em uma imagem gerenciada](../virtual-machines/windows/create-vm-generalized-managed.md)e, em seguida, pular para a [preparação e instalação de software](set-up-customize-master-image.md#software-preparation-and-installation).

A segunda opção é criar a imagem localmente baixando a imagem, Provisionando uma VM do Hyper-V e personalizando-a para atender às suas necessidades, que abordamos na seção a seguir.

### <a name="local-image-creation"></a>Criação de imagem local

Depois de baixar a imagem em um local local, abra o **Gerenciador do Hyper-V** para criar uma VM com o VHD que você copiou. As instruções a seguir são uma versão simples, mas você pode encontrar instruções mais detalhadas em [criar uma máquina virtual no Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Para criar uma VM com o VHD copiado:

1. Abra o **Assistente de nova máquina virtual**.

2. Na página especificar geração, selecione **geração 1**.

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da página especificar geração. A opção "geração 1" está selecionada.](media/a41174fd41302a181e46385e1e701975.png)

3. Em tipo de ponto de verificação, desabilite os pontos de verificação desmarcando a caixa de seleção.

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da seção tipo de ponto de verificação da página pontos de verificação.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Você também pode executar o seguinte cmdlet no PowerShell para desabilitar pontos de verificação.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Disco fixo

Se você criar uma VM de um VHD existente, ele criará um disco dinâmico por padrão. Ele pode ser alterado para um disco fixo selecionando **Editar disco...** conforme mostrado na imagem a seguir. Para obter instruções mais detalhadas, consulte [preparar um VHD do Windows ou VHDX para carregar no Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

> [!div class="mx-imgBorder"]
> ![Uma captura de tela da opção Editar disco.](media/35772414b5a0f81f06f54065561d1414.png)

Você também pode executar o seguinte cmdlet do PowerShell para alterar o disco para um disco fixo.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Preparação e instalação de software

Esta seção aborda como preparar e instalar o FSLogix e o Windows Defender, bem como algumas opções de configuração básicas para aplicativos e o registro da imagem.

Se você estiver instalando Microsoft 365 aplicativos para Enterprise e OneDrive em sua VM, vá para [instalar o Office em uma imagem VHD mestre](install-office-on-wvd-master-image.md) e siga as instruções para instalar os aplicativos. Depois de terminar, retorne a este artigo.

Se os usuários precisarem acessar determinados aplicativos LOB, recomendamos que você os instale depois de concluir as instruções desta seção.

### <a name="set-up-user-profile-container-fslogix"></a>Configurar o contêiner de perfil de usuário (FSLogix)

Para incluir o contêiner FSLogix como parte da imagem, siga as instruções em [criar um contêiner de perfil para um pool de hosts usando um compartilhamento de arquivos](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Você pode testar a funcionalidade do contêiner FSLogix com este guia de [início rápido](/fslogix/configure-cloud-cache-tutorial/).

### <a name="configure-windows-defender"></a>Configurar o Windows Defender

Se o Windows Defender estiver configurado na VM, verifique se ele está configurado para não verificar todo o conteúdo dos arquivos VHD e VHDX durante o anexo.

Essa configuração remove apenas a verificação de arquivos VHD e VHDX durante o anexo, mas não afeta a verificação em tempo real.

Para obter instruções mais detalhadas sobre como configurar o Windows Defender no Windows Server, consulte [configurar exclusões do Windows Defender antivírus no Windows Server](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/).

Para saber mais sobre como configurar o Windows Defender para excluir determinados arquivos da verificação, consulte [configurar e validar exclusões com base na extensão do arquivo e no local da pasta](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/).

### <a name="disable-automatic-updates"></a>Desabilitar Atualizações Automáticas

Para desabilitar Atualizações Automáticas via Política de Grupo local:

1. Abra **Editor de Política de Grupo Local \\ modelos administrativos \\ componentes do Windows \\ Windows Update**.
2. Clique com o botão direito do mouse em **Configurar atualização automática** e defina-a como **desabilitada**.

Você também pode executar o comando a seguir em um prompt de comando para desabilitar o Atualizações Automáticas.

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Especificar o layout inicial para PCs com Windows 10 (opcional)

Execute este comando para especificar um layout inicial para PCs com Windows 10.

```cmd
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Configurar o redirecionamento de fuso horário

O redirecionamento de fuso horário pode ser imposto no nível de Política de Grupo, já que todas as VMs em um pool de hosts fazem parte do mesmo grupo de segurança.

Para redirecionar fusos horários:

1. No Active Directory Server, abra o **console de gerenciamento de política de grupo**.
2. Expanda seu domínio e Política de Grupo objetos.
3. Clique com o botão direito do mouse no **objeto política de grupo** que você criou para as configurações da política de grupo e selecione **Editar**.
4. No **Editor de gerenciamento de política de grupo**, navegue até **configurações do computador**  >  **políticas**  >  **modelos administrativos**  >  **componentes do Windows**  >  **serviços de área de trabalho remota**  >  **host da sessão da área de trabalho remota**  >  **redirecionamento de dispositivo e recurso**.
5. Habilite a configuração **permitir redirecionamento de fuso horário** .

Você também pode executar esse comando na imagem mestra para redirecionar os fusos horários:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Desabilitar o sensor de armazenamento

Para o host de sessão de Área de Trabalho Virtual do Windows que usa multisseção do Windows 10 Enterprise ou Windows 10 Enterprise, é recomendável desabilitar o sensor de armazenamento. Você pode desabilitar o sensor de armazenamento no menu configurações, em **armazenamento**, conforme mostrado na seguinte captura de tela:

> [!div class="mx-imgBorder"]
> ![Uma captura de tela do menu armazenamento em configurações. A opção "sensor de armazenamento" está desativada.](media/storagesense.png)

Você também pode alterar a configuração com o registro executando o seguinte comando:

```cmd
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Incluir suporte a idiomas adicionais

Este artigo não aborda como configurar o suporte a idiomas e regionais. Para obter mais informações, consulte os seguintes artigos:

- [Adicionar idiomas a imagens do Windows](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Recursos sob demanda](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Recursos de linguagem e região sob demanda (FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Outros aplicativos e configuração do registro

Esta seção aborda a configuração do aplicativo e do sistema operacional. Todas as configurações nesta seção são feitas por meio de entradas do registro que podem ser executadas por ferramentas de linha de comando e regedit.

>[!NOTE]
>Você pode implementar as práticas recomendadas na configuração com objetos de Política de Grupo (GPOs) ou importações de registro. O administrador pode escolher uma das opções com base nos requisitos de sua organização.

Para coleta de Hub de comentários de dados de telemetria no Windows 10 Enterprise Multi-Session, execute este comando:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Execute o seguinte comando para corrigir falhas do Watson:

```cmd
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Insira os seguintes comandos no editor do registro para corrigir o suporte à resolução de 5K. Você deve executar os comandos para poder habilitar a pilha lado a lado.

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Preparar a imagem para carregar no Azure

Depois de concluir a configuração e instalar todos os aplicativos, siga as instruções em [preparar um VHD do Windows ou VHDX para carregar no Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) para preparar a imagem.

Depois de preparar a imagem para upload, verifique se a VM permanece no estado desligado ou desalocado.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Carregar imagem mestra em uma conta de armazenamento no Azure

Esta seção se aplica somente quando a imagem mestra foi criada localmente.

As instruções a seguir lhe dirão como carregar sua imagem mestra em uma conta de armazenamento do Azure. Se você ainda não tiver uma conta de armazenamento do Azure, siga as instruções neste [artigo](/azure/developer/javascript/tutorial-vscode-static-website-node-03) para criar uma.

1. Converta a imagem de VM (VHD) para fixa se você ainda não tiver feito isso. Se você não converter a imagem em Fixed, não será possível criar a imagem com êxito.

2. Carregue o VHD em um contêiner de BLOB em sua conta de armazenamento. Você pode carregar rapidamente com a [ferramenta de Gerenciador de armazenamento](https://azure.microsoft.com/features/storage-explorer/). Para saber mais sobre a ferramenta de Gerenciador de Armazenamento, consulte [Este artigo](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows).

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da janela de pesquisa da ferramenta de Gerenciador de Armazenamento do Microsoft Azure. A caixa de seleção "carregar arquivos. VHD ou vhdx como BLOBs de página (recomendado)" está marcada.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Em seguida, vá para o portal do Azure no navegador e pesquise "imagens". Sua pesquisa deve levá-lo para a página **criar imagem** , conforme mostrado na seguinte captura de tela:

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da página Criar imagem do portal do Azure, preenchida com valores de exemplo para a imagem.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Depois de criar a imagem, você deverá ver uma notificação como a da captura de tela a seguir:

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da notificação "imagem criada com êxito".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma imagem, você pode criar ou atualizar pools de hosts. Para saber mais sobre como criar e atualizar pools de hosts, consulte os seguintes artigos:

- [Criar um pool de host com um modelo do Azure Resource Manager](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)
- [Tutorial: Criar um pool de host com o Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Criar um pool de host com o PowerShell](create-host-pools-powershell.md)
- [Criar um contêiner de perfil para um pool de hosts usando um compartilhamento de arquivo](create-host-pools-user-profile.md)
- [Configurar o método de balanceamento de carga da Área de Trabalho Virtual do Windows](configure-host-pool-load-balancing.md)

Se você tiver encontrado um problema de conectividade depois de preparar ou personalizar a imagem VHD, confira o [Guia de solução de problemas](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved) para obter ajuda.
