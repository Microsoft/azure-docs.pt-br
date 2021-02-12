---
title: Erro de parada do Windows-exceção de thread do sistema 0x0000007E não tratada
description: Este artigo fornece etapas para resolver problemas em que o sistema operacional convidado encontra um problema e deseja reiniciar a VM do Azure. A mensagem irá declarar que "uma exceção de thread do sistema não foi tratada".
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: beb658474e49f9b47900d8481dab0424dae8c5be
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661384"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Erro de parada do Windows-exceção de thread do sistema 0x0000007E não tratada

Este artigo fornece etapas para resolver problemas em que o sistema operacional convidado (SO convidado) encontra um problema e tenta reiniciar sua VM (máquina virtual) do Azure. A mensagem de erro exibida diz "uma exceção de thread do sistema não foi tratada".

## <a name="symptoms"></a>Sintomas

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir uma captura de tela da saída da VM, você verá que o Windows precisa ser reiniciado com o código de parada "exceção de thread do sistema não tratada" ou o código de erro "0x0000007E".

![Captura de tela mostrando o Windows preso durante a inicialização com um "seu PC encontrou um problema e precisa ser reiniciado. Vamos reiniciar para você. " mensagem de erro e um código de parada "exceção de THREAD do sistema não tratada".](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Causa

A causa não pode ser determinada até que um arquivo de despejo de memória seja analisado. Continue a coletar o arquivo de despejo de memória.

## <a name="solution"></a>Solução

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.


Para resolver esse problema, primeiro você precisa coletar o arquivo de despejo de memória para a falha e, em seguida, enviar o arquivo para o suporte da Microsoft. Para coletar o arquivo de despejo, siga as instruções nas duas próximas seções.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Anexar o disco do sistema operacional a uma nova VM de reparo

1. Para preparar uma VM de reparo, siga as etapas 1-3 dos [comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md).
1. Conecte-se à VM de reparo usando Conexão de Área de Trabalho Remota.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localizar o arquivo de despejo e enviar um tíquete de suporte

1. Na VM de reparo, vá para a pasta do Windows no disco do sistema operacional anexado. Por exemplo, se a letra da unidade atribuída ao disco do sistema operacional anexado for rotulada como *F*, vá para `F:\Windows` .
1. Procure o arquivo *Memory. dmp* e, em seguida, [envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo de memória anexado.
1. Se você estiver tendo problemas para localizar o arquivo *Memory. dmp* , siga o guia para [gerar um arquivo de despejo de memória usando chamadas de NMI (interrupção não maskável)](/windows/client-management/generate-kernel-or-complete-crash-dump).

Para obter mais informações sobre chamadas NMI, consulte as [chamadas NMI no guia do usuário do console serial do Azure](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) .

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Solucionar erros de inicialização da máquina virtual do Azure](./boot-error-troubleshoot.md)
