---
title: Personalizar feed para usuários da área de trabalho virtual do Windows-Azure
description: Como personalizar o feed para usuários da área de trabalho virtual do Windows com cmdlets do PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 09/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e47486f29537cb948aaae7cf17e97bae14b60700
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90084287"
---
# <a name="customize-the-feed-for-windows-virtual-desktop-users"></a>Personalizar o feed para usuários da área de trabalho virtual do Windows

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md).

Você pode personalizar o feed para que os recursos do RemoteApp e da área de trabalho remota apareçam de maneira reconhecível para seus usuários.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já baixou e instalou o módulo do PowerShell de área de trabalho virtual do Windows. Se você ainda não fez isso, siga as instruções em [Configurar o módulo do PowerShell](powershell-module.md).

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalizar o nome de exibição para um RemoteApp

Você pode alterar o nome de exibição de um RemoteApp publicado definindo o nome amigável. Por padrão, o nome amigável é igual ao nome do programa RemoteApp.

Para recuperar uma lista de RemoteApps publicados para um grupo de aplicativos, execute o seguinte cmdlet do PowerShell:

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

Para atribuir um nome amigável a um RemoteApp, execute o seguinte cmdlet com os parâmetros necessários:

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

Por exemplo, digamos que você recuperou os aplicativos atuais com o seguinte cmdlet de exemplo:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

A saída ficaria assim:

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
Para atualizar o nome amigável, execute este cmdlet:

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

Para confirmar que você atualizou com êxito o nome amigável, execute este cmdlet:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

O cmdlet deve fornecer a seguinte saída:

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalizar o nome de exibição para um Área de Trabalho Remota

Você pode alterar o nome de exibição de uma área de trabalho remota publicada definindo um nome amigável. Se você criou manualmente um pool de hosts e um grupo de aplicativos da área de trabalho por meio do PowerShell, o nome amigável padrão é "área de trabalho da sessão". Se você criou um pool de hosts e um grupo de aplicativos de área de trabalho por meio do modelo de Azure Resource Manager do GitHub ou da oferta do Azure Marketplace, o nome amigável padrão é o mesmo que o nome do pool de hosts.

Para recuperar o recurso de área de trabalho remota, execute o seguinte cmdlet do PowerShell:

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

Para atribuir um nome amigável ao recurso de área de trabalho remota, execute o seguinte cmdlet do PowerShell:

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Personalizar um nome de exibição no portal do Azure

Você pode alterar o nome de exibição de uma área de trabalho remota publicada definindo um nome amigável usando o portal do Azure.

1. Entre no Portal do Azure em <https://portal.azure.com>.

2. Pesquise pela **área de trabalho virtual do Windows**.

3. Em serviços, selecione **área de trabalho virtual do Windows**.

4. Na página área de trabalho virtual do Windows, selecione **grupos de aplicativos** no lado esquerdo da tela e selecione o nome do grupo de aplicativos que deseja editar. (Por exemplo, se você quiser editar o nome de exibição do grupo de aplicativos da área de trabalho, selecione o grupo de aplicativos chamado **área de trabalho**.)

5. Selecione **aplicativos** no menu no lado esquerdo da tela.

6. Selecione o aplicativo que você deseja atualizar e insira um novo **nome de exibição**.

7. Selecione **Salvar**. O aplicativo que você editou agora deve exibir o nome atualizado.

## <a name="next-steps"></a>Próximas etapas

Agora que você personalizou o feed para os usuários, você pode entrar em um cliente de área de trabalho virtual do Windows para testá-lo. Para fazer isso, vá para o How-tos da área de trabalho virtual do Windows:

 * [Conectar-se com o Windows 10 ou o Windows 7](connect-windows-7-10.md)
 * [Conectar-se ao cliente Web](connect-web.md)
 * [Conectar-se ao cliente Android](connect-android.md)
 * [Conectar-se ao cliente iOS](connect-ios.md)
 * [Conectar-se ao cliente macOS](connect-macos.md)
