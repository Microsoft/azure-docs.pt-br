---
title: Conectar a uma VM do Windows Server
description: Saiba como se conectar e entrar em uma VM do Windows usando o portal do Azure e o modelo de implantação do Resource Manager.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/26/2018
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 007719469eb19809d9e64bccfef9589e1fe491f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607306"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Como se conectar e entrar em uma máquina virtual do Azure executando o Windows
Você usará o botão **Conectar** no portal do Azure para iniciar uma sessão da Área de Trabalho Remota (RDP) a partir de uma área de trabalho do Windows. Primeiramente, conecte-se à máquina virtual então faça logon.

Para conectar-se a uma VM do Windows por meio de um Mac, será necessário instalar um cliente do RDP para Mac, como a [Área de Trabalho Remota da Microsoft](https://aka.ms/rdmac).

## <a name="connect-to-the-virtual-machine"></a>Conecte-se à máquina virtual
1. Vá para o [portal do Azure](https://portal.azure.com/) a fim de se conectar a uma VM. Pesquise por **Máquinas virtuais** e selecione essa opção.
2. Selecione a máquina virtual na lista.
3. No início da página da máquina virtual, selecione **Conectar**.
4. Na página **Conectar à máquina virtual**, selecione **RDP** e, em seguida, selecione o **Endereço IP** e o **Número da porta** apropriados. Na maioria dos casos, o endereço IP e a porta padrão devem ser usados. Selecione **Baixar Arquivo RDP**. Se a VM tiver uma política Just-In-Time definida, você primeiro precisará selecionar o botão **Solicitar acesso** para solicitar acesso antes de baixar o arquivo RDP. Para obter mais informações sobre a política Just-In-Time, confira [Gerenciar o acesso à máquina virtual usando a política Just-In-Time](../../security-center/security-center-just-in-time.md).
5. Abra o arquivo RDP baixado e selecione **Conectar** quando solicitado. Você receberá um aviso de que o arquivo `.rdp` é proveniente de um editor desconhecido. Isso é esperado. Na janela **Conexão de Área de Trabalho Remota**, selecione **Conectar** para continuar.
   
    ![Captura de tela de um aviso sobre um editor desconhecido.](./media/connect-logon/rdp-warn.png)
3. Na janela **Segurança do Windows**, selecione **Mais opções** e **Usar uma conta diferente**. Digite as credenciais de uma conta na máquina virtual e selecione **OK**.
   
     **Conta local (2008)** : Isso é geralmente o nome de usuário da conta local e senha que você especificou ao criar a máquina virtual. Nesse caso, o domínio é o nome da máquina virtual e é inserido como *nomedavm*&#92;*nome de usuário*.  
   
    **VM ingressada no domínio**: Se a VM pertencer a um domínio, digite o nome de usuário no formato *Domínio*&#92;*Nome de usuário*. A conta precisa estar no grupo Administradores ou ter privilégios de acesso remoto concedidos à VM.
   
    **Controlador de domínio**: Se a VM for um controlador de domínio, digite o nome de usuário e a senha da conta de administrador para esse domínio.
4. Selecione **Sim** para verificar a identidade da máquina virtual e concluir o logon.
   
   ![Captura de tela mostrando uma mensagem sobre como verificar a identidade da VM.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Se o botão **Conectar** no portal estiver esmaecido e você não estiver conectado ao Azure por meio de uma [Express Route](../../expressroute/expressroute-introduction.md) ou de uma conexão [VPN Site a Site](../../vpn-gateway/tutorial-site-to-site-portal.md), será necessário criar e atribuir à VM um endereço IP público antes de usar o RDP. Para obter mais informações, consulte [Endereços IP públicos no Azure](../../virtual-network/public-ip-addresses.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Conecte-se à máquina virtual usando o PowerShell

 

Se você estiver usando o PowerShell e tiver instalado o módulo do Azure PowerShell, você também poderá se conectar usando o cmdlet `Get-AzRemoteDesktopFile`, conforme mostrado abaixo.

Este exemplo iniciará imediatamente a conexão de RDP, levando você por meio de prompts semelhantes conforme acima.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Você também pode salvar o arquivo RDP para uso futuro.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Próximas etapas
Se você tiver dificuldade para se conectar, consulte [Solucionar problemas de conexões de Área de Trabalho Remota](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).