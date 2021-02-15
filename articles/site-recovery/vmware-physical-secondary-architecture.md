---
title: Arquitetura-recuperação de desastres do VMware/físico para um site secundário com Azure Site Recovery
description: Este artigo fornece uma visão geral dos componentes e da arquitetura usados durante a recuperação de desastres de VMs VMware locais ou servidores físicos Windows / Linux para um site VMware secundário com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: d400e6bcda0a2114d798a3289f01f52b677a6f94
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656488"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>Arquitetura para replicação do VMware/servidor físico para um site local secundário

Este artigo descreve a arquitetura e os processos utilizados ao fazer a recuperação de desastre, o failover e a recuperação de VMs (máquinas virtuais) do VMware ou servidores físicos do Windows/Linux para um site VMware secundário utilizando o [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componentes de arquitetura

**Área** | **Componente** | **Detalhes**
--- | --- | ---
**Azure** | Implante este cenário usando o InMage Scout. | Para obter o InMage Scout, você precisa de uma assinatura do Azure.<br/><br/> Depois de criar um cofre de Serviços de Recuperação, baixe o InMage Scout e instale as atualizações mais recentes para configurar a implantação.
**Servidor de processo** | Localizado no site primário | Implante o servidor de processo para manipular o caching, a compactação e a otimização de dados.<br/><br/> Ele também trata da instalação por push do Agente Unificado nas máquinas que você deseja proteger.
**Servidor de configuração** | Localizado no site secundário | O servidor de configuração gerencia, configura e monitora sua implantação, usando o site de gerenciamento ou o console do vContinuum.
**Servidor vContinuum** | Opcional. Instalado no mesmo local que o servidor de configuração. | Ele fornece um console para o gerenciamento e monitoramento de seu ambiente protegido.
**Servidor de destino mestre** | Localizado no site secundário | O servidor de destino mestre armazena os dados replicados. Ele recebe os dados do servidor de processo, cria uma máquina de réplica no site secundário e mantém os pontos de retenção dos dados.<br/><br/> O número de servidores de destino mestre necessário depende do número de máquinas que você está protegendo.<br/><br/> Se você quiser realizar o failback no site primário, também precisará de um servidor de destino mestre nesse local. O Agente Unificado está instalado neste servidor.
**Servidor VMware ESX/ESXi e vCenter** |  VMs são hospedadas em hosts ESX/ESXi. Hosts são gerenciados com um servidor do vCenter | Você precisa de uma infraestrutura do VMware para replicar VMs VMware.
**VMs/servidores físicos** |  Agente Unificado instalado em VMs VMware ou em servidores físicos que você deseja replicar. | O agente atua como um provedor de comunicação entre todos os componentes.

## <a name="set-up-outbound-network-connectivity"></a>Configurar a conectividade de rede de saída

Para que Site Recovery funcionem conforme o esperado, você precisa modificar a conectividade de rede de saída para permitir que seu ambiente seja replicado.

> [!NOTE]
> O Site Recovery não dá suporte ao uso de um proxy de autenticação para controlar a conectividade de rede.

### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Caso esteja usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita acesso a estas URLs:

| **Nome**                  | **Comercial**                               | **Governo**                                 | **Descrição** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Armazenamento                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Permite que os dados sejam gravados da VM para a conta de armazenamento de cache da região de origem. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fornece autorização e autenticação para as URLs do serviço Site Recovery. |
| Replicação               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Permite que a VM se comunique com o serviço Site Recovery. |
| Barramento de Serviço               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Permite que a VM grave o monitoramento do Site Recovery e os dados de diagnóstico. |

## <a name="replication-process"></a>Processo de replicação

1. Configure os servidores de componente em cada site (configuração, processo, destino mestre) e instale o Agente Unificado nos computadores que você deseja replicar.
2. Após a replicação inicial, os agentes em cada computador enviam as alterações de replicação delta para o servidor de processo.
3. O servidor de processo otimiza os dados e os transfere para o servidor de destino mestre no site secundário. O servidor de configuração gerencia o processo de replicação.

![Diagrama mostrando a replicação de VMs VMware e servidores físicos para um datacenter secundário](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>Próximas etapas

[Configurar](vmware-physical-secondary-disaster-recovery.md) a recuperação de desastres das VMs do VMware e dos servidores físicos em um site secundário.
