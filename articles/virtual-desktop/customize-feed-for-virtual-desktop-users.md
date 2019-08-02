---
title: Personalizar o feed para os usuários da área de trabalho Virtual do Windows - Azure
description: Como personalizar o feed para usuários de área de trabalho Virtual do Windows com os cmdlets do PowerShell.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: v-hevem
ms.openlocfilehash: 5fe2a8b8ee5870ff7986ca2d91739f82a5128882
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618968"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Personalizar o feed para usuários da Área de Trabalho Virtual do Windows

Portanto, o RemoteApp e os recursos de área de trabalho remotos é exibido de forma reconhecível para seus usuários, você pode personalizar o feed.

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso.

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalizar o nome de exibição para um RemoteApp

Você pode alterar o nome de exibição para um RemoteApp publicado, definindo o nome amigável. Por padrão, o nome amigável é igual ao nome do programa RemoteApp.

Para recuperar uma lista de RemoteApps publicado para um grupo de aplicativos, execute o seguinte cmdlet do PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Uma captura de tela do cmdlet do PowerShell Get-RDSRemoteApp com o nome e FriendlyName realçado.](media/get-rdsremoteapp.png)

Para atribuir um nome amigável para um RemoteApp, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Uma captura de tela do cmdlet do PowerShell Set-RDSRemoteApp com o nome e o novo FriendlyName realçado.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalizar o nome de exibição para uma área de trabalho remota

Você pode alterar o nome de exibição para uma área de trabalho remota publicado, definindo um nome amigável. Se você criou manualmente um host do pool e o grupo de aplicativos da área de trabalho por meio do PowerShell, o nome amigável padrão é "Área de trabalho de sessão." Se você criou um host do pool e o grupo de aplicativos da área de trabalho por meio do modelo do GitHub do Azure Resource Manager ou a oferta do Azure Marketplace, o nome amigável padrão é o mesmo que o nome do pool de host.

Para recuperar o recurso de área de trabalho remoto, execute o seguinte cmdlet do PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Uma captura de tela do cmdlet do PowerShell Get-RDSRemoteApp com o nome e FriendlyName realçado.](media/get-rdsremotedesktop.png)

Para atribuir um nome amigável para o recurso de área de trabalho remoto, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Uma captura de tela do cmdlet do PowerShell Set-RDSRemoteApp com o nome e o novo FriendlyName realçado.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você personalizou o feed para os usuários, você pode entrar um cliente de área de trabalho Virtual do Windows para testá-lo. Para fazer isso, continue a conectar-se a instruções de área de trabalho Virtual do Windows:
    
 * [Conectar-se do Windows 10 ou Windows 7](connect-windows-7-and-10.md)
 * [Conectar-se de um navegador da Web](connect-web.md) 
