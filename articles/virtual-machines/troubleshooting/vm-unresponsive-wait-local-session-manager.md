---
title: A VM não está respondendo ao aguardar o serviço Gerenciador de sessão local
description: Este artigo fornece etapas para resolver problemas em que o sistema operacional convidado está preso aguardando o Gerenciador de sessão local concluir o processamento durante a inicialização de uma VM do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: fc3bd5d2590e969db07e9dffa61b4902ea4604c3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632539"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager-service"></a>A VM não está respondendo ao aguardar o serviço Gerenciador de sessão local

Este artigo fornece etapas para resolver problemas em que o sistema operacional convidado (SO convidado) está preso aguardando o Gerenciador de sessão local concluir o processamento enquanto Inicializa uma VM (máquina virtual) do Azure.

## <a name="symptoms"></a>Sintomas

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir uma captura de tela da saída da VM, você verá que a captura de tela exibe um prompt com uma mensagem "Aguarde o Gerenciador de sessão local".

![Captura de tela mostrando o sistema operacional convidado preso no Windows Server 2012 R2, com uma mensagem "Aguarde o Gerenciador de sessão local".](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Causa

Pode haver várias razões para uma VM ficar paralisada aguardando o Gerenciador de sessão local. Se esse problema persistir, você precisará coletar um despejo de memória para análise.

## <a name="solution"></a>Solução

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

Em alguns casos, simplesmente esperar que o processo seja concluído resolverá o problema. Se sua VM não responder e permanecer na tela de espera por mais de uma hora, você deverá coletar um despejo de memória e entrar em contato com o suporte da Microsoft.

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
> [Solucionar erros de inicialização da máquina virtual do Azure](boot-error-troubleshoot.md)