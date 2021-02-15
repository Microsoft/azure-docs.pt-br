---
title: Personalizar propriedades RDP com o PowerShell – Azure
description: Como personalizar as propriedades de RDP para a área de trabalho virtual do Windows com cmdlets do PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cc3a08f383368b189e41bebd204707f2483e77c0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018300"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>Personalizar as propriedades de protocolo RDP (RDP) para um pool de hosts

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica) sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md).

Personalizar as propriedades de protocolo RDP (RDP) de um pool de hosts, como experiência de vários monitores e redirecionamento de áudio, permite que você forneça uma experiência ideal para seus usuários com base em suas necessidades. Você pode personalizar as propriedades de RDP na área de trabalho virtual do Windows usando o portal do Azure ou usando o parâmetro *-CustomRdpProperty* no cmdlet **Update-AzWvdHostPool** .

Consulte [configurações de arquivo RDP com suporte](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) para obter uma lista completa das propriedades com suporte e seus valores padrão.

## <a name="default-rdp-file-properties"></a>Propriedades do arquivo RDP padrão

Os arquivos RDP têm as seguintes propriedades por padrão:

|Propriedade RDP|Na área de trabalho|Como um RemoteApp|
|---|---|---|
|Modo de vários monitores|Desabilitado|habilitado|
|Redirecionamentos de unidade habilitados|Unidades, área de transferência, impressoras, portas COM e cartões inteligentes|Unidades, área de transferência e impressoras|
|Modo de áudio remoto|Reproduzir localmente|Reproduzir localmente|

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, siga as instruções em [Configurar o módulo do PowerShell da Área de Trabalho Virtual do Windows](powershell-module.md) para configurar o módulo do PowerShell e entrar no Azure.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Configurar propriedades de RDP no portal do Azure

Para configurar as propriedades de RDP no portal do Azure:

1. Entre no Azure em <https://portal.azure.com>.
2. Insira **área de trabalho virtual do Windows** na barra de pesquisa.
3. Em serviços, selecione **área de trabalho virtual do Windows**.
4. Na página área de trabalho virtual do Windows, selecione **pools de host** no menu no lado esquerdo da tela.
5. Selecione **o nome do pool de hosts** que você deseja atualizar.
6. Selecione **Propriedades de RDP** no menu no lado esquerdo da tela.
7. Defina a propriedade desejada.
   - Como alternativa, você pode abrir a guia **avançado** e adicionar suas propriedades RDP em um formato separado por ponto-e-vírgula, como os exemplos do PowerShell nas seções a seguir.
8. Quando terminar, selecione **salvar** para salvar as alterações.

As seções a seguir lhe dirão como editar manualmente as propriedades de RDP personalizadas no PowerShell.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Adicionar ou editar uma única propriedade RDP personalizada

Para adicionar ou editar uma única propriedade RDP personalizada, execute o seguinte cmdlet do PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

Para verificar se o cmdlet que você acabou de executar atualizou a propriedade, execute este cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Por exemplo, se você estiver verificando a propriedade "audiocapturemode" em um pool de hosts chamado 0301HP, você inseriria este cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Adicionar ou editar várias propriedades personalizadas de RDP

Para adicionar ou editar várias propriedades de RDP personalizadas, execute os seguintes cmdlets do PowerShell fornecendo as propriedades de RDP personalizadas como uma cadeia de caracteres separada por ponto-e-vírgula:

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

Você pode verificar para certificar-se de que a propriedade RDP foi adicionada executando o seguinte cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Com base em nosso exemplo de cmdlet anterior, se você configurar várias propriedades de RDP no pool de hosts 0301HP, o cmdlet ficaria assim:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>Redefinir todas as propriedades de RDP personalizadas

Você pode redefinir propriedades individuais de RDP personalizadas para seus valores padrão seguindo as instruções em [Adicionar ou editar uma única propriedade RDP personalizada](#add-or-edit-a-single-custom-rdp-property), ou você pode redefinir todas as propriedades RDP personalizadas para um pool de hosts executando o seguinte cmdlet do PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

Para verificar se você removeu com êxito a configuração, digite este cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>Próximas etapas

Agora que você personalizou as propriedades de RDP para um determinado pool de hosts, você pode entrar em um cliente de área de trabalho virtual do Windows para testá-los como parte de uma sessão de usuário. Os próximos guias de instruções lhe dirão como se conectar a uma sessão usando o cliente de sua escolha:

- [Conectar-se ao cliente da Área de Trabalho do Windows](connect-windows-7-10.md)
- [Conectar-se ao cliente Web](connect-web.md)
- [Conectar-se ao cliente Android](connect-android.md)
- [Conectar-se ao cliente macOS](connect-macos.md)
- [Conectar-se ao cliente iOS](connect-ios.md)