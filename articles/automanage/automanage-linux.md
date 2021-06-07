---
title: Autogerenciar do Azure para Linux
description: Saiba mais sobre as práticas recomendadas de gerenciamento automático do Azure para máquinas virtuais para serviços que são automaticamente integrados e configurados para computadores Linux.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 3aab43be49cb98fbe136e1f0216590785d650392
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953242"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---linux"></a>Autogerenciamento do Azure para máquinas virtuais práticas recomendadas-Linux

Esses serviços do Azure são automaticamente integrados para você quando você usa o autogerenci para VMs (máquinas virtuais) em uma VM do Linux. Eles são essenciais para nossas práticas recomendadas white paper, que você pode encontrar em nossa [estrutura de adoção de nuvem](/azure/cloud-adoption-framework/manage/azure-server-management).

Para todos esses serviços, iremos integrar automaticamente, configurar automaticamente, monitorar descompasso e corrigir se o descompasso for detectado. Para saber mais sobre esse processo, confira [autogerenciar do Azure para máquinas virtuais](automanage-virtual-machines.md).

## <a name="supported-linux-distributions-and-versions"></a>Distribuições e versões do Linux com suporte

O autogerenci oferece suporte às seguintes distribuições e versões do Linux:

- CentOS 7.3 +
- RHEL 7.4 +
- Ubuntu 16.04 e 18.04
- SLES 12 (somente SP3 – SP5)

## <a name="participating-services"></a>Serviços participantes

>[!NOTE]
> O Microsoft Antimalware não tem suporte em VMs do Linux no momento.

|Serviço    |Descrição    |Ambientes com suporte<sup>1</sup>    |Preferências com suporte<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|Monitoramento de informações de VM    |O Azure Monitor para VMs monitora o desempenho e a integridade de suas máquinas virtuais, incluindo seus processos e dependências em execução em outros recursos. Saiba [mais](../azure-monitor/vm/vminsights-overview.md).    |Produção    |Não    |
|Backup    |O Backup do Azure fornece backups independentes e isolados para proteger contra a destruição indesejada dos dados em suas VMs. Saiba [mais](../backup/backup-azure-vms-introduction.md). Os encargos são baseados no número e no tamanho das VMs que estão sendo protegidas. Saiba [mais](https://azure.microsoft.com/pricing/details/backup/).    |Produção    |Sim    |
|Central de Segurança do Azure    |A central de segurança do Azure é um sistema de gerenciamento de segurança de infraestrutura unificado que reforça a postura de segurança de seus data centers e fornece proteção avançada contra ameaças em suas cargas de trabalho híbridas na nuvem. Saiba [mais](../security-center/security-center-introduction.md).  O autogerenci irá configurar a assinatura em que sua VM reside para a oferta de camada gratuita da central de segurança do Azure. Se sua assinatura já estiver integrada à central de segurança do Azure, o autogerenciá-la não será reconfigurada.    |Produção, desenvolvimento/teste    |Não    |
|Gerenciamento de atualizações    |Você pode usar Gerenciamento de Atualizações na automação do Azure para gerenciar atualizações do sistema operacional para suas máquinas virtuais. Você pode rapidamente avaliar o status de atualizações disponíveis em todos os computadores de agente e gerenciar o processo de instalação das atualizações necessárias para os servidores. Saiba [mais](../automation/update-management/overview.md).    |Produção, desenvolvimento/teste    |Não    |
|Controle de Alterações inventário de &    |O Controle de Alterações e Inventário combinam as funções de controle de alterações e inventário para permitir que você rastreie as alterações de infraestrutura de máquina virtual e de servidor. O serviço dá suporte ao controle de alterações entre serviços, software de daemons, registro e arquivos em seu ambiente para ajudá-lo a diagnosticar alterações indesejadas e gerar alertas. O suporte a inventário permite consultar recursos no convidado para obter visibilidade sobre aplicativos instalados e outros itens de configuração.  Saiba [mais](../automation/change-tracking/overview.md).    |Produção, desenvolvimento/teste    |Não    |
|Configuração de convidado do Azure    | A política de configuração de convidado é usada para monitorar a configuração e o relatório sobre a conformidade do computador. O serviço de autogerenciamento instalará a linha de base do Linux do Azure usando a extensão de configuração de convidado. Para computadores Linux, o serviço de configuração de convidado instalará a linha de base no modo somente auditoria. Você poderá ver onde sua VM está fora de conformidade com a linha de base, mas a não conformidade não será corrigida automaticamente. Saiba [mais](../governance/policy/concepts/guest-configuration.md).    |Produção, desenvolvimento/teste    |Não    |
|Conta de Automação do Azure    |A Automação do Azure dá suporte ao gerenciamento durante todo o ciclo de vida de sua infraestrutura e de seus aplicativos. Saiba [mais](../automation/automation-intro.md).    |Produção, desenvolvimento/teste    |Não    |
|Workspace do Log Analytics    |O Azure Monitor armazena dados de log em um espaço de trabalho Log Analytics, que é um recurso do Azure e um contêiner em que os dados são coletados, agregados e servem como um limite administrativo. Saiba [mais](../azure-monitor/logs/design-logs-deployment.md).    |Produção, desenvolvimento/teste    |Não    |


<sup>1</sup> a seleção de ambiente está disponível quando você está habilitando o autogerenciamento. Saiba [mais](automanage-virtual-machines.md#environment-configuration). Você também pode ajustar as configurações padrão do ambiente e definir suas próprias preferências nas restrições de práticas recomendadas.


## <a name="next-steps"></a>Próximas etapas

Tente habilitar o autogerenci para máquinas virtuais no portal do Azure.

> [!div class="nextstepaction"]
> [Habilitar o autogerenci para máquinas virtuais no portal do Azure](quick-create-virtual-machines-portal.md)