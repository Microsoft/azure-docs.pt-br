---
title: Personalizar feed para usuários da área de trabalho virtual do Windows (clássico)-Azure
description: Como personalizar o feed para usuários da área de trabalho virtual do Windows (clássico) com cmdlets do PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd7496690ec88fbe4297386c32d1b8a2c3234577
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540754"
---
# <a name="customize-feed-for-windows-virtual-desktop-classic-users"></a>Personalizar feed para usuários da área de trabalho virtual do Windows (clássico)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../customize-feed-for-virtual-desktop-users.md).

Você pode personalizar o feed para que os recursos do RemoteApp e da área de trabalho remota apareçam de maneira reconhecível para seus usuários.

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalizar o nome de exibição para um RemoteApp

Você pode alterar o nome de exibição de um RemoteApp publicado definindo o nome amigável. Por padrão, o nome amigável é igual ao nome do programa RemoteApp.

Para recuperar uma lista de RemoteApps publicados para um grupo de aplicativos, execute o seguinte cmdlet do PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Uma captura de tela do cmdlet do PowerShell Get-RDSRemoteApp com Name e FriendlyName realçado para personalizar o nome de exibição.](../media/get-rdsremoteapp.png)

Para atribuir um nome amigável a um RemoteApp, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Uma captura de tela do cmdlet do PowerShell Set-RDSRemoteApp com nome e novo FriendlyName realçados para personalizar o nome de exibição.](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalizar o nome de exibição para um Área de Trabalho Remota

Você pode alterar o nome de exibição de uma área de trabalho remota publicada definindo um nome amigável. Se você criou manualmente um pool de hosts e um grupo de aplicativos da área de trabalho por meio do PowerShell, o nome amigável padrão é "área de trabalho da sessão". Se você criou um pool de hosts e um grupo de aplicativos de área de trabalho por meio do modelo de Azure Resource Manager do GitHub ou da oferta do Azure Marketplace, o nome amigável padrão é o mesmo que o nome do pool de hosts.

Para recuperar o recurso de área de trabalho remota, execute o seguinte cmdlet do PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Uma captura de tela do cmdlet do PowerShell Get-RDSRemoteApp com Name e FriendlyName realçado.](../media/get-rdsremotedesktop.png)

Para atribuir um nome amigável ao recurso de área de trabalho remota, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Uma captura de tela do cmdlet do PowerShell Set-RDSRemoteApp com nome e novo FriendlyName realçado.](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você personalizou o feed para os usuários, você pode entrar em um cliente de área de trabalho virtual do Windows para testá-lo. Para fazer isso, vá para o How-tos da área de trabalho virtual do Windows:

 * [Conectar-se do Windows 10 ou Windows 7](connect-windows-7-10-2019.md)
 * [Conectar-se de um navegador da Web](connect-web-2019.md)
