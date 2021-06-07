---
title: Solucionar problemas de failback em recuperação de desastre de VM VMware com Azure Site Recovery
description: Este artigo descreve maneiras de solucionar problemas de failback e reprotecção durante a recuperação de desastres de VMs do VMware para o Azure com o Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sharrai
ms.openlocfilehash: ed4e52470264441a99c5ccf0a736bb00233510c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87423109"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Resolver problemas de failback no local do Azure

Este artigo descreve como solucionar problemas que podem ser encontrados ao fazer failback nas VMs do Azure na sua infraestrutura VMware local, após o failover para o Azure usando o [Azure Site Recovery](site-recovery-overview.md).

O failback envolve essencialmente duas etapas principais. Para a primeira etapa, após o failover, é necessário fazer nova proteção das VMs do Azure para local para que iniciem a replicação. A segunda etapa é executar um failover do Azure, para fazer failback ao site local.

## <a name="common-issues"></a>Problemas comuns

- Se você executar a descoberta de vCenter de Usuário somente leitura e proteger as máquinas virtuais, a proteção terá êxito e o failover funcionará. Durante a nova proteção, o failover falhar porque os armazenamentos de dados não podem ser descobertos. Um sintoma é que os repositórios de dados não estão listados durante a nova proteção. Para resolver esse problema, você pode atualizar as credenciais do vCenter com uma conta apropriada que tenha permissões e, em seguida, repita o trabalho.
- Ao executar failback de uma máquina virtual Linux e executá-la localmente, você verá que o pacote do Gerenciador de Rede foi desinstalado do computador. Essa desinstalação ocorre porque o pacote do Gerenciador de rede é removido quando a máquina virtual será recuperada no Azure.
- Quando uma máquina virtual Linux é configurada com um endereço IP estático e o failover é feito para o Azure, o endereço IP é obtido do DHCP. Quando você executa o failover localmente, a máquina virtual continua a usar o DHCP para obter o endereço IP. Entrar manualmente para a máquina e, em seguida, defina o endereço IP para um endereço estático, se necessário. Uma máquina virtual do Windows podem obter seu endereço IP novamente.
- Se você usar a edição gratuita ESXi 5.5 ou a edição gratuita do vSphere 6 hipervisor, o failover for bem-sucedido, mas failback não for bem-sucedida. Para permitir o failback, atualize para a licença de avaliação dos programas.
- Se você não conseguir acessar o servidor de configuração do servidor de processo, use o Telnet para verificar a conectividade com o servidor de configuração na porta 443. Você também pode tentar executar ping no servidor de configuração do servidor de processo. Um servidor de processo também deve ter uma pulsação quando ele está conectado ao servidor de configuração.
- Um servidor do Windows Server 2008 R2 SP1 que é protegido como um servidor de local físico não pode ser com falha do Azure para um site local.
- Não é possível fazer failback nas seguintes circunstâncias:
    - Você migrou computadores para o Azure. 
    - Uma VM foi movida para outro grupo de recursos.
    - A VM do Azure foi excluída.
    - A proteção da VM foi desabilitada.
    - A VM foi criada manualmente no Azure. A máquina deve ter protegido inicialmente no local e failover para o Azure antes que passou por failover.
    - Você pode não apenas para um host de ESXi. Não é possível failback de VMs VMware ou servidores físicos para hosts do Hyper-V, computadores físicos ou estações de trabalho VMware.


## <a name="troubleshoot-reprotection-errors"></a>Solucionar problemas de erros de nova proteção

Esta seção detalha os erros de nova proteção comuns e como corrigi-los.

### <a name="error-code-95226"></a>Código de erro 95226

**A nova proteção falhou porque a máquina virtual do Azure não foi capaz de alcançar o servidor de configuração local.**

Esse erro ocorre quando:

* A VM do Azure não pode alcançar o servidor de configuração local. A VM não pode ser descoberta e registrada no servidor de configuração.
* O serviço de aplicativo InMage Scout não está executando na VM Azure após o failover. O serviço é necessário para comunicações com o servidor de configuração local.

Para resolver o problema:

* Verifique se a rede de VMs do Azure permite que a VM do Azure comunique-se com o servidor de configuração local. Você pode configurar uma VPN site a site no datacenter local ou configure uma conexão ExpressRoute do Azure com o emparelhamento privado na rede virtual do VM do Azure.
* Se a VM pode se comunicar com o servidor de configuração local, entre na VM. Em seguida, verifique o serviço de aplicativo InMage Scout. Se você vir que ele não está em execução, inicie o serviço manualmente. Verifique se o tipo de início do serviço está definido como **automático**.

### <a name="error-code-78052"></a>Código de erro 78052

**Não foi possível concluir a proteção da máquina virtual.**

Esse problema pode ocorrer se já houver uma VM com o mesmo nome no servidor de destino mestre para qual o failback está falhando.

Para resolver o problema:

* Selecione um servidor de destino mestre diferente em um host diferente, de modo que a nova proteção crie a máquina em um host diferente, onde os nomes não entrem em conflito.
* Você também pode usar o VMotion para mover o destino mestre para um host diferente em que a colisão de nome não acontecerá. Se a VM existente for um computador perdido, renomeie-a para que a nova VM possa ser criada no mesmo host ESXi.


### <a name="error-code-78093"></a>Código de erro 78093

**A VM não está em execução, não está respondendo ou não está acessível.**

Para resolver o problema:

Para proteger uma VM de failover, a VM do Azure deve estar executando para que o Serviço de Mobilidade registre-se no servidor de configuração local e possa iniciar a replicação comunicando-se com o servidor de processo. Se o computador estiver em uma rede incorreta ou não estiver em execução (não respondendo ou desligando), o servidor de configuração não poderá acessar o serviço de mobilidade na VM para iniciar a nova proteção.

* Reinicie a VM para que possa começar a comunicar-se de volta no local.
* Reinicie o trabalho de nova proteção depois de iniciar a máquina virtual do Azure.

### <a name="error-code-8061"></a>Código de erro 8061

**A loja de dados não está acessível no host ESXi.**

Verifique os [pré-requisitos de destino e os armazenamentos de dados com suporte](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) para failback.


## <a name="troubleshoot-failback-errors"></a>Solucionar problemas de erros de failback

Esta seção descreve erros comuns que podem ser encontrados durante o failback.

### <a name="error-code-8038"></a>Código de erro 8038

**Falha ao ativar a máquina virtual local devido ao erro.**

Esse problema ocorre quando a VM local é criada em um host que não possui memória provisionada suficiente. 

Para resolver o problema:

* Provisione mais memória no host ESXi.
* Além disso, você pode usar o VMotion para mover a VM para outro host ESXi com memória suficiente para inicializar a VM.
