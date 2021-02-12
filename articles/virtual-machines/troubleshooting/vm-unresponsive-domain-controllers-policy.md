---
title: A VM não responde durante a aplicação da política de controladores de domínio padrão
titlesuffix: Azure Virtual Machines
description: Este artigo fornece etapas para resolver problemas em que a política de controladores de domínio padrão impede a inicialização de uma VM do Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 34e6b765-3496-46a1-b7d7-6def00884394
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: v-miegge
ms.openlocfilehash: 6c139398182ca9d875de0d3b21c58afe503bd8a5
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632267"
---
# <a name="vm-is-unresponsive-while-applying-default-domain-controllers-policy"></a>A VM não responde durante a aplicação da política de controladores de domínio padrão

Este artigo fornece etapas para resolver problemas em que a política de controladores de domínio padrão impede a inicialização de uma VM (máquina virtual) do Azure.

## <a name="symptom"></a>Sintoma

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para exibir a captura de tela da VM, você verá que a captura de tela exibe o sistema operacional ficando sem resposta durante a inicialização com a **política de controladores de domínio padrão** da mensagem.

  ![A Figura 1 exibe o sistema operacional preso com a mensagem "política de controladores de domínio padrão"](./media/vm-unresponsive-domain-controllers-policy/1-default-domain-controllers-policy.png)

## <a name="cause"></a>Causa

Esse problema pode ser devido a alterações recentes feitas na política de controladores de domínio padrão. Caso contrário, será necessário executar uma análise de arquivo de despejo de memória para determinar a causa raiz.

## <a name="solution"></a>Solução

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

Se você fez alterações recentemente na política de controladores de domínio padrão, talvez queira desfazer essas alterações para corrigir o problema. Se você não tiver certeza do que está causando o problema, colete um despejo de memória e, em seguida, envie um tíquete de suporte.

### <a name="collect-the-memory-dump-file"></a>Coletar o arquivo de despejo de memória

Para resolver esse problema, primeiro você deve coletar o arquivo de despejo de memória para a falha e, em seguida, contatar o suporte com o arquivo de despejo de memória. Para coletar o arquivo de despejo, siga estas etapas:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Anexar o disco do sistema operacional a uma nova VM de reparo

1. Use as [etapas 1 a 3 dos comandos de reparo da VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar uma VM de reparo.

1. Use a Conexão de Área de Trabalho Remota para conectar-se à VM de reparo.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localizar o arquivo de despejo e enviar um tíquete de suporte

1. Na VM de reparo, vá para a pasta do Windows no disco do sistema operacional anexado. Se a letra da unidade atribuída ao disco do SO anexado for `F`, você precisará ir até `F:\Windows`.

1. Localize o arquivo Memory. dmp e [envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo de memória.

1. Se você estiver tendo problemas para localizar o arquivo Memory. dmp, talvez queira usar [chamadas de NMI (interrupção não mascaráveis) no console serial](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) . Siga o guia para [gerar um arquivo de despejo de memória usando chamadas NMI](/windows/client-management/generate-kernel-or-complete-crash-dump).