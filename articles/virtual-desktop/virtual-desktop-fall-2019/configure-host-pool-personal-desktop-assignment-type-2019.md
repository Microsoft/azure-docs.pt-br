---
title: Tipo de atribuição de área de trabalho virtual do Windows (clássico)-Azure
description: Como configurar o tipo de atribuição para um pool de hosts pessoais da área de trabalho virtual do Windows (clássico).
author: Heidilohr
ms.topic: how-to
ms.date: 05/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 669010d2de90498c98fc685fe931b084a11cd104
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008501"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type-for-windows-virtual-desktop-classic"></a>Configurar o tipo de atribuição do pool de hosts de área de trabalho pessoal para o Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../configure-host-pool-personal-desktop-assignment-type.md).

Você pode configurar o tipo de atribuição de seu pool de hosts da área de trabalho pessoal para ajustar o ambiente da Área de Trabalho Virtual do Windows e atender melhor às suas necessidades. Neste tópico, mostraremos como configurar a atribuição automática ou direta para seus usuários.

>[!NOTE]
> As instruções deste artigo se aplicam somente a pools de hosts da área de trabalho pessoal, e não a pools de hosts em pool, já que os usuários em pools de hosts em pool não são atribuídos a hosts de sessão específicos.

## <a name="configure-automatic-assignment"></a>Configurar a atribuição automática

A atribuição automática é o tipo de atribuição padrão para novos pools de hosts da área de trabalho pessoal criados em seu ambiente da Área de Trabalho Virtual do Windows. A atribuição automática de usuários não exige um host de sessão específico.

Para atribuir usuários automaticamente, primeiro atribua-os ao pool de hosts da área de trabalho pessoal para que eles possam ver a área de trabalho no próprio feed. Quando um usuário atribuído iniciar a área de trabalho no feed, ele solicitará um host de sessão disponível, se ainda não tiver se conectado ao pool de hosts, o que concluirá o processo de atribuição.

Antes de começar, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/), caso ainda não tenha feito isso.

> [!NOTE]
> Verifique se você instalou a versão 1.0.1534.2001 ou posterior do módulo do PowerShell da Área de Trabalho Virtual do Windows antes de seguir estas instruções.

Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Para configurar um pool de hosts para atribuir usuários automaticamente a VMs, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Para atribuir um usuário ao pool de hosts da área de trabalho pessoal, execute o seguinte cmdlet do PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Configurar a atribuição direta

Ao contrário da atribuição automática, quando você usa a atribuição direta, deve atribuir o usuário ao pool de hosts da área de trabalho pessoal e a um host de sessão específico antes que ele possa se conectar à área de trabalho pessoal. Se o usuário for atribuído apenas a um pool de hosts sem uma atribuição de host de sessão, ele não conseguirá acessar os recursos.

Para configurar um pool de hosts para exigir a atribuição direta de usuários a hosts de sessão, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Para atribuir um usuário ao pool de hosts da área de trabalho pessoal, execute o seguinte cmdlet do PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Para atribuir um usuário a um host de sessão específico, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="remove-a-user-assignment"></a>Remover uma atribuição de usuário

Talvez você queira remover uma atribuição de usuário porque este não precisa mais da área de trabalho pessoal, porque saiu da empresa ou porque você deseja reutilizar a área de trabalho para outra pessoa.

No momento, a única maneira de remover a atribuição de usuário de uma área de trabalho pessoal é removendo completamente o host da sessão. Para fazer isso, execute este cmdlet:

```powershell
Remove-RdsSessionHost
```

Se precisar adicionar o host da sessão de volta ao pool de hosts da área de trabalho pessoal, desinstale a Área de Trabalho Virtual do Windows no respectivo computador e siga as etapas explicadas em [Criar um pool de hosts com o PowerShell](create-host-pools-powershell-2019.md) para registrar novamente o host da sessão.

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou o tipo de atribuição da área de trabalho pessoal, pode entrar em um cliente da Área de Trabalho Virtual do Windows para testá-lo como parte de uma sessão de usuário. Os dois tutoriais a seguir mostrarão como se conectar a uma sessão usando o cliente de sua escolha:

- [Conectar-se ao cliente da Área de Trabalho do Windows](connect-windows-7-10-2019.md)
- [Conectar-se ao cliente Web](connect-web-2019.md)
