---
title: Personalizar as propriedades RDP com o PowerShell - Azure
description: Como personalizar o RDP propriedades para Windows Desktop Virtual com os cmdlets do PowerShell.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: v-hevem
ms.openlocfilehash: ce14f990272fa1e70d07c0f4a1f18025b536eccc
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618876"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalizar as propriedades de protocolo de área de trabalho remota para um pool de host

Personalizando as propriedades de protocolo de área de trabalho remota (RDP) de um pool host, como a experiência de vários monitor e redirecionamento de áudio, permite que você fornecer uma experiência ideal para seus usuários com base em suas necessidades. Você pode personalizar as propriedades RDP na área de trabalho Virtual do Windows usando o **- CustomRdpProperty** parâmetro na **RdsHostPool conjunto** cmdlet.

Ver [configurações do arquivo RDP da área de trabalho remota](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) para obter uma lista completa das propriedades com suporte e seus valores padrão.

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Adicionar ou editar uma única propriedade personalizada de RDP

Para adicionar ou editar uma única propriedade personalizada de RDP, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![Uma captura de tela do cmdlet do PowerShell Get-RDSRemoteApp com o nome e FriendlyName realçado.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Adicionar ou editar várias propriedades personalizadas de RDP

Para adicionar ou editar várias propriedades personalizadas de RDP, execute os seguintes cmdlets do PowerShell, fornecendo as propriedades personalizadas de RDP como uma cadeia de caracteres separada por vírgulas:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![Uma captura de tela do cmdlet do PowerShell Get-RDSRemoteApp com o nome e FriendlyName realçado.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Redefinir todas as propriedades personalizadas de RDP

Você pode redefinir as propriedades personalizadas de RDP individuais para seus valores padrão, seguindo as instruções em [adicionar ou editar uma única propriedade personalizada de RDP](#add-or-edit-a-single-custom-rdp-property), ou você pode redefinir todas as propriedades personalizadas de RDP para um pool de host executando o seguinte Cmdlet do PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![Uma captura de tela do cmdlet do PowerShell Get-RDSRemoteApp com o nome e FriendlyName realçado.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você personalizou as propriedades RDP para um pool de determinado host, você pode entrar um cliente de área de trabalho Virtual do Windows para testá-los como parte de uma sessão de usuário. Para fazer isso, continue a conectar-se a instruções de área de trabalho Virtual do Windows:

- [Conectar-se do Windows 10 e Windows 7](connect-windows-7-and-10.md)
- [Conectar-se de um navegador da Web](connect-web.md)
