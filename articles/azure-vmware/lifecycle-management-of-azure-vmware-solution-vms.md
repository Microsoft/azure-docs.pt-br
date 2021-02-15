---
title: Gerenciamento do ciclo de vida de VMs da solução Azure VMware
description: Saiba como gerenciar todos os aspectos do ciclo de vida de suas VMs de solução do Azure VMware com Microsoft Azure ferramentas nativas.
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: d8224a37e46b336ebf889fe1c075930f34f10ca4
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988526"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Gerenciamento do ciclo de vida de VMs da solução Azure VMware

Microsoft Azure ferramentas nativas permitem que você monitore e gerencie suas VMs (máquinas virtuais) no ambiente do Azure. Ainda assim, eles também permitem que você monitore e gerencie suas VMs na solução VMware do Azure e suas VMs locais. Nesta visão geral, veremos a arquitetura de monitoramento integrado que o Azure oferece e como você pode usar suas ferramentas nativas para gerenciar suas VMs de solução do Azure VMware em todo o ciclo de vida.

## <a name="benefits"></a>Benefícios

- Os serviços nativos do Azure podem ser usados para gerenciar suas VMs em um ambiente híbrido (Azure, solução do Azure VMware e local).
- Monitoramento e visibilidade integrados de seu Azure, solução Azure VMware e VMs locais.
- Com o Azure Gerenciamento de Atualizações na automação do Azure, você pode gerenciar atualizações do sistema operacional para suas máquinas Windows e Linux. 
- A central de segurança do Azure fornece proteção avançada contra ameaças, incluindo:
    - Monitoramento de integridade do arquivo
    - Alertas de segurança sem arquivo
    - Avaliação de patch do sistema operacional
    - Avaliação de configurações incorretas de segurança
    - Avaliação da proteção de ponto de extremidade 
- Implante facilmente o agente de Log Analytics usando os servidores habilitados para Arc do Azure suporte de extensão de VM para VMs novas e existentes. 
- Seu espaço de trabalho Log Analytics no Azure Monitor habilita a coleta de logs e a coleta do contador de desempenho usando o agente ou as extensões do Log Analytics. Colete dados e logs para um único ponto e apresente esses dados a diferentes serviços nativos do Azure. 
- Os benefícios adicionais do Azure Monitor incluem: 
    - Monitoramento contínuo 
    - Melhor visibilidade da infraestrutura 
    - Notificações instantâneas 
    - Resolução automática 
    - Eficiência de custo 

## <a name="integrated-azure-monitoring-architecture"></a>Arquitetura de monitoramento do Azure integrada

O diagrama a seguir mostra a arquitetura de monitoramento integrado para VMs de solução do Azure VMware.

![Arquitetura de monitoramento do Azure integrada](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>Antes de começar

Se você for novo no Azure ou não estiver familiarizado com qualquer um dos serviços mencionados anteriormente, examine os seguintes artigos:

- [Visão geral da autenticação da conta de automação](../automation/automation-security-overview.md)
- [Criando sua implantação de logs do Azure monitor](../azure-monitor/platform/design-logs-deployment.md) e [Azure monitor](../azure-monitor/overview.md)
- [Planejamento](../security-center/security-center-planning-and-operations-guide.md) e [plataformas com suporte](../security-center/security-center-os-coverage.md) para a central de segurança do Azure
- [Visão geral de Habilitar o Azure Monitor para VMs](../azure-monitor/insights/vminsights-enable-overview.md)
- [O que são servidores habilitados para Arc do Azure?](../azure-arc/servers/overview.md) e [o que é o Azure Arc habilitado kubernetes?](../azure-arc/kubernetes/overview.md)
- [Visão geral do Gerenciamento de Atualizações](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>Integrando e implantando os serviços nativos do Azure

### <a name="enable-azure-update-management"></a>Habilitar Gerenciamento de Atualizações do Azure

O Azure Gerenciamento de Atualizações na automação do Azure gerencia atualizações do sistema operacional para seus computadores Windows e Linux em um ambiente híbrido. Ele monitora a conformidade de patches e encaminha alertas de desvio de aplicação de patch para Azure Monitor para correção. O Azure Gerenciamento de Atualizações deve se conectar ao seu espaço de trabalho do Log Analytics para usar dados armazenados para avaliar o status das atualizações em suas VMs.

1.  Antes de adicionar Log Analytics ao Gerenciamento de Atualizações do Azure, primeiro você precisa [criar uma conta de automação do Azure](../automation/automation-create-standalone-account.md). Se preferir criar sua conta usando um modelo, veja [Criar uma conta de Automação usando um modelo do Azure Resource Manager](../automation/quickstart-create-automation-account-template.md).

2. **Log Analytics espaço de trabalho** habilita a coleta de logs e a coleta de contadores de desempenho usando o agente log Analytics ou extensões. Para criar seu espaço de trabalho Log Analytics, consulte [criar um espaço de trabalho log Analytics no portal do Azure](../azure-monitor/learn/quick-create-workspace.md). Se preferir, você também pode criar um espaço de trabalho por meio da [CLI](../azure-monitor/learn/quick-create-workspace-cli.md), do [PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md)ou do [modelo de Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md).

3. Para habilitar o Azure Gerenciamento de Atualizações para suas VMs, consulte [habilitar gerenciamento de atualizações de uma conta de automação](../automation/update-management/enable-from-automation-account.md). No processo, você vinculará seu espaço de trabalho do Log Analytics à sua conta de automação. 
 
4. Depois de adicionar VMs ao Gerenciamento de Atualizações do Azure, você pode [implantar atualizações em VMs e examinar os resultados](../automation/update-management/deploy-updates.md). 

### <a name="enable-azure-security-center"></a>Habilitar a Central de Segurança do Azure

A central de segurança do Azure fornece proteção avançada contra ameaças em suas cargas de trabalho híbridas na nuvem e localmente. Ele avaliará a vulnerabilidade das VMs de solução do Azure VMware e emitirá alertas conforme necessário. Esses alertas de segurança podem ser encaminhados para Azure Monitor para resolução.

A central de segurança do Azure não requer implantação. Para obter mais informações, consulte uma lista de [recursos com suporte para máquinas virtuais](../security-center/security-center-services.md).

1. Para adicionar VMs da solução Azure VMware e VMs não Azure à central de segurança, consulte [início rápido: Configurando a central de segurança do Azure](../security-center/security-center-get-started.md). 

2. Depois de adicionar VMs da solução do Azure VMware ou VMs de um ambiente não Azure, habilite o Azure defender na central de segurança. A central de segurança avaliará as VMs em busca de possíveis problemas de segurança. Ele também fornece recomendações na guia Visão geral. Para obter mais informações, consulte [recomendações de segurança na central de segurança do Azure](../security-center/security-center-recommendations.md).

3. Você pode definir políticas de segurança na central de segurança do Azure. Para obter informações sobre como configurar suas políticas de segurança, consulte [trabalhando com políticas de segurança](../security-center/tutorial-security-policy.md).

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Carregar VMs para servidores habilitados para Arc do Azure

O Arc do Azure estende o gerenciamento do Azure para qualquer infraestrutura, incluindo a solução Azure VMware, local ou outras plataformas de nuvem.

- Para obter informações sobre como habilitar servidores habilitados para Arc do Azure para várias VMs do Windows ou Linux, consulte [conectar computadores híbridos ao Azure em escala](../azure-arc/servers/onboard-service-principal.md).

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Integrar clusters híbridos do kubernetes com o kubernetes habilitado para Arc

Você pode anexar um cluster kubernetes hospedado em seu ambiente de solução do Azure VMware usando o kubernetes habilitado para Arc do Azure. 

- Para obter mais informações, consulte [criar uma entidade de serviço de integração habilitada para o Arc do Azure](../azure-arc/kubernetes/create-onboarding-service-principal.md).

### <a name="deploy-the-log-analytics-agent"></a>Implantar o agente de Log Analytics

As VMs da solução Azure VMware podem ser monitoradas por meio do agente de Log Analytics (também conhecido como Microsoft Monitoring Agent (MMA) ou agente do OMS para Linux). Você já criou um espaço de trabalho Log Analytics ao habilitar a Gerenciamento de Atualizações de automação do Azure.

- Implante o agente de Log Analytics usando o [suporte de extensão de VM de servidores habilitados para Arc do Azure](../azure-arc/servers/manage-vm-extensions.md).

### <a name="enable-azure-monitor"></a>Habilitar Azure Monitor

Azure Monitor é uma solução abrangente para coletar, analisar e agir na telemetria de seus ambientes locais e na nuvem. Ele não requer nenhuma implantação. Com o Azure Monitor, você pode monitorar o desempenho do sistema operacional convidado e descobrir e mapear dependências de aplicativo para a solução do Azure VMware ou VMs locais.

- Azure Monitor permite que você colete dados de fontes diferentes para monitorar e analisar. Para obter mais informações, consulte [fontes de dados de monitoramento para Azure monitor](../azure-monitor/platform/data-sources.md).

- Colete diferentes tipos de dados para análise, visualização e alertas. Para obter mais informações, consulte [Azure monitor Data Platform](../azure-monitor/platform/data-platform.md).

- Para configurar Azure Monitor com seu espaço de trabalho do Log Analytics, consulte [configurar log Analytics espaço de trabalho para Azure monitor para VMs](../azure-monitor/insights/vminsights-configure-workspace.md).

- Você pode criar regras de alerta para identificar problemas em seu ambiente, como alto uso de recursos, patches ausentes, pouco espaço em disco e pulsação de suas VMs. Você também pode definir uma resposta automatizada para eventos detectados enviando um alerta para as ferramentas de gerenciamento de serviços de ti (ITSM). A notificação de detecção de alerta também pode ser enviada por email. Para criar essas regras, consulte:
    - [Crie, exiba e gerencie alertas de métrica usando Azure monitor](../azure-monitor/platform/alerts-metric.md).
    - [Crie, exiba e gerencie alertas de log usando Azure monitor](../azure-monitor/platform/alerts-log.md).
    - [Regras de ação](../azure-monitor/platform/alerts-action-rules.md) para definir ações e notificações automatizadas.
    - [Conecte o Azure a ferramentas de ITSM usando o conector de gerenciamento de serviços de ti](../azure-monitor/platform/itsmc-overview.md).
    
 ## <a name="next-steps"></a>Próximas etapas

Agora que você já abordou o uso das ferramentas nativas do Azure para gerenciar suas VMs de solução do Azure VMware em todo o ciclo de vida, talvez você queira saber mais sobre:

- [Proteger suas VMs de solução do Azure VMware com a central de segurança do Azure](azure-security-integration.md).
- [Configurando servidor de backup do Azure para a solução do Azure VMware](set-up-backup-server-for-azure-vmware-solution.md).
- [Integração da solução Azure VMware em uma arquitetura de Hub e spoke](concepts-hub-and-spoke.md).
