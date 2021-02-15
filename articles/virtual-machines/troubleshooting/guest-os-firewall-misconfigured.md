---
title: Firewall do SO Guest do Azure VM está desconfigurado|Microsoft Docs
description: Saiba como usar o console serial ou o método offline para diagnosticar e corrigir um firewall de sistema operacional convidado configurado incorretamente em uma VM do Azure remota.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: e6f42bdf462ac5261f77bc05c62e50500345fe37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80422543"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Firewall do sistema operacional de convidado VM do Azure está configurado incorretamente

Este artigo apresenta como corrigir o firewall do sistema operacional convidado mal configurado no VM do Azure.

## <a name="symptoms"></a>Sintomas

1.  A tela de boas-vindas da máquina virtual (VM) mostra que a VM está totalmente carregada.

2.  Dependendo de como o sistema operacional convidado está configurado, pode haver algum tráfego ou nenhum tráfego de rede chegando à VM.

## <a name="cause"></a>Causa

Um erro de configuração do firewall do sistema convidado pode bloquear alguns ou todos os tipos de tráfego de rede para a VM.

## <a name="solution"></a>Solução

Antes de seguir estas etapas, tire um instantâneo do disco do sistema da VM afetada como um backup. Para obter mais informações, consulte [Instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

Para solucionar esse problema, use o Console serial ou [repare a VM off-line](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) anexando o disco do sistema da VM a um VM de recuperação.

## <a name="online-mitigations"></a>Reduções online

Conecte-se ao [ Console serial e abra uma instância do PowerShell ](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Se o Console Serial não estiver habilitado na VM, vá para a seção "Reparar a VM Off-line" do seguinte artigo do Azure:

 [Ocorre um erro interno ao tentar se conectar a uma VM do Azure por meio da área de trabalho remota](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

As regras a seguir podem ser editadas para permitir o acesso à VM (por meio do RDP) ou para fornecer uma experiência de solução de problemas mais fácil:

*   Área de Trabalho Remota (TCP-In): Esta é a regra padrão que fornece acesso primário à VM, permitindo RDP no Azure.

*   Gerenciamento Remoto do Windows (HTTP-In): essa regra permite que você se conecte à VM usando o PowerShell. No Azure, esse tipo de acesso permite usar o aspecto de script de scripts remotos e a solução de problemas.

*   Compartilhamento de arquivos e impressoras (SMB-In): essa regra permite o acesso ao compartilhamento de rede como uma opção de solução de problemas.

*   Compartilhamento de arquivos e impressoras (solicitação de eco - ICMPv4-In): essa regra permite executar o ping na VM.

Na instância do Serial Console Access, você pode consultar o status atual da regra de firewall.

*   Consulta usando o nome de exibição como um parâmetro:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Consulta usando a porta local que o aplicativo usa:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Consulta usando o endereço IP local que o aplicativo usa:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Se você perceber que a regra está desabilitada, poderá ativá-la executando o seguinte comando:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Para solucionar problemas, você pode desativar os perfis do firewall:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Se você fizer isso para definir o firewall corretamente, reative o firewall após concluir a solução de problemas.

    > [!Note]
    > Você não precisa reiniciar a VM para aplicar essa alteração.

*   Tente novamente se conectar à VM por meio do RDP.

### <a name="offline-mitigations"></a>Redução Offline

1.  Para habilitar ou desabilitar regras de firewall, consulte [habilitar ou desabilitar uma regra de firewall em um SO convidado de VM do Azure](enable-disable-firewall-rule-guest-os.md).

2.  Verifique se você está no cenário de tráfego de entrada do [Firewall do OS convidado bloqueando](guest-os-firewall-blocking-inbound-traffic.md).

3.  Se ainda estiver em dúvida se o firewall está bloqueando seu acesso, consulte [Desabilitar o Firewall do SO guest na VM do Azure](disable-guest-os-firewall-windows.md) e reativar o firewall do sistema convidado usando as regras corretas.

