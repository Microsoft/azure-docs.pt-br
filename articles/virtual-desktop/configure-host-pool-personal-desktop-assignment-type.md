---
title: Tipo de atribuição da área de trabalho pessoal da Área de Trabalho Virtual do Windows – Azure
description: Como configurar a atribuição automática ou direta para um pool de hosts da área de trabalho pessoal do Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 07/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 446dae3c064f5f23d35cb12b2b24bdfea9e27012
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88007804"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configurar o tipo de atribuição do pool de hosts da área de trabalho pessoal

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica) sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).

Você pode configurar o tipo de atribuição de seu pool de hosts da área de trabalho pessoal para ajustar o ambiente da Área de Trabalho Virtual do Windows e atender melhor às suas necessidades. Neste tópico, mostraremos como configurar a atribuição automática ou direta para seus usuários.

>[!NOTE]
> As instruções deste artigo se aplicam somente a pools de hosts da área de trabalho pessoal, e não a pools de hosts em pool, já que os usuários em pools de hosts em pool não são atribuídos a hosts de sessão específicos.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já baixou e instalou o módulo do PowerShell de área de trabalho virtual do Windows. Se você ainda não fez isso, siga as instruções em [Configurar o módulo do PowerShell](powershell-module.md).

## <a name="configure-automatic-assignment"></a>Configurar a atribuição automática

A atribuição automática é o tipo de atribuição padrão para novos pools de hosts da área de trabalho pessoal criados em seu ambiente da Área de Trabalho Virtual do Windows. A atribuição automática de usuários não exige um host de sessão específico.

Para atribuir usuários automaticamente, primeiro atribua-os ao pool de hosts da área de trabalho pessoal para que eles possam ver a área de trabalho no próprio feed. Quando um usuário atribuído iniciar a área de trabalho no feed, ele solicitará um host de sessão disponível, se ainda não tiver se conectado ao pool de hosts, o que concluirá o processo de atribuição.

Para configurar um pool de hosts para atribuir usuários automaticamente a VMs, execute o seguinte cmdlet do PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

Para atribuir um usuário ao pool de hosts da área de trabalho pessoal, execute o seguinte cmdlet do PowerShell:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Configurar a atribuição direta

Ao contrário da atribuição automática, quando você usa a atribuição direta, deve atribuir o usuário ao pool de hosts da área de trabalho pessoal e a um host de sessão específico antes que ele possa se conectar à área de trabalho pessoal. Se o usuário for atribuído apenas a um pool de hosts sem uma atribuição de host de sessão, ele não conseguirá acessar os recursos.

Para configurar um pool de hosts para exigir a atribuição direta de usuários a hosts de sessão, execute o seguinte cmdlet do PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

Para atribuir um usuário ao pool de hosts da área de trabalho pessoal, execute o seguinte cmdlet do PowerShell:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Para atribuir um usuário a um host de sessão específico, execute o seguinte cmdlet do PowerShell:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

Para atribuir diretamente um usuário a um host de sessão no portal do Azure:

1. Entre no Portal do Azure em <https://portal.azure.com>.
2. Insira **área de trabalho virtual do Windows** na barra de pesquisa.
3. Em **Serviços**, selecione **área de trabalho virtual do Windows**.
4. Na página área de trabalho virtual do Windows, acesse o menu no lado esquerdo da janela e selecione **pools de hosts**.
5. Selecione o nome do pool de hosts que você deseja atualizar.
6. Em seguida, vá para o menu no lado esquerdo da janela e selecione **grupos de aplicativos**.
7. Selecione o nome do grupo de aplicativos da área de trabalho que você deseja editar e, em seguida, selecione **atribuições** no menu no lado esquerdo da janela.
8. Selecione **+ Adicionar**e, em seguida, selecione os usuários ou grupos de usuários para os quais você deseja publicar esse grupo de aplicativos de desktop.
9. Selecione **atribuir VM** na barra de informações para atribuir um host de sessão a um usuário.
10. Selecione o host de sessão que você deseja atribuir ao usuário e, em seguida, selecione **atribuir**.
11. Selecione o usuário ao qual você deseja atribuir o host de sessão da lista de usuários disponíveis.
12. Quando terminar, selecione **selecionar**.

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou o tipo de atribuição da área de trabalho pessoal, pode entrar em um cliente da Área de Trabalho Virtual do Windows para testá-lo como parte de uma sessão de usuário. Os dois tutoriais a seguir mostrarão como se conectar a uma sessão usando o cliente de sua escolha:

- [Conectar-se ao cliente da Área de Trabalho do Windows](connect-windows-7-10.md)
- [Conectar-se ao cliente Web](connect-web.md)
- [Conectar-se ao cliente Android](connect-android.md)
- [Conectar-se ao cliente iOS](connect-ios.md)
- [Conectar-se ao cliente macOS](connect-macos.md)