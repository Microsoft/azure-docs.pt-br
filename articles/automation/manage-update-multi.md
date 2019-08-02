---
title: Gerenciar atualizações para várias máquinas virtuais do Azure
description: Este artigo descreve como gerenciar atualizações de máquinas virtuais do Azure.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0a4990673479c913777a5a7c410460d3d3b31264
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478326"
---
# <a name="manage-updates-for-multiple-machines"></a>Gerenciar atualizações de vários computadores

É possível usar a solução Gerenciamento de Atualizações para gerenciar atualizações e patches para as máquinas virtuais do Windows e Linux. A partir da sua conta da [Automação do Azure](automation-offering-get-started.md), é possível:

- Integrar máquinas virtuais
- Avaliar o status das atualizações disponíveis
- Agendar a instalação de atualizações necessárias
- Examinar os resultados de implantação para verificar se as atualizações foram aplicadas com êxito em todas as máquinas virtuais nas quais o Gerenciamento de Atualizações está habilitado

## <a name="prerequisites"></a>Pré-requisitos

Para usar o Gerenciamento de Atualizações, você precisa de:

- Uma máquina virtual ou um computador com um dos sistemas operacionais com suporte instalado.

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

O Gerenciamento de Atualizações dá suporte aos seguintes sistemas operacionais:

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Suporta apenas avaliações de atualização.         |
|Windows Server 2008 R2 SP1 e posterior     |Windows PowerShell 4.0 ou posterior é necessário. ([Baixar WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))</br> Windows PowerShell 5.1é recomendado para maior confiabilidade. ([Baixar WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Os agentes do Linux devem ter acesso a um repositório de atualização.         |

> [!NOTE]
> Para prevenir que atualizações sejam aplicadas fora de uma janela de manutenção no Ubuntu, reconfigure o pacote de atualização automática para desabilitar as atualizações automáticas. Para obter mais informações, consulte o [tópico de Atualizações automáticas no Guia do servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Os agentes do Linux devem ter acesso a um repositório de atualização.

Esta solução não oferece suporte a um Agente do Log Analytics para Linux configurado para gerar relatórios em vários espaços de trabalho do Log do Azure Analytics.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Habilitar o Gerenciamento de Atualizações para máquinas virtuais do Azure

No portal do Azure, abra a conta de Automação e selecione **Gerenciamento de Atualizações**.

Selecione **Adicionar VMs do Azure**.

![Adicione tab Azure VM](./media/manage-update-multi/update-onboard-vm.png)

Selecione uma máquina virtual a ser carregada. 

Em **Habilitar Gerenciamento de Atualizações**, selecione **Habilitar** para integrar a máquina virtual.

![Caixa de diálogo Habilitar Gerenciamento de Atualizações](./media/manage-update-multi/update-enable.png)

Quando a integração é concluída, o Gerenciamento de Atualizações é habilitado para a máquina virtual.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Habilitar o Gerenciamento de Atualizações para computadores e máquinas virtuais que não são Azure

Para saber como habilitar o gerenciamento de atualizações para computadores e máquinas de virtuais do Windows não - Azure, consulte [computadores Windows de se conectar ao serviço do Azure Monitor no Azure](../log-analytics/log-analytics-windows-agent.md).

Para saber como habilitar o gerenciamento de atualizações para computadores e máquinas virtuais do Linux não - Azure, consulte [conectar computadores Linux ao Azure Monitor logs](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Exibir computadores conectados à sua conta de Automação

Após habilitar o Gerenciamento de Atualizações para as máquinas, você poderá exibir as informações da máquina, selecionando **Computadores**. É possível exibir as informações sobre *nome do computador*, *status de conformidade*, *ambiente*, *tipo de SO*, *atualizações críticas e de segurança instaladas*, *outras atualizações instaladas* e *preparação para atualização do agente* dos computadores.

  ![Guia Exibir computadores](./media/manage-update-multi/update-computers-tab.png)

Computadores que foram habilitados recentemente para o Gerenciamento de Atualizações podem ainda não terem sido avaliados. O status do estado de conformidade para esses computadores é **Não avaliado**. Aqui, é apresentada uma lista de possíveis valores para o estado de conformidade:

- **Em conformidade**: Computadores com todas as atualizações críticas ou de segurança.

- **Sem conformidade**: Computadores que não têm pelo menos uma atualização crítica ou de segurança.

- **Não avaliado**: Os dados da avaliação de atualização não foram recebidos do computador dentro do período de tempo esperado. Para computadores Linux, o período de tempo esperado é na última hora. Para computadores Windows, o período de tempo esperado é nas últimas 12 horas.

Para exibir o status do agente, selecione o link na coluna **PREPARAÇÃO PARA ATUALIZAÇÃO DO AGENTE**. Selecionar essa opção abre o painel **Hybrid Worker** e exibe o status do Hybrid Worker. A imagem a seguir mostra um exemplo de um agente que não foi conectado ao Gerenciamento de Atualizações por um longo período de tempo:

![Guia Exibir computadores](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Exibir uma avaliação de atualização

Depois que o Gerenciamento de Atualizações for habilitado, o painel **Gerenciamento de Atualizações** será aberto. Você pode ver uma lista de atualizações ausentes na guia **Atualizações ausentes**.

## <a name="collect-data"></a>Coletar dados

Os agentes instalados em máquinas virtuais e computadores coletam dados sobre atualizações. Os agentes enviam os dados para o Gerenciamento de Atualizações do Azure.

### <a name="supported-agents"></a>Agentes com suporte

A tabela a seguir descreve as fontes conectadas às quais essa solução dá suporte:

| Fonte conectada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |O Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes do Windows e, em seguida, inicia a instalação das atualizações necessárias. |
| Agentes do Linux |Sim |O Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes para Linux e, em seguida, inicia a instalação das atualizações necessárias nas distribuições com suporte. |
| Grupo de gerenciamento do Operations Manager |Sim |O Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes em um grupo de gerenciamento conectado. |
| Conta de Armazenamento do Azure |Não |O Armazenamento do Microsoft Azure não inclui informações sobre atualizações do sistema. |

### <a name="collection-frequency"></a>Frequência de coleta

Após um computador de uma verificação de conformidade da atualização, o agente encaminha as informações em massa para logs do Azure Monitor. Em um computador Windows, a verificação de conformidade é executada a cada 12 horas por padrão.

Além do agendamento da verificação, a verificação de conformidade de atualizações será iniciada em 15 minutos se o MMA estiver sendo reiniciado antes da instalação da atualização e após a instalação da atualização.

Para um computador Linux, a verificação de conformidade é executada a cada hora por padrão. Se o agente MMA for reiniciado, uma verificação de conformidade é iniciada dentro de 15 minutos.

Pode demorar entre 30 minutos e 6 horas para o painel exibir os dados atualizados dos computadores gerenciados.

## <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Para instalar atualizações, agende uma implantação que alinhe-se com a agenda de liberação e período de serviço. Você pode escolher quais tipos de atualização deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

Para agendar uma nova implantação de atualização para uma ou mais máquinas virtuais, em **Gerenciamento de Atualizações**, selecione **Agendar implantação de atualizações**.

No painel **Nova implantação de atualização**, especifique as seguintes informações:

- **Nome**: Insira um nome exclusivo para identificar a implantação de atualizações.
- **Sistema operacional**: Selecione **Windows** ou **Linux**.
- **Grupos para atualizar (versão prévia)** : Defina uma consulta com base em uma combinação de assinatura, grupos de recursos, locais e tags para criar um grupo dinâmico de VMs do Azure para incluir em sua implantação. Para saber mais, consulte [Grupos dinâmicos](automation-update-management.md#using-dynamic-groups)
- **Computadores para atualizar**: Selecione uma pesquisa salva, um grupo importado ou selecione Máquinas, para escolher as máquinas que você deseja atualizar. Se você escolher **Machines**, a prontidão da máquina é mostrada na coluna **UPDATE AGENT READINESS**. É possível ver o estado de integridade do computador antes de agendar a implantação de atualização. Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos logs do Azure Monitor, veja [Grupos de computadores nos logs do Azure Monitor](../azure-monitor/platform/computer-groups.md)

  ![Painel da nova implantação de atualizações](./media/manage-update-multi/update-select-computers.png)

- **Classificação de atualização**: Selecione os tipos de software para incluir na implantação de atualização. Para obter uma descrição dos tipos de classificação, consulte [Classificações de atualização](automation-update-management.md#update-classifications). Os tipos de classificação são:
  - Atualizações críticas
  - Atualizações de segurança
  - Pacotes cumulativos de atualização
  - Feature packs
  - Service packs
  - Atualizações de definição
  - Ferramentas
  - Atualizações

- **Atualizações a serem incluídas/excluídas** – Isso abre a página **Incluir/Excluir**. As atualizações a serem incluídas ou excluídas estão em guias separadas. Para obter mais informações sobre como a inclusão é tratada, consulte [comportamento de inclusão](automation-update-management.md#inclusion-behavior)

- **Configurações da agenda**: Você pode aceitar a data e hora padrão, que é de 30 minutos após a hora atual. Também é possível especificar uma hora diferente.

   Você também pode especificar se a implantação ocorre uma única vez ou em um agendamento recorrente. Para configurar um agendamento recorrente, em **Recorrente**, selecione **Recorrência**.

   ![Caixa de diálogo Configurações de agendamento](./media/manage-update-multi/update-set-schedule.png)

- **Pré-scripts + pós-scripts**: Selecione os scripts a serem executados antes e depois de sua implantação. Para saber mais, consulte [Gerenciar pré e pós-scripts](pre-post-scripts.md).
- **Janela de manutenção (minutos)** : Especifique o período de tempo em que deseja que a implantação da atualização ocorra. Essa configuração ajuda a garantir que as alterações sejam executadas dentro das janelas de serviço definidas.

- **Reinicialize o controle** -essa configuração determina como as reinicializações são tratadas para a implantação de atualização.

   |Opção|DESCRIÇÃO|
   |---|---|
   |Reinicialização, se necessário| **(Padrão)** Se necessário, uma reinicialização será iniciada se a janela de manutenção permitir.|
   |Sempre reinicializar|Uma reinicialização for iniciada, independentemente se é necessário. |
   |Nunca reinicializar|Independentemente de uma reinicialização ser ou não necessária, as reinicializações são suprimidas.|
   |Somente reinicialização - não instalará as atualizações|Essa opção ignora a instalação de atualizações e apenas inicia uma reinicialização.|

Ao terminar de configurar o agendamento, selecione o botão **Criar** para retornar ao painel de status. A tabela **Agendado** mostra o agendamento de implantação que você criou.

> [!NOTE]
> O Gerenciamento de Atualizações dá suporte à implantação de atualizações próprias e patches baixados previamente. Isso requer alterações nos sistemas a corrigir, consulte [suporte próprio e de pré-download](automation-update-management.md#firstparty-predownload) para saber como definir essas configurações em seus sistemas.

## <a name="view-results-of-an-update-deployment"></a>Exibir resultados de uma implantação de atualização

Após o início da implantação agendada, você pode ver o status dessa implantação na guia **Implantações de atualização** em **Gerenciamento de Atualizações**.

Se a implantação estiver em execução no momento, seu status será **Em andamento**. Depois que a implementação for concluída com êxito, o status será alterado para **Êxito**.

Se uma ou mais atualizações falharem na implantação, o status será **Falhou parcialmente**.

![Status da implantação da atualização](./media/manage-update-multi/update-view-results.png)

Para ver o painel de uma implantação de atualização, selecione a implantação concluída.

O painel **Resultados da atualização** mostra o número total de atualizações e os resultados da implantação para a máquina virtual. A tabela à direita fornece uma análise detalhada de cada atualização e os resultados da instalação. Os resultados de instalação podem ser um dos seguintes valores:

- **Nenhuma tentativa**: A atualização não foi instalada pois não havia tempo suficiente disponível com base na janela de manutenção definida.
- **Êxito**: A atualização foi bem-sucedida.
- **Falha**: Falha na atualização.

Para ver todas as entradas de log que a implantação criou, selecione **Todos os logs**.

Para ver o fluxo de trabalho do runbook que gerencia a implantação de atualização na máquina virtual de destino, selecione o bloco de saída.

Para ver informações detalhadas sobre quaisquer erros da implantação, selecione **Erros**.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o Gerenciamento de Atualizações, incluindo logs, saída e erros, consulte [Solução Gerenciamento de Atualizações no Azure](../operations-management-suite/oms-solution-update-management.md).

