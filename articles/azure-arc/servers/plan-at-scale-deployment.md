---
title: Como planejar uma implantação em escala de servidores habilitados para Arc do Azure
description: Saiba como habilitar um grande número de máquinas para servidores habilitados para Arc do Azure para simplificar a configuração de recursos essenciais de segurança, gerenciamento e monitoramento no Azure.
ms.date: 02/23/2021
ms.topic: conceptual
ms.openlocfilehash: 0e77fc00f94f2f46c60bb2c5dcecc10a4e2e3bc5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032221"
---
# <a name="planing-for-an-at-scale-deployment-of-azure-arc-enabled-servers"></a>Planejando uma implantação em escala de servidores habilitados para Arc do Azure

A implantação de um serviço de infraestrutura de ti ou aplicativo de negócios é um desafio para qualquer empresa. Para executá-lo bem e evitar surpresas indesejadas e custos não planejados, você precisa planejar completamente para ele para garantir que você esteja o mais pronto possível. Para planejar a implantação de servidores habilitados para Arc do Azure em escala, ele deve abranger os critérios de design e implantação que precisam ser atendidos para concluir com êxito as tarefas para dar suporte a uma implantação em escala.

Para que a implantação continue sem problemas, seu plano deve estabelecer uma compreensão clara de:

* Funções e responsabilidades.
* Inventário de servidores físicos ou máquinas virtuais para verificar se eles atendem aos requisitos de rede e de sistema.
* O conjunto de habilidades e o treinamento necessários para habilitar a implantação bem-sucedida e o gerenciamento contínuo.
* Critérios de aceitação e como você controla seu sucesso.
* Ferramentas ou métodos a serem usados para automatizar as implantações.
* Riscos identificados e planos de mitigação para evitar atrasos, interrupções etc.
* Como evitar a interrupção durante a implantação.
* Qual é o caminho de escalonamento quando ocorre um problema significativo?

A finalidade deste artigo é garantir que você esteja preparado para uma implantação bem-sucedida de servidores habilitados para Arc do Azure em vários servidores físicos de produção ou máquinas virtuais em seu ambiente.

## <a name="prerequisites"></a>Pré-requisitos

* Seus computadores executam um [sistema operacional com suporte](agent-overview.md#supported-operating-systems) para o agente do computador conectado.
* Seus computadores têm conectividade de sua rede local ou de outro ambiente de nuvem para recursos no Azure, seja diretamente ou por meio de um servidor proxy.
* Para instalar e configurar o agente de computador conectado de servidores habilitados para Arc, uma conta com privilégios elevados (ou seja, um administrador ou como raiz) nos computadores.
* Para os computadores de integração, você é membro da função **Integração do Azure Connected Machine**.
* Para ler, modificar e excluir um computador, você é membro da função de **administrador de recursos do computador conectado do Azure** .

## <a name="pilot"></a>Piloto

Antes de implantar em todas as máquinas de produção, comece avaliando esse processo de implantação antes de adotá-lo em larga escala em seu ambiente. Para um piloto, identifique uma amostra representativa de computadores que não são essenciais para a capacidade de suas empresas de realizar negócios. Você deve ter certeza de que deseja ter tempo suficiente para executar o piloto e avaliar seu impacto: recomendamos um mínimo de 30 dias.

Estabeleça um plano formal que descreva o escopo e os detalhes do piloto. Veja a seguir um exemplo do que um plano deve incluir para ajudar você a começar.

* Objetivos-descreve os drivers técnicos e de negócios que levaram à decisão de que um piloto é necessário.
* Critérios de seleção-especifica os critérios usados para selecionar quais aspectos da solução serão demonstrados por meio de um piloto.
* Escopo-descreve o escopo do piloto, que inclui, mas não se limita a componentes da solução, agendamento antecipado, duração do piloto e número de máquinas para o destino.
* Critérios de sucesso e métricas – defina os critérios de sucesso do piloto e as medidas específicas usadas para determinar o nível de sucesso.
* Plano de treinamento – descreve o plano para treinar engenheiros de sistema, administradores, etc. que são novos no Azure e serviços de ti durante o piloto.
* Plano de transição-descreve a estratégia e os critérios usados para orientar a transição do piloto para a produção.
* Rollback-descreve os procedimentos para reverter um piloto para o estado de pré-implantação.
* Riscos – liste todos os riscos identificados para condução do piloto e associados à implantação de produção.

## <a name="phase-1-build-a-foundation"></a>Fase 1: criar uma base

Nesta fase, os engenheiros de sistema ou os administradores habilitam os principais recursos da assinatura do Azure de suas organizações para iniciar a base antes de habilitar seus computadores para gerenciamento por servidores habilitados para Arc e outros serviços do Azure.

|Tarefa |Detalhe |Duration |
|-----|-------|---------|
| [Criar um grupo de recursos](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Um grupo de recursos dedicado para incluir apenas servidores habilitados para Arc e centralizar o gerenciamento e o monitoramento desses recursos. | Uma hora |
| Aplique [marcas](../../azure-resource-manager/management/tag-resources.md) para ajudar a organizar computadores. | Avalie e desenvolva uma estratégia de [marcação](/cloud-adoption-framework/decision-guides/resource-tagging/) alinhada a ti que pode ajudar a reduzir a complexidade de gerenciar seus servidores habilitados para Arc e simplificar a tomada de decisões de gerenciamento. | Um dia |
| Criar e implantar [logs de Azure monitor](../../azure-monitor/logs/data-platform-logs.md) | Avalie [considerações de design e implantação](../../azure-monitor/logs/design-logs-deployment.md) para determinar se sua organização deve usar um existente ou implementar outro espaço de trabalho log Analytics para armazenar dados de log coletados de servidores e máquinas híbridas. <sup>1</sup> | Um dia |
| [Desenvolver um plano de governança de Azure Policy](../../governance/policy/overview.md) | Determine como você implementará a governança de servidores híbridos e computadores na assinatura ou no escopo do grupo de recursos com o Azure Policy. | Um dia |
| Configurar RBAC ( [controle de acesso baseado em função](../../role-based-access-control/overview.md) ) | Desenvolva um plano de acesso para controlar quem tem acesso para gerenciar servidores habilitados para Arc e a capacidade de exibir seus dados de outros serviços e soluções do Azure. | Um dia |
| Identificar computadores com o agente de Log Analytics já instalado | Execute a seguinte consulta de log em [log Analytics](../../azure-monitor/logs/log-analytics-overview.md) para dar suporte à conversão de implantações de log Analytics Agent existentes para o agente gerenciado por extensão:<br> Pulsação <br> &#124; onde TimeGenerated > atrás (30D) <br> &#124; em que ResourceType = = "Machines" e (ComputerEnvironment = = "non-Azure") <br> &#124; resumir por computador, resourceprovider, ResourceType, ComputerEnvironment | Uma hora |

<sup>1</sup> uma consideração importante como parte da avaliação de seu design de log Analytics espaço de trabalho, é a integração com a automação do Azure para dar suporte ao seu gerenciamento de atualizações e ao recurso de controle de alterações e inventário, bem como à central de segurança do Azure e ao Azure Sentinel. Se sua organização já tiver uma conta de automação e tiver habilitado seus recursos de gerenciamento vinculados a um espaço de trabalho Log Analytics, avalie se você pode centralizar e simplificar as operações de gerenciamento, bem como minimizar o custo, usando esses recursos existentes em vez de criar uma conta duplicada, um espaço de trabalho etc.

## <a name="phase-2-deploy-arc-enabled-servers"></a>Fase 2: implantar servidores habilitados para Arc

Em seguida, adicionamos à Fundação disposta na fase 1, preparando a implantação e executando a instalação do agente.

|Tarefa |Detalhe |Duration |
|-----|-------|---------|
| Baixar o script de instalação predefinido | Examine e personalize o script de instalação predefinido para a implantação em escala do agente do computador conectado para dar suporte aos seus requisitos de implantação automatizados.<br><br> Exemplo de recursos de integração em escala:<br><br> <ul><li> [Script de implantação básica em escala](onboard-service-principal.md)</ul></li> <ul><li>[Integração em escala VMware vSphere VMs do Windows Server](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/vmware_scaled_powercli_win.md)</ul></li> <ul><li>[Integração em escala VMware vSphere VMs Linux](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/vmware_scaled_powercli_linux.md)</ul></li> <ul><li>[Integração em escala AWS instâncias de EC2 usando o Ansible](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/aws_scale_ansible.md)</ul></li> <ul><li>[Implantação em escala usando a comunicação remota do PowerShell](https://docs.microsoft.com/azure/azure-arc/servers/onboard-powershell) (somente Windows)</ul></li>| Um ou mais dias, dependendo dos requisitos, dos processos organizacionais (por exemplo, alteração e Release Management) e do método de automação usado. |
| [Criar uma entidade de serviço](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Crie uma entidade de serviço para conectar computadores de forma não interativa usando Azure PowerShell ou no Portal.| Uma hora |
| Implantar o agente do computador conectado nos servidores e máquinas de destino |Use sua ferramenta de automação para implantar os scripts em seus servidores e conectá-los ao Azure.| Um ou mais dias, dependendo do seu plano de versão, e se estiver seguindo uma distribuição em fases. |

## <a name="phase-3-manage-and-operate"></a>Fase 3: gerenciar e operar

A fase 3 vê os administradores ou engenheiros de sistema que permitem a automação de tarefas manuais para gerenciar e operar o agente de computador conectado e a máquina durante o ciclo de vida deles.

|Tarefa |Detalhe |Duration |
|-----|-------|---------|
|Criar um alerta Resource Health |Se um servidor parar de enviar pulsações para o Azure por mais de 15 minutos, isso pode significar que está offline, a conexão de rede foi bloqueada ou o agente não está em execução. Desenvolva um plano para como você responderá e investigará esses incidentes e usará [Resource Health alertas](../..//service-health/resource-health-alert-monitor-guide.md) para ser notificado quando eles forem iniciados.<br><br> Especifique o seguinte ao configurar o alerta:<br> **Tipo**  =  de recurso **Servidores habilitados para Arc do Azure**<br> Status do recurso **atual**  =  **Não disponível**<br> Status do recurso **anterior**  =  **Disponível** | Uma hora |
|Criar um alerta do supervisor do Azure | Para obter a melhor experiência e correções de bugs e segurança mais recentes, é recomendável manter o agente de servidores habilitados para Arc do Azure atualizado. Agentes desatualizados serão identificados com um [alerta do supervisor do Azure](../../advisor/advisor-alerts-portal.md).<br><br> Especifique o seguinte ao configurar o alerta:<br> **Tipo**  =  de recomendação **Atualizar para a versão mais recente do agente do computador conectado do Azure** | Uma hora |
|[Atribuir políticas do Azure](../../governance/policy/assign-policy-portal.md) à sua assinatura ou ao escopo do grupo de recursos |Atribua a [política](../../azure-monitor/vm/vminsights-enable-policy.md) **habilitar Azure monitor para VMs** (e outras que atendam às suas necessidades) à assinatura ou ao escopo do grupo de recursos. Azure Policy permite atribuir definições de política que instalam os agentes necessários para Azure Monitor para VMs em seu ambiente.| Varia |
|[Habilitar Gerenciamento de Atualizações para seus servidores habilitados para Arc](../../automation/update-management/enable-from-automation-account.md) |Configure Gerenciamento de Atualizações na automação do Azure para gerenciar atualizações do sistema operacional para suas máquinas virtuais Windows e Linux registradas com servidores habilitados para Arc. | 15 minutos |

## <a name="next-steps"></a>Próximas etapas

* Informações de solução de problemas podem ser encontradas no [guia solucionar problemas do agente do computador conectado](troubleshoot-agent-onboard.md).

* Saiba como simplificar a implantação com outros serviços do Azure, como a [configuração de estado](../../automation/automation-dsc-overview.md) da automação do Azure e outras [extensões de VM do Azure](manage-vm-extensions.md)com suporte.