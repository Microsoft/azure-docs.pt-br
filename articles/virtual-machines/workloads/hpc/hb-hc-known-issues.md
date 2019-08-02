---
title: Problemas conhecidos com HB-HC-VMs das séries e - máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre problemas conhecidos com tamanhos VM da série HB no Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 8d4b57fb2fee3849e102868c86fe3cab465fc70d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707789"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Problemas comuns com VMs série HB e série HC

Este artigo fornece os problemas e soluções mais comuns ao usar HB-HC-VMs das séries e.

## <a name="dram-on-hb-series"></a>DRAM HB-Series

As VMs da série HB podem expor somente 228 GB de RAM para máquinas virtuais convidadas neste momento. Isso é devido a uma limitação conhecida de hipervisor do Azure para impedir que páginas que está sendo atribuído para o local DRAM do AMD CCX (domínios NUMA) reservada para a VM convidada.

## <a name="accelerated-networking"></a>Rede Acelerada

Rede acelerada do Azure não está habilitada no momento, mas será como nós progredimos com o período de visualização. Vamos notificar os clientes quando esse recurso tem suporte.

## <a name="qp0-access-restriction"></a>Restrição de acesso qp0

Para impedir o acesso de hardware de baixo nível que pode resultar em vulnerabilidades de segurança, o par de fila 0 não está acessível para as VMs convidadas. Isso só deve afetar ações normalmente associados com a administração da NIC ConnectX-5 e executar alguns diagnósticos InfiniBand como ibdiagnet, mas não os aplicativos do usuário final em si.

## <a name="ud-transport"></a>Transporte UD

No lançamento, as séries de HB e de HC não dão suporte a dinamicamente conectado transporte (DCT). Suporte para DCT será implementado ao longo do tempo. Há suporte para transportes de Conexão (RC) e não confiáveis datagrama (UD) confiáveis.

## <a name="gss-proxy"></a>Proxy GSS

GSS Proxy tem um bug conhecido no RHEL/CentOS 7.5 que pode se manifestar como um significativos de desempenho e a penalidade de capacidade de resposta quando usado com NFS. Isso pode ser reduzido com:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Limpeza do cache

Em sistemas HPC, geralmente é útil limpar a memória depois que um trabalho foi concluído antes do próximo usuário é atribuído o mesmo nó. Depois de executar aplicativos no Linux, você pode achar que sua memória disponível reduz enquanto o seu aumenta de memória de buffer, apesar de não executar quaisquer aplicativos.

![Captura de tela do prompt de comando](./media/known-issues/cache-cleaning-1.png)

Usando `numactl -H` mostrará quais NUMAnode(s) a memória é armazenada em buffer com (possivelmente todos). No Linux, os usuários podem limpar os caches em três maneiras de retornar armazenadas em buffer ou armazenados em cache de memória para 'gratuito'. Você precisa ser a raiz ou ter permissões de sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Captura de tela do prompt de comando](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avisos de kernel

Você pode ver as seguintes mensagens de aviso de kernel durante a inicialização de uma VM da série HB no Linux.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

Você pode ignorar este aviso. Isso ocorre devido a uma limitação conhecida do hipervisor do Azure que será resolvido ao longo do tempo.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [computação de alto desempenho](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
