---
title: Solucione o failback para local durante a recuperação de desastre de VMware para o Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve maneiras de solucionar problemas de failback e reprotecção durante a recuperação de desastres de VMs do VMware para o Azure com o Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 20cb7a446befb1d31f0e069d91d0230fc4a2a901
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565592"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Resolver problemas de failback no local do Azure

Este artigo descreve como solucionar problemas que podem ser encontrados ao fazer failback nas VMs do Azure na sua infraestrutura VMware local, após o failover para o Azure usando o [Azure Site Recovery](site-recovery-overview.md).

O failback envolve essencialmente duas etapas principais. Para a primeira etapa, após o failover, é necessário fazer nova proteção das VMs do Azure para local para que iniciem a replicação. A segunda etapa é executar um failover do Azure, para fazer failback ao site local.

## <a name="troubleshoot-reprotection-errors"></a>Solucionar problemas de erros de nova proteção

Esta seção detalha os erros de nova proteção comuns e como corrigi-los.

### <a name="error-code-95226"></a>Código de erro 95226

**A nova proteção falhou porque a máquina virtual do Azure não foi capaz de alcançar o servidor de configuração local.**

Esse erro ocorre quando:

* A VM do Azure não pode alcançar o servidor de configuração local. A VM não pode ser descoberta e registrada no servidor de configuração.
* O serviço de aplicativo InMage Scout não está executando na VM Azure após o failover. O serviço é necessário para comunicações com o servidor de configuração local.

Para resolver esse problema:

* Verifique se a rede de VMs do Azure permite que a VM do Azure comunique-se com o servidor de configuração local. Você pode configurar uma VPN site a site no datacenter local ou configure uma conexão ExpressRoute do Azure com o emparelhamento privado na rede virtual do VM do Azure.
* Se a VM pode se comunicar com o servidor de configuração local, entre na VM. Em seguida, verifique o serviço de aplicativo InMage Scout. Se você vir que ele não está em execução, inicie o serviço manualmente. Verifique se o tipo de início do serviço está definido como **automático**.

### <a name="error-code-78052"></a>Código de erro 78052

**Não foi possível concluir a proteção da máquina virtual.**

Esse problema pode ocorrer se já houver uma VM com o mesmo nome no servidor de destino mestre para qual o failback está falhando.

Para resolver esse problema:

* Selecione um servidor de destino mestre diferente em um host diferente, de modo que a nova proteção crie a máquina em um host diferente, onde os nomes não entrem em conflito.
* Você também pode usar vMotion para mover o destino mestre para um host diferente no qual não ocorra conflito entre os nomes. Se a VM existente for um computador perdido, renomeie-a para que a nova VM possa ser criada no mesmo host ESXi.


### <a name="error-code-78093"></a>Código de erro 78093

**A VM não está em execução, está em um estado suspenso ou não está acessível.**

Para resolver esse problema:

Para proteger uma VM de failover, a VM do Azure deve estar executando para que o Serviço de Mobilidade registre-se no servidor de configuração local e possa iniciar a replicação comunicando-se com o servidor de processo. Se o computador está em uma rede incorreta ou não está em execução (não responder ou desligado), o servidor de configuração não pode acessar o serviço de mobilidade na VM para iniciar a nova proteção.

* Reinicie a VM para que possa começar a comunicar-se de volta no local.
* Reinicie o trabalho de nova proteção depois de iniciar a máquina virtual do Azure.

### <a name="error-code-8061"></a>Código de erro 8061

**A loja de dados não está acessível no host ESXi.**

Verifique os [pré-requisitos de destino e os armazenamentos de dados com suporte](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) para failback.


## <a name="troubleshoot-failback-errors"></a>Solucionar problemas de erros de failback

Esta seção descreve erros comuns que podem ser encontrados durante o failback.

### <a name="error-code-8038"></a>Código de erro 8038

**Falha ao ativar a máquina virtual local devido ao erro.**

Esse problema ocorre quando a VM local é criada em um host que não possui memória provisionada suficiente. 

Para resolver esse problema:

* Provisione mais memória no host ESXi.
* Além disso, você pode usar o vMotion para mover a VM para outro host ESXi que tenha memória suficiente para inicializar a VM.
