---
title: Versão prévia do PowerShell do Windows Virtual Desktop MSIX app Attach – Azure
description: Como configurar o anexo de aplicativo do MSIX para a área de trabalho virtual do Windows usando o PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1196982fedc7321805e36cceed27c90e43a6e705
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558328"
---
# <a name="set-up-msix-app-attach-preview-using-powershell"></a>Configurar a anexação do aplicativo MSIX (versão prévia) usando o PowerShell

> [!IMPORTANT]
> A anexação do aplicativo MSIX está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Além do portal do Azure, você também pode configurar a anexação do aplicativo MSIX (visualização) manualmente com o PowerShell. Este artigo explicará como usar o PowerShell para configurar a anexação do aplicativo MSIX.

## <a name="requirements"></a>Requisitos

>[!IMPORTANT]
>Antes de começar, certifique-se de preencher e enviar [este formulário](https://aka.ms/enablemsixappattach) para habilitar a anexação do aplicativo MSIX em sua assinatura. Se você não tiver uma solicitação aprovada, o MSIX app Attach não funcionará. A aprovação de solicitações pode levar até 24 horas durante os dias úteis. Você receberá um email quando sua solicitação for aceita e concluída.

Veja o que você precisa para configurar o anexo do aplicativo MSIX:

- Uma implantação funcional da Área de Trabalho Virtual do Windows. Para saber como implantar a área de trabalho virtual do Windows (clássica), consulte [criar um locatário na área de trabalho virtual do Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Para saber como implantar a área de trabalho virtual do Windows com a integração do Azure Resource Manager, confira [criar um pool de hosts com o portal do Azure](./create-host-pools-azure-marketplace.md).
- Um pool de hosts da área de trabalho virtual do Windows com pelo menos um host de sessão ativo.
- Esse pool de hosts deve estar no ambiente de validação.
- Um grupo de aplicativos remotos da área de trabalho.
- A ferramenta de empacotamento MSIX.
- Um aplicativo MSIX expandido em uma imagem MSIX que é carregada em um compartilhamento de arquivos.
- Um compartilhamento de arquivos em sua implantação de área de trabalho virtual do Windows onde o pacote MSIX será armazenado.
- O compartilhamento de arquivos em que você carregou a imagem MSIX também deve ser acessível a todas as VMs (máquinas virtuais) no pool de hosts. Os usuários precisarão de permissões somente leitura para acessar a imagem.
- Baixe e instale o PowerShell Core.
- Baixe o módulo Azure PowerShell de visualização pública e expanda-o para uma pasta local.
- Instale o módulo do Azure executando o seguinte cmdlet:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Entrar no Azure e importar o módulo

Depois de ter todos os requisitos prontos, abra o PowerShell Core em um prompt de comando com privilégios elevados e execute este cmdlet:

```powershell
Connect-AzAccount
```

Depois de executá-lo, autentique sua conta usando suas credenciais. Nesse caso, você pode ser solicitado a fornecer uma URL de dispositivo ou um token.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Importar o módulo AZ. WindowsVirtualDesktop

Você precisará do módulo AZ. DesktopVirtualization para seguir as instruções neste artigo.

>[!NOTE]
>Para a visualização pública, forneceremos o módulo como arquivos ZIP separados que você deve importar manualmente.

Antes de começar, você pode executar o seguinte cmdlet para ver se o módulo AZ. DesktopVirtualization já está instalado em sua sessão ou VM:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Se a WAN for desinstalar uma cópia existente do módulo e começar novamente, execute este cmdlet:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Se o módulo estiver bloqueado em sua VM, execute este cmdlet para desbloqueá-lo:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Com essa limpeza fora do caminho, é hora de importar o módulo.

1. Execute o cmdlet a seguir e pressione a tecla **R** quando for solicitado a concordar em executar o código personalizado.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Depois de executar o cmdlet de importação, verifique se ele tem os cmdlets para MSIX executando o seguinte cmdlet:

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Se os cmdlets estiverem lá, a saída deverá ter a seguinte aparência:

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Se você não vir essa saída, feche todas as sessões do PowerShell e do PowerShell Core e tente novamente.

## <a name="set-up-helper-variables"></a>Configurar variáveis auxiliares

Depois de importar o módulo, você precisará configurar as variáveis auxiliares. Os exemplos a seguir mostrarão como fazer cada um deles.

Para obter sua ID de assinatura:

```powershell
Get-AzContext -ListAvailable | fl
```

Para selecionar o contexto de um locatário do Azure e uma assinatura com um nome:

```powershell
$obj = Select-AzContext -Name "<Name>"
```

Para definir a variável de assinatura:

```powershell
$subId = $obj.Subscription.Id
```

Para definir o nome do espaço de trabalho:

```powershell
$ws = "<WorksSpaceName>"
```

Para definir o nome do pool de hosts:

```powershell
$hp = "<HostPoolName>"
```

Para configurar o grupo de recursos em que as VMs host de sessão são configuradas:

```powershell
$rg = "<ResourceGroupName>"
```

E, finalmente, para confirmar que você definiu corretamente todas as variáveis:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>Adicionar um pacote MSIX a um pool de hosts

Depois de configurar tudo, é hora de adicionar o pacote MSIX a um pool de hosts. Para fazer isso, primeiro você precisará obter o caminho UNC para a imagem MSIX.

Usando o caminho UNC, execute este cmdlet para expandir a imagem MSIX:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

Execute este cmdlet para adicionar o pacote MSIX ao pool de hosts desejado:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

Depois de terminar, confirme se o pacote foi criado com este cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>Remover um pacote MSIX de um pool de hosts

Para remover um pacote de um pool de hosts:

Obtenha uma lista de todos os pacotes associados a um pool de hosts com esse cmdlet e localize o nome do pacote que deseja remover na saída:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

Como alternativa, você também pode obter um pacote específico com base em seu nome de exibição com este cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

Para remover o pacote, execute este cmdlet:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>Publicar aplicativos MSIX em um grupo de aplicativos

Você só poderá seguir as instruções nesta seção se tiver terminado de seguir as instruções nas seções anteriores. Se você tiver um pool de hosts com um host de sessão ativa, pelo menos um grupo de aplicativos de desktop e tiver adicionado um pacote MSIX ao pool de hosts, você estará pronto para começar.

Para publicar um aplicativo do pacote MSIX em um grupo de aplicativos, você precisará encontrar seu nome e, em seguida, usar esse nome no cmdlet de publicação.

Para publicar um aplicativo:

Execute este cmdlet para listar todos os grupos de aplicativos disponíveis:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

Quando você encontrar o nome do grupo de aplicativos no qual deseja publicar aplicativos, use seu nome neste cmdlet:

```powershell
$grName = "<AppGroupName>"
```

Por fim, você precisará publicar o aplicativo.

- Para publicar o aplicativo MSIX em um grupo de aplicativos de área de trabalho, execute este cmdlet:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Para publicar o aplicativo em um grupo de aplicativos remoto, execute este cmdlet:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Se um usuário for atribuído a um grupo de aplicativos remoto e a um grupo de aplicativos de área de trabalho no mesmo pool de hosts, quando o usuário se conectar à área de trabalho remota, eles verão os aplicativos MSIX de ambos os grupos.

## <a name="next-steps"></a>Próximas etapas

Pergunte a nossas perguntas da Comunidade sobre esse recurso na [área de trabalho virtual do Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Você também pode deixar comentários para a Área de Trabalho Virtual do Windows no [Hub de comentários da Área de Trabalho Virtual do Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Aqui estão alguns outros artigos que você pode achar úteis:

- [Glossário de anexação de aplicativo MSIX](app-attach-glossary.md)
- [Perguntas frequentes sobre anexação de aplicativo MSIX](app-attach-faq.md)
