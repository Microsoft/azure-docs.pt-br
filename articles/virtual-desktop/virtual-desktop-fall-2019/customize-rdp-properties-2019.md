---
title: Personalizar propriedades de RDP com a área de trabalho virtual do Windows do PowerShell (clássico)-Azure
description: Como personalizar as propriedades de RDP para a área de trabalho virtual do Windows (clássico) com cmdlets do PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5110e97e52939ea2115bb839768cc7ab96802961
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020701"
---
# <a name="customize-remote-desktop-protocol-properties-for-a--windows-virtual-desktop-classic-host-pool"></a>Personalizar propriedades de protocolo RDP para um pool de hosts da área de trabalho virtual do Windows (clássico)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../customize-rdp-properties.md).

Personalizar as propriedades de protocolo RDP (RDP) de um pool de hosts, como experiência de vários monitores e redirecionamento de áudio, permite que você forneça uma experiência ideal para seus usuários com base em suas necessidades. Você pode personalizar as propriedades de RDP na área de trabalho virtual do Windows usando o parâmetro **-CustomRdpProperty** no cmdlet **set-RdsHostPool** .

Consulte [configurações de arquivo RDP com suporte](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) para obter uma lista completa das propriedades com suporte e seus valores padrão.

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Propriedades padrão de RDP

Por padrão, os arquivos RDP publicados contêm as seguintes propriedades:

|Propriedades de RDP | Desktops | RemoteApps |
|---|---| --- |
| Modo de vários monitores | habilitado | N/D |
| Redirecionamentos de unidade habilitados | Unidades, área de transferência, impressoras, portas COM, dispositivos USB e cartões inteligentes| Unidades, área de transferência e impressoras |
| Modo de áudio remoto | Reproduzir localmente | Reproduzir localmente |

Todas as propriedades personalizadas que você definir para o pool de hosts substituirão esses padrões.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Adicionar ou editar uma única propriedade RDP personalizada

Para adicionar ou editar uma única propriedade RDP personalizada, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![Uma captura de tela do cmdlet do PowerShell Get-RDSRemoteApp com Name e FriendlyName realçado para editar uma propriedade personalizada do R D P.](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Adicionar ou editar várias propriedades personalizadas de RDP

Para adicionar ou editar várias propriedades de RDP personalizadas, execute os seguintes cmdlets do PowerShell fornecendo as propriedades de RDP personalizadas como uma cadeia de caracteres separada por ponto-e-vírgula:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![Uma captura de tela do cmdlet do PowerShell Set-RDSRemoteApp com Name e FriendlyName realçado para editar uma propriedade personalizada do R D P.](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Redefinir todas as propriedades de RDP personalizadas

Você pode redefinir propriedades individuais de RDP personalizadas para seus valores padrão seguindo as instruções em [Adicionar ou editar uma única propriedade RDP personalizada](#add-or-edit-a-single-custom-rdp-property), ou você pode redefinir todas as propriedades RDP personalizadas para um pool de hosts executando o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![Uma captura de tela do cmdlet do PowerShell Get-RDSRemoteApp com Name e FriendlyName realçado.](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você personalizou as propriedades de RDP para um determinado pool de hosts, você pode entrar em um cliente de área de trabalho virtual do Windows para testá-los como parte de uma sessão de usuário. Os dois tutoriais a seguir mostrarão como se conectar a uma sessão usando o cliente de sua escolha:

- [Conectar-se ao cliente da Área de Trabalho do Windows](connect-windows-7-10-2019.md)
- [Conectar-se ao cliente Web](connect-web-2019.md)