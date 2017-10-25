---
title: Arquitetura do OMS (Operations Management Suite) | Microsoft Docs
description: "O OMS (Microsoft Operations Management Suite) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda a gerenciar e proteger sua infraestrutura local e de nuvem.  Este artigo identifica os diferentes serviços incluídos no OMS e fornece links para o conteúdo detalhado."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 40e41686-7e35-4d85-bbe8-edbcb295a534
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 76f69946724b5297b1f9a1f715819c69c4a4a51d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="oms-architecture"></a>Arquitetura do OMS
O [OMS (Operations Management Suite)](https://azure.microsoft.com/documentation/services/operations-management-suite/) é uma coleção de serviços baseados em nuvem para gerenciar seus ambientes locais e na nuvem.  Este artigo descreve os diferentes componentes locais e na nuvem do OMS e sua arquitetura de computação em nuvem de alto nível.  Consulte a documentação de cada serviço para obter mais detalhes.

## <a name="log-analytics"></a>Log Analytics
Todos os dados coletados pelo [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) são armazenados no repositório do OMS, que é hospedado no Azure.  As Fontes Conectadas geram os dados coletados no repositório do OMS.  Atualmente, há três tipos de fontes conectadas com suporte.

* Um agente instalado em um computador com [Windows](../log-analytics/log-analytics-windows-agents.md) ou [Linux](../log-analytics/log-analytics-linux-agents.md) conectado diretamente ao OMS.
* Um grupo de gerenciamento do SCOM (System Center Operations Manager) [conectado ao Log Analytics](../log-analytics/log-analytics-om-agents.md) .  Os agentes do SCOM continuam se comunicando com servidores de gerenciamento que encaminham eventos e dados de desempenho para o Log Analytics.
* Uma [conta de armazenamento do Azure](../log-analytics/log-analytics-azure-storage.md) que coleta dados do [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) por meio de uma função de trabalho, função web ou máquina virtual no Azure.

As fontes de dados definem os dados coletados pelo Log Analytics de fontes conectadas, incluindo logs de eventos e contadores de desempenho.  As soluções adicionam funcionalidade ao OMS e podem ser facilmente adicionadas ao seu espaço de trabalho por meio da [Galeria de Soluções do OMS](../log-analytics/log-analytics-add-solutions.md).  Algumas soluções podem exigir uma conexão direta ao Log Analytics por meio de agentes do SCOM, enquanto outras podem exigir a instalação de um agente adicional.

O Log Analytics tem um portal baseado na Web que você pode usar para gerenciar os recursos do OMS, adicionar e configurar as soluções do OMS, bem como exibir e analisar dados no repositório do OMS.

![Arquitetura de alto nível do Log Analytics](media/operations-management-suite-architecture/log-analytics.png)

## <a name="azure-automation"></a>Automação do Azure
[runbooks da Automação do Azure](http://azure.microsoft.com/documentation/services/automation) são executados na nuvem do Azure e podem acessar recursos que estão no Azure, em outros serviços de nuvem ou acessíveis pela Internet pública.  Você também pode designar computadores locais em seu data center local usando o [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md) para que os runbooks possam acessar recursos locais.

[configurações DSC](../automation/automation-dsc-overview.md) armazenadas na Automação do Azure podem ser aplicadas diretamente às máquinas virtuais do Azure.  Outras máquinas virtuais e físicas podem solicitar configurações do servidor de recepção de DSC de Automação do Azure.

A Automação do Azure traz uma solução do OMS que exibe estatísticas e links para iniciar o portal do Azure para qualquer operação.

![Arquitetura de alto nível da Automação do Azure](media/operations-management-suite-architecture/automation.png)

## <a name="azure-backup"></a>Serviço de Backup do Azure
Os dados protegidos no [Backup do Azure](http://azure.microsoft.com/documentation/services/backup) são armazenados em um cofre de backup localizado em uma região geográfica específica.  Os dados são replicados na mesma região e, dependendo do tipo de cofre, também podem ser replicados para outra região para redundância adicional.

O Backup do Azure apresenta três cenários fundamentais.

* Computador com Windows com o agente do Backup do Azure.  Isso permite que você faça backup de arquivos e pastas por meio de qualquer servidor ou cliente Windows diretamente em seu cofre de backup do Azure.  
* DPM (System Center Data Protection Manager) ou Servidor de Backup do Microsoft Azure. Isso permite que você aproveite o DPM ou o Servidor de Backup do Microsoft Azure para fazer backup de arquivos e pastas, além de cargas de trabalho do aplicativo, como SQL e SharePoint, no armazenamento local e, em seguida, replicá-los em seu cofre de backup do Azure.
* Extensões da Máquina Virtual do Azure.  Isso permite que você faça backup de máquinas virtuais do Azure em seu cofre de backup do Azure.

O Backup do Azure traz uma solução do OMS que exibe estatísticas e links para iniciar o portal do Azure para qualquer operação.

![Arquitetura de alto nível do Backup do Azure](media/operations-management-suite-architecture/backup.png)

## <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) orquestra a replicação, o failover e o failback de máquinas virtuais e servidores físicos. Os dados de replicação são trocados entre hosts Hyper-V, hipervisores VMware ou servidores físicos nos data centers primário e secundário, ou entre o data center e o armazenamento do Azure.  A Recuperação de Site armazena os metadados em cofres localizados em determinada região geográfica do Azure. Nenhum dado replicado é armazenado pelo serviço da Recuperação de Site.

O Azure Site Recovery apresenta três cenários fundamentais de replicação.

**Replicação de máquinas virtuais Hyper-V**

* Se as máquinas virtuais Hyper-V forem gerenciadas em nuvens do VMM, será possível replicá-las em um data center secundário ou no armazenamento do Azure.  A replicação no Azure é feita por uma conexão segura com a Internet.  A replicação em um data center secundário é feita pela LAN.
* Se as máquinas virtuais Hyper-V não forem gerenciadas pelo VMM, será possível replicar apenas o armazenamento do Azure.  A replicação no Azure é feita por uma conexão segura com a Internet.

**Replicação de máquinas virtuais VMWare**

* É possível replicar máquinas virtuais VMware em um data center secundário que executa o VMware ou no armazenamento do Azure.  A replicação no Azure pode ocorrer por uma VPN site a site, pelo Azure ExpressRoute ou por uma conexão segura com a Internet. A replicação em um data center secundário ocorre pelo canal de dados do InMage Scout.

**Replicação de servidores físicos do Windows e Linux** 

* É possível replicar servidores físicos em um data center secundário ou no armazenamento do Azure. A replicação no Azure pode ocorrer por uma VPN site a site, pelo Azure ExpressRoute ou por uma conexão segura com a Internet. A replicação em um data center secundário ocorre pelo canal de dados do InMage Scout.  O Azure Site Recovery traz uma solução do OMS que exibe algumas estatísticas, mas é necessário usar o portal do Azure para qualquer operação.

![Arquitetura de alto nível do Azure Site Recovery](media/operations-management-suite-architecture/site-recovery.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Saiba mais sobre a [Automação do Azure](https://azure.microsoft.com/documentation/services/automation).
* Saiba mais sobre o [Backup do Azure](http://azure.microsoft.com/documentation/services/backup).
* Saiba mais sobre o [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).

