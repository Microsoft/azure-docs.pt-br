---
title: Usar o PowerShell para habilitar a Área de Trabalho Remota para uma função
description: Como configurar seu aplicativo de serviço de nuvem do Azure usando o PowerShell para permitir conexões de área de trabalho remota
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 5b1650edb575de8fd59ad2495dafcd628a717c02
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610392"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-powershell"></a>Habilitar Conexão de Área de Trabalho Remota para uma função nos serviços de nuvem do Azure (clássico) usando o PowerShell

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

A área de trabalho remota permite que você acesse a área de trabalho de uma função em execução no Azure. Você pode usar a conexão da área de trabalho remota para solucionar e diagnosticar problemas com seu aplicativo durante a execução.

Este artigo descreve como habilitar a área de trabalho remota em suas funções de serviço de nuvem usando o PowerShell. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/) para os pré-requisitos necessários para este artigo. O PowerShell usa a Extensão da Área de Trabalho Remota para que você possa habilitar a Área de Trabalho Remota depois que o aplicativo for implantado.

## <a name="configure-remote-desktop-from-powershell"></a>Configurar a Área de Trabalho Remota por meio do PowerShell
O cmdlet [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) permite habilitar a Área de Trabalho Remota em funções especificadas ou todas as funções da implantação do serviço de nuvem. O cmdlet permite que você especifique o nome de usuário e a senha para o usuário da área de trabalho remota por meio do parâmetro *Credential* , que aceita um objeto PSCredential.

Se estiver usando o PowerShell interativamente, você pode definir facilmente o objeto PSCredential chamando o cmdlet [Get-Credentials](/powershell/module/microsoft.powershell.security/get-credential) .

```powershell
$remoteusercredentials = Get-Credential
```

Esse comando exibirá uma caixa de diálogo, permitindo que você insira o nome de usuário e a senha para o usuário remoto de modo seguro.

Já que o PowerShell ajuda em cenários de automação, você também pode configurar o objeto **PSCredential** de modo que não exija interação do usuário. Primeiro, você precisa configurar uma senha de segurança. Comece com a especificação de uma senha de texto sem formatação e converta-a em uma cadeia de caracteres segura usando [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring). Em seguida, você precisa converter essa cadeia de caracteres segura em uma cadeia de caracteres criptografada padrão usando [ConvertFrom-SecureString](/powershell/module/microsoft.powershell.security/convertfrom-securestring). Agora você pode salvar essa cadeia de caracteres criptografada padrão em um arquivo, usando [Set-Content](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176959(v=technet.10)).

Você também pode criar um arquivo de senha segura para que não precise digitar a senha em todas as ocasiões. Além disso, um arquivo de senha segura é melhor do que um arquivo de texto sem formatação. Use o PowerShell a seguir para criar um arquivo de senha de segurança:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Ao definir a senha, atenda aos [requisitos de complexidade](/previous-versions/windows/it-pro/windows-server-2003/cc786468(v=ws.10)).

Para criar o objeto de credencial com base no arquivo de senha segura, você deve ler os conteúdos do arquivo e convertê-los novamente em uma cadeia de caracteres segura, usando [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring).

O cmdlet [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) também aceita um parâmetro *Expiration* que especifica um **DateTime** (data e hora) em que a conta de usuário vai expirar. Por exemplo, você pode definir a conta a expirar em alguns dias após a data e hora atuais.

Este PowerShell de exemplo mostra como definir a Extensão de Área de Trabalho Remota em um serviço de nuvem:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Você também pode especificar o slot de implantação e as funções em que deseja habilitar a área de trabalho remota. Se esses parâmetros não forem especificados, o cmdlet habilitará a área de trabalho remota em todas as funções no slot de implantação de **Produção** .

A extensão de Área de Trabalho Remota está associada uma implantação. Se você criar uma nova implantação para o serviço, precisará habilitar novamente a área de trabalho remota nessa implantação. Se você sempre quiser ter a área de trabalho remota habilitada em suas implantações, deverá considerar a integração dos scripts do PowerShell em seu fluxo de trabalho de implantação.

## <a name="remote-desktop-into-a-role-instance"></a>Área de Trabalho Remota em uma instância de função

O cmdlet [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) é usado para a área de trabalho remota em uma instância de função específica do serviço de nuvem. Você pode usar o parâmetro *LocalPath* para baixar o arquivo RDP localmente. Ou você pode usar o parâmetro *Launch* para iniciar diretamente a caixa de diálogo Conexão de Área de Trabalho Remota para acessar a instância de função do serviço de nuvem.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Verifique se a extensão de Área de Trabalho Remota está habilitada em um serviço

O cmdlet [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) exibe se a área de trabalho remota está habilitada ou desabilitada em uma implantação de serviço. O cmdlet retorna o nome de usuário para o usuário de área de trabalho remota e as funções nas quais a extensão de área de trabalho remota está habilitada. Por padrão, isso ocorre no slot de implantação e você pode optar por usar o slot de preparo em vez disso.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Remover a extensão de Área de Trabalho Remota de um serviço

Se você já tiver habilitado a extensão de área de trabalho remota em uma implantação e se precisar atualizar as configurações de área de trabalho remota, primeiro remova a extensão. E habilite-o novamente com as novas configurações. Por exemplo, se você deseja definir uma nova senha para a conta de usuário remoto ou se a conta tiver expirado. Isso é necessário em implantações existentes com a extensão de área de trabalho remota habilitada. Para as novas implantações, você pode simplesmente aplicar a extensão de forma direta.

Para remover a extensão de área de trabalho remota de uma implantação, você poderá usar o cmdlet [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/remove-azureserviceremotedesktopextension) . Você também pode especificar o slot de implantação e a função dos quais você deseja remover a extensão da área de trabalho remota.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Para remover completamente a configuração de extensão, você deve chamar o cmdlet *remove* com o parâmetro **UninstallConfiguration** .
>
> O parâmetro **UninstallConfiguration** desinstala qualquer configuração de extensão aplicada ao serviço. Todas as configurações de extensão estão associadas à configuração do serviço. Chamar o cmdlet *remove* sem **UninstallConfiguration** dissocia a <mark>implantação</mark> da configuração de extensão, removendo assim efetivamente a extensão. No entanto, a configuração de extensão permanece associada ao serviço.

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar serviços de nuvem](cloud-services-how-to-configure-portal.md)