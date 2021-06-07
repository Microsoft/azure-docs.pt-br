---
title: Práticas recomendadas do Azure Manage para máquinas virtuais
description: Saiba mais sobre as práticas recomendadas de gerenciamento automático do Azure para máquinas virtuais para serviços que são automaticamente integrados e configurados para você.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: 5cdc0080c0d8b06bd0b7e19566b54b4f105648e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122095"
---
# <a name="azure-automanage-for-virtual-machines-best-practices"></a>Práticas recomendadas do Azure Manage para máquinas virtuais


Esses serviços do Azure são automaticamente integrados para você quando você usa o autogerenci para máquinas virtuais. Eles são essenciais para nossas práticas recomendadas white paper, que você pode encontrar em nossa [estrutura de adoção de nuvem](/azure/cloud-adoption-framework/manage/azure-server-management).

Para todos esses serviços, faremos a integração automática, configuração automática, monitor para descompasso e mediação se a descompasso for detectada. Para saber mais sobre esse processo, confira [autogerenciar do Azure para máquinas virtuais](automanage-virtual-machines.md).


## <a name="participating-services"></a>Serviços participantes

|Serviço    |Descrição    |Perfis com suporte<sup>1</sup>    |Preferências com suporte<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|Monitoramento de informações de VM    |O Azure Monitor para VMs monitora o desempenho e a integridade de suas máquinas virtuais, incluindo seus processos e dependências em execução em outros recursos. Saiba [mais](../azure-monitor/vm/vminsights-overview.md).    |Práticas recomendadas de VM do Azure – produção    |Não    |
|Backup    |O Backup do Azure fornece backups independentes e isolados para proteger contra a destruição indesejada dos dados em suas VMs. Saiba [mais](../backup/backup-azure-vms-introduction.md). Os encargos são baseados no número e no tamanho das VMs que estão sendo protegidas. Saiba [mais](https://azure.microsoft.com/pricing/details/backup/).    |Práticas recomendadas de VM do Azure – produção    |Sim    |
|Central de Segurança do Azure    |A central de segurança do Azure é um sistema de gerenciamento de segurança de infraestrutura unificado que reforça a postura de segurança de seus data centers e fornece proteção avançada contra ameaças em suas cargas de trabalho híbridas na nuvem. Saiba [mais](../security-center/security-center-introduction.md).  O autogerenci irá configurar a assinatura em que sua VM reside para a oferta de camada gratuita da central de segurança do Azure. Se sua assinatura já estiver integrada à central de segurança do Azure, a autogerenciada não será reconfigurada.    |Práticas recomendadas de VM do Azure – produção, práticas recomendadas de VM do Azure – desenvolvimento/teste    |Não    |
|Microsoft Antimalware    |O Microsoft Antimalware para Azure é uma proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. Ele gera alertas quando um software mal-intencionado ou indesejado conhecido tenta se instalar ou executar nos sistemas do Azure. Saiba [mais](../security/fundamentals/antimalware.md). |Práticas recomendadas de VM do Azure – produção, práticas recomendadas de VM do Azure – desenvolvimento/teste    |Sim    |
|Gerenciamento de atualizações    |Você pode usar Gerenciamento de Atualizações na automação do Azure para gerenciar atualizações do sistema operacional para suas máquinas virtuais. Você pode rapidamente avaliar o status de atualizações disponíveis em todos os computadores de agente e gerenciar o processo de instalação das atualizações necessárias para os servidores. Saiba [mais](../automation/update-management/overview.md).    |Práticas recomendadas de VM do Azure – produção, práticas recomendadas de VM do Azure – desenvolvimento/teste    |Não    |
|Controle de Alterações inventário de &    |O Controle de Alterações e Inventário combinam as funções de controle de alterações e inventário para permitir que você rastreie as alterações de infraestrutura de máquina virtual e de servidor. O serviço dá suporte ao controle de alterações entre serviços, software de daemons, registro e arquivos em seu ambiente para ajudá-lo a diagnosticar alterações indesejadas e gerar alertas. O suporte a inventário permite consultar recursos no convidado para obter visibilidade sobre aplicativos instalados e outros itens de configuração.  Saiba [mais](../automation/change-tracking/overview.md).    |Práticas recomendadas de VM do Azure – produção, práticas recomendadas de VM do Azure – desenvolvimento/teste    |Não    |
|Configuração de convidado do Azure    | A política de configuração de convidado é usada para monitorar a configuração e o relatório sobre a conformidade do computador. O serviço de autogerenciamento instalará as [linhas de base de segurança do Windows](/windows/security/threat-protection/windows-security-baselines) usando a extensão de configuração de convidado. Saiba [mais](../governance/policy/concepts/guest-configuration.md).    |Práticas recomendadas de VM do Azure – produção, práticas recomendadas de VM do Azure – desenvolvimento/teste    |Não    |
|Conta de Automação do Azure    |A Automação do Azure dá suporte ao gerenciamento durante todo o ciclo de vida de sua infraestrutura e de seus aplicativos. Saiba [mais](../automation/automation-intro.md).    |Práticas recomendadas de VM do Azure – produção, práticas recomendadas de VM do Azure – desenvolvimento/teste    |Não    |
|Workspace do Log Analytics    |O Azure Monitor armazena dados de log em um espaço de trabalho Log Analytics, que é um recurso do Azure e um contêiner em que os dados são coletados, agregados e servem como um limite administrativo. Saiba [mais](../azure-monitor/logs/design-logs-deployment.md).    |Práticas recomendadas de VM do Azure – produção, práticas recomendadas de VM do Azure – desenvolvimento/teste    |Não    |


<sup>1</sup> perfis de configuração estão disponíveis quando você está habilitando o autogerenciamento. Saiba [mais](automanage-virtual-machines.md). Você também pode ajustar as configurações padrão do perfil de configuração e definir suas próprias preferências nas restrições de práticas recomendadas.


## <a name="next-steps"></a>Próximas etapas

Tente habilitar o autogerenci para máquinas virtuais no portal do Azure.

> [!div class="nextstepaction"]
> [Habilitar o autogerenci para máquinas virtuais no portal do Azure](quick-create-virtual-machines-portal.md)