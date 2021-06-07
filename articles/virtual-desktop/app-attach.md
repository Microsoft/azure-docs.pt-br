---
title: Configurar scripts do PowerShell do Windows Virtual Desktop MSIX app Attach – Azure
description: Como criar scripts do PowerShell para o MSIX app attach para a área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5e45c51735e0b7ab4b263d3f3047b5848c82439d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185760"
---
# <a name="create-powershell-scripts-for-msix-app-attach-preview"></a>Criar scripts do PowerShell para anexação do aplicativo MSIX (versão prévia)

> [!IMPORTANT]
> A anexação do aplicativo MSIX está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tópico explicará como configurar scripts do PowerShell para anexação do aplicativo MSIX.

>[!IMPORTANT]
>Antes de começar, certifique-se de preencher e enviar [este formulário](https://aka.ms/enablemsixappattach) para habilitar a anexação do aplicativo MSIX em sua assinatura. Se você não tiver uma solicitação aprovada, o MSIX app Attach não funcionará. A aprovação de solicitações pode levar até 24 horas durante os dias úteis. Você receberá um email quando sua solicitação for aceita e concluída.

## <a name="install-certificates"></a>Instalar certificados

Você deve instalar certificados em todos os hosts de sessão no pool de hosts que hospedarão os APS de seus pacotes de anexação de aplicativo MSIX.

Se seu aplicativo usa um certificado que não é confiável para o público ou foi autoassinado, veja como instalá-lo:

1. Clique com o botão direito do mouse no pacote e selecione **Propriedades**.
2. Na janela exibida, selecione a guia **Assinaturas digitais**. Deve haver apenas um item na lista na guia, conforme mostrado na imagem a seguir. Selecione esse item para realçar o item e, em seguida, selecione **Detalhes**.
3. Quando a janela detalhes da assinatura digital for exibida, selecione a guia **geral** , selecione **Exibir certificado** e, em seguida, selecione **Instalar certificado**.
4. Quando o instalador for aberto, selecione **Computador local** como o local de armazenamento e, em seguida, selecione **Avançar**.
5. Se o instalador perguntar se você deseja permitir que o aplicativo faça alterações no seu dispositivo, selecione **Sim**.
6. Selecione **Colocar todos os certificados no seguinte repositório** e, em seguida, selecione **Procurar**.
7. Quando a janela Selecionar repositório de certificados for exibida, selecione **Pessoas confiáveis** e, em seguida, selecione **OK**.
8. Selecione **Avançar** e **concluir**.

## <a name="enable-microsoft-hyper-v"></a>Habilitar Microsoft Hyper-V

Microsoft Hyper-V deve ser habilitado porque o `Mount-VHD` comando é necessário para preparar e `Dismount-VHD` é necessário para desprepará-lo.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>Essa alteração exigirá que você reinicie a máquina virtual.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Preparar scripts do PowerShell para anexação do aplicativo MSIX

A anexação do aplicativo MSIX tem quatro fases distintas que devem ser executadas na seguinte ordem:

1. Estágio
2. Registrar
3. Cancelar registro
4. Cancelar preparo

Cada fase cria um script do PowerShell. Os scripts de exemplo para cada fase estão disponíveis [aqui](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-powershell-script"></a>Preparar script do PowerShell

Antes de atualizar os scripts do PowerShell, verifique se você tem o GUID do volume no VHD. Para obter o GUID do volume:

1.  Abra o compartilhamento de rede em que o VHD está localizado dentro da VM em que você executará o script.

2.  Clique com o botão direito no VHD e selecione **Montar**. Isso montará o VHD em uma letra da unidade.

3.  Depois de montar o VHD, a janela **Explorador de Arquivos** será aberta. Capturar a pasta pai e atualizar a variável **$parentFolder**

    >[!NOTE]
    >Se você não vir uma pasta pai, isso significará que o MSIX não foi expandido corretamente. Refaça a seção anterior e tente novamente.

4.  Abra a pasta pai. Se tiver sido expandido corretamente, você verá uma pasta com o mesmo nome que o pacote. Atualize a variável **$packageName** para corresponder ao nome desta pasta.

    Por exemplo, `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Abra um prompt de comando e digite **mountvol**. Esse comando exibirá uma lista de volumes e seus GUIDs. Copie o GUID do volume em que a letra da unidade corresponde à unidade na qual você montou o VHD na etapa 2.

    Por exemplo, neste exemplo de saída para o comando mountvol, se você montou o VHD para a unidade C, copie o valor acima `C:\`:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Atualize a variável **$volumeGuid** com o GUID do volume que você acabou de copiar.

7. Abra um prompt de administrador do PowerShell e atualize o seguinte script do PowerShell com as variáveis que se aplicam ao seu ambiente.

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>Registrar o script do PowerShell

Para executar o script de registro, execute os seguintes cmdlets do PowerShell com os valores de espaço reservado substituídos pelos valores que se aplicam ao seu ambiente.

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>Cancelar o registro do script do PowerShell

Para esse script, substitua o espaço reservado para **$packageName** pelo nome do pacote que você está testando.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>Cancelar o preparo do script do PowerShell

Para esse script, substitua o espaço reservado para **$packageName** pelo nome do pacote que você está testando. Em uma implantação de produção, seria melhor executar isso ao desligar.

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Configurar scripts de simulação para o agente de anexação do aplicativo MSIX

Depois de criar os scripts, os usuários podem executá-los manualmente ou configurá-los para serem executados automaticamente como scripts de inicialização, logon, logoff e desligamento. Para saber mais sobre esses tipos de scripts, confira [Como usar scripts de inicialização, desligamento, logon e logoff na Política de Grupo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Cada um desses scripts automáticos executa uma fase dos scripts de anexação do aplicativo:

- O script de inicialização executa o script de preparo.
- O script de logon executa o script de registro.
- O script de logoff executa o script de cancelamento de registro.
- O script de desligamento executa o script de cancelamento do preparo.

## <a name="use-packages-offline"></a>Usar pacotes offline

Se estiver usando pacotes do [Microsoft Store para Empresas](https://businessstore.microsoft.com/) ou [Microsoft Store para Educação](https://educationstore.microsoft.com/) em sua rede ou em dispositivos que não estejam conectados à Internet, você precisará obter as licenças de pacote da Microsoft Store e instalá-las no dispositivo para executar o aplicativo com êxito. Se o dispositivo estiver online e puder se conectar ao Microsoft Store para Empresas, as licenças necessárias deverão ser baixadas automaticamente, mas se você estiver offline, precisará configurar as licenças manualmente.

Para instalar os arquivos de licença, você precisará usar um script do PowerShell que chama a classe MDM_EnterpriseModernAppManagement_StoreLicenses02_01 no Provedor de Ponte WMI.

Veja como configurar as licenças para uso offline:

1. Baixe o pacote do aplicativo, as licenças e as estruturas necessárias do Microsoft Store para Empresas. Você precisa dos arquivos de licença codificados e não codificados. As instruções detalhadas de download são encontradas [aqui](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Atualize as seguintes variáveis no script para a etapa 3:
      1. `$contentID` é o valor ContentID do arquivo de licença não codificado (.xml). Você pode abrir o arquivo de licença em um editor de texto de sua escolha.
      2. `$licenseBlob` é a cadeia de caracteres inteira para o blob de licença no arquivo de licença codificado (.bin). Você pode abrir o arquivo de licença codificado em um editor de texto de sua escolha.
3. Execute o script a seguir em um prompt de administrador do PowerShell. Um bom local para executar a instalação da licença é no final do [script de preparo](#stage-powershell-script) que também precisa ser executado em um prompt de administrador.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>Próximas etapas

Atualmente, não há suporte para esse recurso, mas você pode fazer perguntas para a Comunidade na [TechCommunity da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Você também pode deixar comentários para a Área de Trabalho Virtual do Windows no [Hub de comentários da Área de Trabalho Virtual do Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
