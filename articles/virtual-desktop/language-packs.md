---
title: Instalar pacotes de idiomas em VMs do Windows 10 na Área de Trabalho Virtual do Windows – Azure
description: Como instalar pacotes de idiomas para VMs de várias sessões do Windows 10 na Área de Trabalho Virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: eaf6fc789020553b80967341cc9219a30ffce749
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446104"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Adicionar pacotes de idiomas a uma imagem de várias sessões do Windows 10

A Área de Trabalho Virtual do Windows é um serviço que os usuários podem implantar a qualquer momento, em qualquer lugar. Por isso, é importante que os usuários possam personalizar em qual idioma suas imagens de várias sessões do Windows 10 Enterprise são exibidas.

Há duas maneiras de acomodar as necessidades de idioma dos seus usuários:

- Crie pools de host dedicados com uma imagem personalizada para cada idioma.
- Tenha usuários com requisitos diferentes de idioma e localização no mesmo pool de host, mas personalize as imagens deles para garantir que eles possam selecionar o idioma de que precisam.

O último método é muito mais eficiente e econômico. No entanto, cabe a você decidir qual método melhor atende às suas necessidades. Este artigo mostrará como personalizar idiomas para suas imagens.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes itens para personalizar as imagens de várias sessões do Windows 10 Enterprise e adicionar vários idiomas:

- Uma VM (máquina virtual) do Azure com Windows 10 Enterprise de várias sessões, versão 1903 ou posterior

- O idioma ISO, o FOD (recurso sob demanda) disco 1 e os aplicativos da caixa de entrada ISO da versão do SO usada pela imagem. Você pode baixá-las aqui: 
     
     - Idioma ISO:
        - [Windows 10, ISO do Pacote de Idiomas versão 1903 ou 1909](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, ISO do Pacote de Idiomas versão 2004 ou 20H2](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - ISO FOD disco 1:
        - [Windows 10, ISO FOD disco 1 versão 1903 ou 1909](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, ISO FOD disco 1 versão 2004 ou 20H2](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - ISO dos aplicativos da caixa de entrada:
        - [Windows 10, ISO dos aplicativos da caixa de entrada versão 1903 ou 1909](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, ISO dos aplicativos da caixa de entrada versão 2004](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, ISO dos aplicativos da caixa de entrada versão 20H2](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Se você usar arquivos ISO do LXP (pacote de experiência local) para localizar suas imagens, também será necessário baixar o ISO do LXP apropriado para ter a melhor experiência de linguagem
        - Se você estiver usando o Windows 10, versão 1903 ou 1909:
          - [Windows 10, ISO do LXP versão 1903 ou 1909](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Se você estiver usando o Windows 10, versão 2004 ou 20H2, use as informações em [Como adicionar idiomas no Windows 10: problemas conhecidos](/windows-hardware/manufacture/desktop/language-packs-known-issue) para descobrir qual dos seguintes ISOs do LXP é ideal para você:
          - [Windows 10, versão 2004 ou 20H2 **9B** ISO do LXP](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10, versão 2004 ou 20H2 **9C** ISO do LXP](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10, versão 2004 ou 20H2 **10C** ISO do LXP](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10, versão 2004 ou 20H2 **11C** ISO do LXP](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10, versão 2004 ou 20H2 **1C** ISO do LXP](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [Windows 10, versão 2004 ou 20H2 **2C** ISO do LXP](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)

- Um Compartilhamento de Arquivos do Azure ou um compartilhamento de arquivo em uma máquina virtual do servidor de arquivos do Windows

>[!NOTE]
>O compartilhamento de arquivo (repositório) precisa ser acessível na VM do Azure que você planeja usar para criar a imagem personalizada.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Criar um repositório de conteúdo para pacotes de idiomas e recursos sob demanda

Para criar o repositório de conteúdo para pacotes de idiomas e FODs e um repositório para os pacotes de aplicativos da caixa de entrada:

1. Em uma VM do Azure, baixe os aplicativos ISO de vários idiomas, FODs e de caixa de entrada do Windows 10 para as imagens do Windows 10 Enterprise multissessão, versão 1903/1909 e 2004 dos links em [Pré-requisitos](#prerequisites).

2. Abra e monte os arquivos ISO na VM.

3. Vá para o pacote de idiomas ISO e copie o conteúdo das pastas **LocalExperiencePacks** e **x64\\langpacks**; depois, cole o conteúdo no compartilhamento de arquivo.

4. Vá para o **arquivo ISO FOD**, copie todo o conteúdo e cole-o no compartilhamento de arquivo.
5. Vá para a pasta **amd64fre** no ISO dos aplicativos da caixa de entrada e copie o conteúdo no repositório para os aplicativos da caixa de entrada que você preparou.

     >[!NOTE]
     > Se você estiver trabalhando com armazenamento limitado, copie apenas os arquivos dos idiomas que você sabe que os usuários precisam. Você pode diferenciar os arquivos examinando os códigos de idioma nos nomes do arquivo. Por exemplo, o arquivo francês tem o código "fr-FR" no nome. Para obter uma lista completa de códigos de idioma para todos os idiomas disponíveis, confira [Pacotes de idiomas disponíveis para Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

     >[!IMPORTANT]
     > Alguns idiomas exigem fontes adicionais incluídas em pacotes satélites que seguem diferentes convenções de nomenclatura. Por exemplo, nomes de arquivo de fonte em japonês incluem "Jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![Um exemplo dos pacotes de idiomas japoneses com a marca de idioma "Jpan" nos nomes de arquivo.](media/language-pack-example.png)

6. Defina as permissões no compartilhamento do repositório de conteúdo do idioma para ter acesso de leitura da VM que você usará para criar a imagem personalizada.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Criar uma imagem personalizada de várias sessões do Windows 10 Enterprise manualmente

Para criar uma imagem personalizada de várias sessões do Windows 10 Enterprise manualmente:

1. Implante uma VM do Azure, vá para a Galeria do Azure e selecione a versão atual do Windows 10 Enterprise de várias sessões que você está usando.
2. Depois de implantar a VM, conecte-se a ela usando o RDP como administrador local.
3. Verifique se sua VM tem todas as atualizações mais recentes do Windows. Baixe as atualizações e reinicie a VM, se necessário.
4. Conecte-se ao repositório de compartilhamento de arquivo do pacote de idiomas, do FOD e dos aplicativos da caixa de entrada e monte-os em uma unidade de letra (por exemplo, unidade E).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Criar uma imagem personalizada de várias sessões do Windows 10 Enterprise automaticamente

Se você preferir instalar idiomas por meio de um processo automatizado, poderá configurar um script no PowerShell. Você pode usar o exemplo de script a seguir para instalar os pacotes de idiomas espanhol (Espanha), francês (França) e chinês (RPC) e os pacotes satélites para o Windows 10 Enterprise de várias sessões, versão 2004. O script integra o pacote da interface de idiomas e todos os pacotes satélites necessários na imagem. No entanto, você também pode modificar esse script para instalar outros idiomas. Basta executar o script de uma sessão do PowerShell com privilégios elevados caso contrário, ele não funcionará.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

O script pode demorar um pouco dependendo do número de idiomas que você precisa instalar.

Após a conclusão da execução do script, verifique se os pacotes de idiomas foram instalados corretamente acessando **Iniciar** > **Configurações** > **Hora e Idioma** > **Idioma**. Se os arquivos de idioma estiverem lá, estará tudo pronto.

Depois de adicionar outros idiomas à imagem do Windows, os aplicativos da caixa de entrada também precisam ser atualizados para dar suporte aos idiomas adicionados. Para isso, atualize os aplicativos pré-instalados com o conteúdo do ISO dos aplicativos da caixa de entrada. Para executar essa atualização em um ambiente desconectado (sem acesso à Internet da VM possível), você pode usar a amostra de script do PowerShell a seguir para automatizar o processo.

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>Os aplicativos da caixa de entrada incluídos no ISO não são as versões mais recentes dos aplicativos do Windows pré-instalados. Para obter a versão mais recente de todos os aplicativos, você precisa atualizar os aplicativos usando o aplicativo da Windows Store e executar uma pesquisa manual de atualizações depois de instalar os idiomas adicionais.

Quando terminar, desconecte o compartilhamento.

## <a name="finish-customizing-your-image"></a>Concluir a personalização da imagem

Depois de instalar os pacotes de idiomas, você pode instalar qualquer outro software que deseja adicionar à imagem personalizada.

Quando terminar de personalizar a imagem, execute a ferramenta de preparação do sistema (sysprep).

Para executar a sysprep:

1. Abra um prompt de comandos com privilégios elevados e execute o seguinte comando para generalizar a imagem:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Pare a VM e capture-a em uma imagem gerenciada seguindo as instruções em [Criar uma imagem gerenciada de uma VM generalizada no Azure](../virtual-machines/windows/capture-image-resource.md).

3. Agora você pode usar a imagem personalizada para implantar um pool de host da Área de Trabalho Virtual do Windows. Para saber como implantar um pool de host, confira o [Tutorial: Criar um pool de host com o portal do Azure](create-host-pools-azure-marketplace.md).

## <a name="enable-languages-in-windows-settings-app"></a>Habilitar idiomas no aplicativo de configurações do Windows

Por fim, depois de implantar o pool de host, você precisará adicionar o idioma à lista de idiomas de cada usuário para que ele possa selecionar o idioma preferencial no menu de configurações.

Para garantir que os usuários possam selecionar os idiomas que você instalou, entre como o usuário e execute o cmdlet do PowerShell a seguir para adicionar os pacotes de idiomas instalados ao menu de idiomas. Você também pode configurar esse script como uma tarefa automatizada ou um script de logon que é ativado quando o usuário entra na sessão dele.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Depois que um usuário altera as configurações de idioma, ele precisará sair a sessão da Área de Trabalho Virtual do Windows e entrar novamente para que as alterações entrem em vigor. 

## <a name="next-steps"></a>Próximas etapas

Se você tiver curiosidade sobre problemas conhecidos para pacotes de idiomas, confira [Como adicionar pacotes de idiomas no Windows 10, versão 1803 e versões posteriores: problemas conhecidos](/windows-hardware/manufacture/desktop/language-packs-known-issue).

Se você tiver outras dúvidas sobre o Windows 10 Enterprise de várias sessões, confira nossas [perguntas frequentes](windows-10-multisession-faq.yml).
