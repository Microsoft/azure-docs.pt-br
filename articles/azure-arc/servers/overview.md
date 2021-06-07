---
title: Visão geral de servidores habilitados para Azure Arc
description: Saiba como usar servidores habilitados para Azure Arc para gerenciar servidores hospedados fora do Azure como um recurso do Azure.
keywords: azure automation, DSC, powershell, desired state configuration, update management, change tracking, inventory, runbooks, python, graphical, hybrid
ms.date: 02/18/2021
ms.topic: overview
ms.openlocfilehash: 863cab073018c5a592cba6e94451a9af038ebd52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023326"
---
# <a name="what-is-azure-arc-enabled-servers"></a>O que são servidores habilitados para Azure Arc?

Os servidores habilitados para Azure Arc permitem que você gerencie servidores físicos e máquinas virtuais Windows e Linux hospedados *fora* do Azure, em sua rede corporativa ou em outro provedor de nuvem. Essa experiência de gerenciamento foi projetada para ser consistente com a maneira como você gerencia máquinas virtuais nativas do Azure. Quando um computador híbrido é conectado ao Azure, ele se torna um computador conectado e é tratado como um recurso no Azure. Cada computador conectado tem uma ID de Recurso, está incluído em um grupo de recursos e se beneficia de constructos padrão do Azure, como o Azure Policy e aplicação de marcações. Os provedores de serviços que gerenciam a infraestrutura local de um cliente podem gerenciar seus computadores híbridos, assim como eles fazem hoje com recursos nativos do Azure, em vários ambientes de clientes, usando o [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) com o Azure Arc.

Para oferecer essa experiência com seus computadores híbridos hospedados fora do Azure, o agente do Azure Connected Machine precisa ser instalado em cada computador que você planeja conectar ao Azure. Esse agente não oferece nenhuma outra funcionalidade e não substitui o [agente do Azure Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). O agente do Log Analytics para Windows e Linux é necessário quando você deseja monitorar proativamente o sistema operacional e as cargas de trabalho em execução no computador, o gerencia usando os runbooks de automação ou soluções como o Gerenciamento de Atualizações ou usa outros serviços do Azure como a [Central de Segurança do Azure](../../security-center/security-center-introduction.md).

## <a name="supported-scenarios"></a>Cenários com suporte

Quando você conecta seu computador a servidores habilitados para Azure Arc, ele habilita a capacidade de execução das seguintes tarefas de monitoramento e gerenciamento de configuração:

- Atribua [configurações de convidado do Azure Policy](../../governance/policy/concepts/guest-configuration.md) usando a mesma experiência que a atribuição de política para máquinas virtuais do Azure. Hoje, a maioria das políticas de Configuração de Convidado não aplicam configurações, elas só auditam as configurações dentro do computador. Para entender o custo de usar políticas de Configuração de Convidado do Azure Policy com servidores habilitados para Arc, confira o [guia de preços](https://azure.microsoft.com/pricing/details/azure-policy/) do Azure Policy.

- Relate as alterações de configuração sobre o software instalado, os serviços da Microsoft, os arquivos e o Registro Windows e os daemons do Linux em servidores monitorados usando o [Controle de Alterações e Inventário](../../automation/change-tracking/overview.md) e o [Monitoramento de Integridade de Arquivo da Central de Segurança do Azure](../../security-center/security-center-file-integrity-monitoring.md) da Automação do Azure para servidores habilitados com o [Azure Defender para servidores](../../security-center/defender-for-servers-introduction.md).

- Monitore o desempenho do sistema operacional convidado do computador conectado e descubra os componentes do aplicativo para monitorar seus processos e dependências com outros recursos com os quais o aplicativo se comunica usando o [Azure Monitor para VMs](../../azure-monitor/vm/vminsights-overview.md).

- Simplifique a implantação com outros serviços do Azure, como o [State Configuration](../../automation/automation-dsc-overview.md) da Automação do Azure e workspace do Log Analytics do Azure Monitor usando as [extensões de VM do Azure](manage-vm-extensions.md) com suporte para seu computador Windows ou Linux não Azure. Isso inclui a execução da instalação de software ou a configuração pós-implantação usando a Extensão de Script Personalizado.

- Usar o [Gerenciamento de Atualizações](../../automation/update-management/overview.md) na Automação do Azure para gerenciar as atualizações do sistema operacional nos servidores Windows e Linux

    > [!NOTE]
    > Neste momento, não há suporte para habilitar o Gerenciamento de Atualizações diretamente de um servidor habilitado para Arc. Confira [Habilitar o Gerenciamento de Atualizações em sua conta de automação](../../automation/update-management/enable-from-automation-account.md) para entender os requisitos e como habilitá-los para o servidor.

- Inclua seus servidores não Azure para detecção de ameaças e monitore proativamente possíveis ameaças à segurança usando a [Central de Segurança do Azure](../../security-center/security-center-introduction.md).

Os dados de log coletados e armazenados em um workspace do Log Analytics do computador híbrido agora contêm propriedades específicas do computador, como uma ID de Recurso. Isso pode ser usado para dar suporte ao acesso ao log no [contexto de recurso](../../azure-monitor/logs/design-logs-deployment.md#access-mode).

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Regiões com suporte

Para obter uma lista definitiva de regiões com suporte com servidores habilitados para o Azure Arc, confira a página [Produtos do Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

Na maioria dos casos, o local selecionado durante a criação do script de instalação deve ser a região do Azure mais próxima à localização do seu computador. Os dados em repouso são armazenados na geografia do Azure que contém a região especificada por você, o que também pode afetar sua preferência de região, caso você tenha requisitos de residência de dados. Se a região do Azure a que seu computador está conectado for afetada por uma interrupção, o computador conectado não será afetado, mas talvez não seja possível concluir as operações de gerenciamento que usam o Azure. Caso haja uma interrupção regional e várias localizações que dão suporte a um serviço com redundância geográfica, será melhor conectar os computadores em cada localização a uma região diferente do Azure.

As seguintes informações de metadados sobre o computador conectado são coletadas e armazenadas na região em que o recurso de computador do Azure Arc está configurado:

- Nome e versão do sistema operacional
- Nome do computador
- FQDN (nome de domínio totalmente qualificado) do computador
- Versão do agente do Connected Machine

Por exemplo, se o computador estiver registrado no Azure Arc na região Leste dos EUA, esses dados serão armazenados na região dos EUA.

### <a name="supported-environments"></a>Ambientes compatíveis

Os servidores habilitados para Arc dão suporte ao gerenciamento de servidores físicos e de máquinas virtuais hospedados *fora* do Azure. Para obter detalhes específicos sobre para quais ambientes de nuvem híbrida hospedam VMs há suporte, confira [Pré-requisitos do agente do computador conectado](agent-overview.md#supported-environments).

> [!NOTE]
> Os servidores habilitados para Arc não são projetados nem têm suporte para habilitar o gerenciamento de máquinas virtuais em execução no Azure.

### <a name="agent-status"></a>Status do agente

O agente do Connected Machine envia uma mensagem de pulsação regular para o serviço a cada 5 minutos. Se o serviço parar de receber essas mensagens de pulsação de um computador, esse computador será considerado offline e o status será alterado automaticamente para **Desconectado** no portal no período de 15 a 30 minutos. Ao receber uma mensagem de pulsação subsequente do agente do Connected Machine, o status dela será alterado automaticamente para **Conectado**.

## <a name="next-steps"></a>Próximas etapas

Antes de avaliar ou habilitar servidores habilitados para Arc em vários computadores híbridos, examine [Visão geral do agente do Computador Conectado](agent-overview.md) para entender os requisitos, os detalhes técnicos sobre o agente e os métodos de implantação.