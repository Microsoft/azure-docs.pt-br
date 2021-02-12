---
title: Guia para configurar um computador de modelo do Windows | Microsoft Docs
description: Etapas genéricas para preparar um computador de modelo do Windows no Lab Services.  Essas etapas incluem a configuração Windows Update agendamento, instalação do OneDrive e instalação do Office.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 216dc843b31eac355e1d818014f3d70b2ef83132
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647895"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Guia para configurar um computador de modelo do Windows no Azure Lab Services

Se você estiver configurando um computador de modelo do Windows 10 para Azure Lab Services, aqui estão algumas práticas recomendadas e dicas a serem consideradas. As etapas de configuração a seguir são todas opcionais.  No entanto, essas etapas preparatórias podem ajudar a tornar seus alunos mais produtivos, minimizar as interrupções de tempo de classe e garantir que estejam usando as tecnologias mais recentes.

>[!IMPORTANT]
>Este artigo contém trechos de código do PowerShell para simplificar o processo de modificação do modelo de computador.  Para todos os scripts do PowerShell mostrados, você desejará executá-los no Windows PowerShell com privilégios de administrador. No Windows 10, uma maneira rápida de fazer isso é clicar com o botão direito do mouse no menu iniciar e escolher "Windows PowerShell (administrador)".

## <a name="install-and-configure-onedrive"></a>Instalar e configurar o OneDrive

Para proteger os dados dos alunos de serem perdidos se uma máquina virtual for redefinida, recomendamos que os alunos retornem seus dados para a nuvem.  O Microsoft OneDrive pode ajudar os alunos a proteger seus dados.  

### <a name="install-onedrive"></a>Instalar o OneDrive

Para baixar e instalar [manualmente o onedrive](https://onedrive.live.com/about/download/) , consulte as páginas de download do onedrive ou [onedrive for Business](https://onedrive.live.com/about/business/) .

Você também pode usar o seguinte script do PowerShell.  Ele baixará e instalará automaticamente a versão mais recente do OneDrive.  Depois que o cliente do OneDrive for instalado, execute o instalador.  Em nosso exemplo, usamos a `/allUsers` opção para instalar o onedrive para todos os usuários no computador. Também usamos a `/silent` opção para instalar silenciosamente o onedrive.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>Personalizações do OneDrive

Há muitas [personalizações que podem ser feitas no onedrive](/onedrive/use-group-policy). Vamos abordar algumas das personalizações mais comuns.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Mover silenciosamente as pastas conhecidas do Windows para o OneDrive

Pastas como documentos, downloads e imagens geralmente são usadas para armazenar arquivos de aluno. Para garantir que o backup dessas pastas seja feito no OneDrive, recomendamos que você mova essas pastas para o OneDrive.

Se você estiver em um computador que não está usando Active Directory, os usuários poderão mover manualmente essas pastas para o OneDrive depois de se autenticarem no OneDrive.

1. Abrir o Explorador de Arquivos
2. Clique com o botão direito do mouse na pasta documentos, downloads ou imagens.
3. Vá para propriedades > local.  Mova a pasta para uma nova pasta no diretório do OneDrive.

Se sua máquina virtual estiver conectada a Active Directory, você poderá definir o computador de modelo para solicitar automaticamente que os alunos movam as pastas conhecidas para o OneDrive.  

Você precisará recuperar a ID da sua organização primeiro.  Para obter mais instruções, consulte [localizar sua ID de organização Microsoft 365](/onedrive/find-your-office-365-tenant-id).  Você também pode obter a ID da organização usando o PowerShell a seguir.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Depois de ter a ID da sua organização, defina o OneDrive para solicitar a movimentação de pastas conhecidas para o OneDrive usando o PowerShell a seguir.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Usar arquivos do OneDrive sob demanda

Os alunos podem ter muitos arquivos em suas contas do OneDrive. Para ajudar a economizar espaço no computador e reduzir o tempo de download, é recomendável fazer com que todos os arquivos armazenados na conta do OneDrive do aluno sejam sob demanda.  Os arquivos sob demanda são baixados somente quando um usuário acessa o arquivo.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Conectar usuários silenciosamente ao OneDrive

O OneDrive pode ser definido para entrar automaticamente com as credenciais do Windows do usuário conectado.  A entrada automática é útil para as classes em que o aluno entra com suas credenciais da escola.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Desabilite o tutorial que aparece no final da instalação do OneDrive

Essa configuração permite impedir que o tutorial seja iniciado em um navegador da Web no final da instalação do OneDrive.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Definir o tamanho máximo de um arquivo a ser baixado automaticamente

Essa configuração é usada em conjunto com silenciosamente usuários de conexão com o cliente de sincronização do OneDrive com suas credenciais do Windows em dispositivos que não têm os arquivos do OneDrive sob demanda habilitados. Qualquer usuário que tenha um OneDrive maior do que o limite especificado (em MB) será solicitado a escolher as pastas que desejam sincronizar antes que o cliente de sincronização do OneDrive (OneDrive.exe) Baixe os arquivos.  Em nosso exemplo, "1111-2222-3333-4444" é a ID da organização e 0005000 define um limite de 5 GB.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-365"></a>Instalar e configurar Microsoft 365

### <a name="install-microsoft-365"></a>Instalar Microsoft 365

Se a sua máquina de modelo precisar do Office, recomendamos a instalação do Office por meio da [ODT (ferramenta de implantação do Office)](https://www.microsoft.com/download/details.aspx?id=49117). Você precisará criar um arquivo de configuração reutilizável usando o [centro de administração de aplicativos Microsoft 365](https://config.office.com/) para escolher qual arquitetura, quais recursos serão necessários no Office e com que frequência ele é atualizado.

1. Vá para [Microsoft 365 centro de administração de aplicativos](https://config.office.com/) e baixe seu próprio arquivo de configuração.
2. Baixe a [ferramenta de implantação do Office](https://www.microsoft.com/download/details.aspx?id=49117).  O arquivo baixado será `setup.exe` .
3. Execute `setup.exe /download configuration.xml` para baixar componentes do Office.
4. Execute `setup.exe /configure configuration.xml` para instalar os componentes do Office.

### <a name="change-the-microsoft-365-update-channel"></a>Alterar o canal de atualização de Microsoft 365

Usando a ferramenta de configuração do Office, você pode definir com que frequência o Office recebe atualizações. No entanto, se você precisar modificar a frequência com que o Office recebe atualizações após a instalação, você pode alterar a URL do canal de atualização. Os endereços de URL de canal de atualização podem ser encontrados em [alterar o canal de atualização de aplicativos Microsoft 365 para dispositivos em sua organização](/deployoffice/change-update-channels). O exemplo a seguir mostra como definir Microsoft 365 para usar o canal de atualização mensal.

```powershell
# Update to the Microsoft 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Instalar e configurar atualizações

### <a name="install-the-latest-windows-updates"></a>Instalar as atualizações mais recentes do Windows

Recomendamos que você instale as atualizações mais recentes da Microsoft no computador de modelo para fins de segurança antes de publicar a VM de modelo.  Ele também evita que os alunos sejam interrompidos em seu trabalho quando as atualizações são executadas em momentos inesperados.

1. Iniciar **configurações** no menu iniciar
2. Clique em **Atualizar** segurança de &
3. Clique em **verificar atualizações**
4. As atualizações serão baixadas e instaladas.

Você também pode usar o PowerShell para atualizar o computador do modelo.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Algumas atualizações podem exigir que o computador seja reiniciado.  Você será avisado se uma reinicialização for necessária.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Instalar as atualizações mais recentes para aplicativos Microsoft Store

É recomendável que todos os aplicativos Microsoft Store sejam atualizados para suas versões mais recentes.  Aqui estão as instruções para atualizar manualmente os aplicativos do Microsoft Store.  

1. Inicie o aplicativo **Microsoft Store** .
2. Clique na elipse (...) ao lado da foto do usuário no canto superior do aplicativo.
3. Selecione **Download** e atualizações no menu suspenso.
4. Clique no botão **obter atualização** .

Você também pode usar o PowerShell para atualizar Microsoft Store aplicativos que já estão instalados.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Parar atualizações automáticas do Windows

Depois de atualizar o Windows para a versão mais recente, você pode considerar a interrupção das atualizações do Windows.  As atualizações automáticas podem interferir potencialmente com a hora da classe agendada.  Se o seu curso for uma execução mais longa, considere solicitar que os alunos verifiquem manualmente se há atualizações ou definindo atualizações automáticas para um tempo fora das horas de classe agendadas.  Para obter mais informações sobre as opções de personalização para Windows Update, consulte [gerenciar configurações de Windows Update adicionais](/windows/deployment/update/waas-wu-settings).

As atualizações automáticas do Windows podem ser interrompidas usando o seguinte script do PowerShell.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Instalar pacotes de idiomas estrangeiros

Se precisar de idiomas adicionais instalados na máquina virtual, você poderá adicioná-los por meio do Microsoft Store.

1. Iniciar Microsoft Store
2. Pesquise por "pacote de idiomas"
3. Escolha o idioma para instalar

Se você já estiver conectado à VM de modelo, use o atalho "instalar pacote de idiomas" ( `ms-settings:regionlanguage?activationSource=SMC-IA-4027670` ) para ir diretamente para a página de configurações apropriada.

## <a name="remove-unneeded-built-in-apps"></a>Remover aplicativos internos desnecessários

O Windows 10 vem com muitos aplicativos internos que podem não ser necessários para sua classe em particular. Para simplificar a imagem do computador para alunos, talvez você queira desinstalar alguns aplicativos do seu computador de modelo.  Para ver uma lista de aplicativos instalados, use o cmdlet do PowerShell `Get-AppxPackage` .  O exemplo a seguir mostra todos os aplicativos instalados que podem ser removidos.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Para remover um aplicativo, use o cmdlet Remove-Appx.  O exemplo a seguir mostra como remover tudo relacionado ao XBox.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Instalar aplicativos comuns relacionados ao ensino

Instale outros aplicativos comumente usados para ensinar por meio do aplicativo da Windows Store. As sugestões incluem aplicativos como [o Microsoft whiteboard app](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [o Microsoft Teams e o](https://www.microsoft.com/store/productId/9MSPC6MP8FM4) [Minecraft Education Edition](https://education.minecraft.net/). Esses aplicativos devem ser instalados manualmente por meio da Windows Store ou de seus respectivos sites na VM de modelo.

## <a name="conclusion"></a>Conclusão

Este artigo mostrou as etapas opcionais para preparar a VM de modelo do Windows para uma classe efetiva.  As etapas incluem a instalação do OneDrive e a instalação do Microsoft 365, a instalação das atualizações do Windows e a instalação de atualizações para Microsoft Store aplicativos.  Também discutimos como definir atualizações para um agendamento que funciona melhor para sua classe.  

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo sobre como controlar o comportamento de desligamento do Windows para ajudar no gerenciamento de custos: [guia para controlar o comportamento de desligamento do Windows](how-to-windows-shutdown.md)