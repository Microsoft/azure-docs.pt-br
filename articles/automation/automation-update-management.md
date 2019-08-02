---
title: Solução Gerenciamento de Atualizações no Azure
description: Este artigo destina-se a ajudá-lo a entender como usar a solução de Gerenciamento de Atualizações do Azure para gerenciar atualizações para os computadores Windows e Linux.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4bd0b6f0652f49c16bd67bbca5a89d19e17a8b2c
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498414"
---
# <a name="update-management-solution-in-azure"></a>Solução Gerenciamento de Atualizações no Azure

Você pode usar a solução Gerenciamento de Atualizações na automação do Azure para gerenciar atualizações do sistema operacional para seus computadores Windows e Linux no Azure, em ambientes locais ou em outros provedores de nuvem. Você pode avaliar o status de atualizações disponíveis em todos os computadores de agente e gerenciar rapidamente o processo de instalação das atualizações necessárias para os servidores.

Você pode habilitar o Gerenciamento de Atualizações para máquinas virtuais diretamente da sua conta da Automação do Azure. Para saber como habilitar o Gerenciamento de Atualizações para máquinas virtuais de sua conta de automação, veja [Gerenciar atualizações para várias máquinas virtuais](manage-update-multi.md). Você também pode habilitar o Gerenciamento de Atualizações para uma máquina virtual da página de máquina virtual no portal do Azure. Esse cenário está disponível para máquinas virtuais do [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) e [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management).

> [!NOTE]
> A solução Gerenciamento de Atualizações requer a vinculação de um espaço de trabalho Log Analytics à sua conta de automação. Para obter uma lista definitiva de regiões com suporte, consulte [./How-to/Region-Mappings.MD]. Os mapeamentos de região não afetam a capacidade de gerenciar máquinas virtuais em uma região separada do que a sua conta de automação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Visão geral da solução

Os computadores que são gerenciados pelo Gerenciamento de Atualizações usam as configurações a seguir para realizar implantações de atualização e avaliação:

* Microsoft Monitoring Agent (MMA) para Windows ou Linux
* DSC (PowerShell Desired State Configuration) para Linux
* Hybrid Runbook Worker de Automação
* Microsoft Update ou Windows Server Update Services (WSUS) para computadores Windows

O diagrama a seguir mostra uma exibição conceitual do comportamento e do fluxo de dados, indicando como a solução avalia e aplica atualizações de segurança a todos os computadores Linux e servidores Windows conectados em um workspace:

![Fluxo do processo de Gerenciamento de Atualizações](./media/automation-update-management/update-mgmt-updateworkflow.png)

O Gerenciamento de Atualizações pode ser usado para integrar nativamente computadores em várias assinaturas no mesmo locatário.

Depois que um pacote é liberado, leva 2-3 horas para que o patch seja exibido para os computadores Linux para avaliação. Para máquinas Windows, leva de 12 a 15 horas para o patch ser exibido para avaliação após sua liberação.

Depois que um computador conclui uma verificação de conformidade de atualização, o agente encaminha as informações em massa para Azure Monitor logs. Em um computador Windows, a verificação de conformidade é executada a cada 12 horas por padrão.

Além do agendamento da verificação, a verificação de conformidade de atualizações será iniciada em 15 minutos se o MMA estiver sendo reiniciado antes da instalação da atualização e após a instalação da atualização.

Para um computador Linux, a verificação de conformidade é executada a cada hora por padrão. Se o agente MMA for reiniciado, uma verificação de conformidade é iniciada dentro de 15 minutos.

A solução relata o grau de atualização do computador com base na fonte com a qual você está configurado para realizar a sincronização. Se o computador do Windows estiver configurado para relatar para o WSUS, dependendo de quando o WSUS foi sincronizado pela última vez com o Microsoft Update, os resultados poderão diferir do que é mostrado pelo Microsoft Updates. Esse comportamento é o mesmo para computadores Linux configurados para relatar a um repositório local em vez de para um repositório público.

> [!NOTE]
> Para relatar adequadamente o serviço, o Gerenciamento de Atualizações requer que determinadas URLs e portas sejam habilitadas. Para saber mais sobre esses requisitos, consulte [Planejamento de rede para Hybrid Workers](automation-hybrid-runbook-worker.md#network-planning).

Você pode implantar e instalar atualizações de software em computadores que precisam de atualizações, criando uma implantação agendada. As atualizações classificadas como *Opcional* não são incluídas no escopo de implantação para computadores Windows. Somente as atualizações necessárias são incluídas no escopo de implantação.

A implantação agendada define quais computadores de destino recebem as atualizações aplicáveis, especificando explicitamente computadores ou selecionando um [grupo](../azure-monitor/platform/computer-groups.md) de computadores baseado em pesquisas de log de um conjunto específico de computadores ou uma [consulta do Azure](#azure-machines) Isso seleciona dinamicamente as VMs do Azure com base em critérios especificados. Esses grupos são diferentes da [configuração de escopo](../azure-monitor/insights/solution-targeting.md), que é usada apenas para determinar quais computadores obtêm os pacotes de gerenciamento que habilitam a solução.

Você também pode especificar uma agenda para aprovar e definir um período de tempo durante o qual as atualizações podem ser instaladas. Esse período de tempo é chamado de janela de manutenção. Dez minutos da janela de manutenção será reservado para reinicializações se uma reinicialização for necessária e você tiver selecionado a opção de reinicialização apropriada. Se a aplicação de patch demorar mais do que o esperado e houver menos de dez minutos na janela de manutenção, uma reinicialização não ocorrerá.

As atualizações são instaladas por runbooks na Automação do Azure. Você não consegue exibir esses runbooks e os runbooks não exigem nenhuma configuração. Quando uma implantação de atualizações é criada, a implantação de atualizações cria uma agenda que inicia um runbook de atualização mestre no momento especificado para os computadores incluídos. O runbook mestre inicia um runbook filho em cada agente para instalar as atualizações necessárias.

Na data e hora especificadas na implantação da atualização, os computadores de destino executam a implantação em paralelo. Antes da instalação, uma verificação é executada para verificar se as atualizações ainda são necessárias. Para computadores cliente WSUS, se as atualizações não forem aprovadas no WSUS, a implantação da atualização falhará.

Não há suporte para um computador registrado para Gerenciamento de Atualizações em mais de um espaço de trabalho Log Analytics (hospedagem múltipla).

## <a name="clients"></a>Clientes

### <a name="supported-client-types"></a>Tipos de clientes com suporte

A tabela a seguir mostra uma lista de sistemas operacionais com suporte:

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Suporta apenas avaliações de atualização.         |
|Windows Server 2008 R2 SP1 e posterior.  |É necessário o .NET Framework 4.5.1 ou posterior. ([Download do .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 ou posterior é necessário. ([Baixar WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1é recomendado para maior confiabilidade.  ([Baixar WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux devem ter acesso a um repositório de atualização. O patch baseado em classificação requer que o yum retorne dados de segurança que o CentOS não possui. Para obter mais informações sobre aplicação de patch com base em classificação no CentOS, consulte [Atualizar classificações no Linux](#linux-2)          |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS, e 18.04 (x86/x64)      |Os agentes do Linux devem ter acesso a um repositório de atualização.         |

> [!NOTE]
> Os conjuntos de dimensionamento de máquinas virtuais do Azure podem ser gerenciados com Gerenciamento de Atualizações. Gerenciamento de Atualizações funciona nas próprias instâncias e não na imagem base. Você precisará agendar as atualizações de forma incremental, como não atualizar todas as instâncias de VM ao mesmo tempo.

### <a name="unsupported-client-types"></a>Tipos de clientes sem suporte

A tabela a seguir lista os sistemas operacionais que não têm suporte:

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Client     | Os sistemas operacionais clientes (como Windows 7 e Windows 10) não têm suporte.        |
|Windows Server 2016 Nano Server     | Sem suporte.       |

### <a name="client-requirements"></a>Requisitos do cliente

#### <a name="windows"></a>Windows

Os agentes do Windows devem ser configurados para comunicarem-se com um servidor WSUS ou eles devem ter acesso ao Microsoft Update. Você pode usar o Gerenciamento de Atualizações com o System Center Configuration Manager. Para saber mais sobre cenários de integração, consulte [Integrar System Center Configuration Manager com Gerenciamento de Atualizações](oms-solution-updatemgmt-sccmintegration.md#configuration). O [agente do Windows](../azure-monitor/platform/agent-windows.md) é obrigatório. O agente será instalado automaticamente se você estiver instalando uma máquina virtual do Azure.

#### <a name="linux"></a>Linux

Para Linux, a máquina deve ter acesso a um repositório de atualização. O repositório de atualização pode ser público ou privado. TLS 1.1 ou TLS 1.2 é necessário para interagir com o gerenciamento de atualizações. Um Agente Log Analytics para Linux configurado para reportar a mais de um espaço de trabalho do Log Analytics não é suportado com esta solução.

Para obter informações sobre como instalar o agente de Log Analytics para Linux e baixar a versão mais recente, consulte [log Analytics Agent para Linux](https://github.com/microsoft/oms-agent-for-linux). Para obter informações sobre como instalar o agente do Log Analytics para Windows, consulte [Microsoft Monitoring Agent para Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Permissões

Para criar e gerenciar implantações de atualização, você precisa ter permissões específicas. Para saber mais sobre essas permissões, consulte [Acesso baseado em Função - Gerenciamento de Atualizações](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Componentes da solução

A solução consiste nos recursos a seguir. Os recursos são adicionados à sua conta de Automação. Eles são os agentes conectados diretamente ou de um grupo de gerenciamento conectados do Operations Manager.

### <a name="hybrid-worker-groups"></a>Grupos de Hybrid Worker

Depois que você habilita essa solução, qualquer computador com Windows conectado diretamente a seu espaço de trabalho do Log Analytics é automaticamente configurado como um Hybrid Runbook Worker para dar suporte aos runbooks incluídos nessa solução.

Cada computador Windows que é gerenciado pela solução é listado no painel de **grupos de trabalho Hybrid** como um **grupo de trabalho híbrido do Sistema** para a conta de Automação. As soluções usam a convenção de nomenclatura *Hostname FQDN_GUID*. Não é possível direcionar esses grupos com runbooks em sua conta. Elas falham se você tentar. Esses grupos devem dar suporte somente à solução de gerenciamento.

Você pode adicionar os computadores com Windows a um grupo de Hybrid Runbook Worker em sua conta de Automação para dar suporte a runbooks de automação se você usar a mesma conta para a solução e para a associação de grupo do Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

### <a name="management-packs"></a>Pacotes de gerenciamento

Se o grupo de gerenciamento do System Center Operations Manager estiver conectado a um espaço de trabalho do Log Analytics, os pacotes de gerenciamento a seguir serão instalados no Operations Manager. Esses pacotes de gerenciamento também são instalados em computadores com Windows conectados diretamente depois que você adicionar a solução. Você não precisa configurar ou gerenciar esses pacotes de gerenciamento.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* MP de Implantação de Atualizações

> [!NOTE]
> Se você tiver um grupo de gerenciamento Operations Manager 1807 com agentes configurados no nível do grupo de gerenciamento para ser associado a um espaço de trabalho, a solução alternativa atual para que eles apareçam é substituir **IsAutoRegistrationEnabled** como **true** no Regra **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Para obter mais informações sobre como os pacotes de gerenciamento de solução são atualizados, consulte [conectar Operations Manager a logs de Azure monitor](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Para sistemas com o agente do Operations Manager, para poder ser totalmente gerenciado pelo gerenciamento de atualizações, o agente precisa ser atualizado para o Microsoft Monitoring Agent. Para saber como atualizar o agente, consulte [como atualizar um agente do Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). Para ambientes que usam Operations Manager, é necessário que você esteja executando o System Center Operations Manager 2012 R2 UR 14 ou posterior.

## <a name="onboard"></a>Habilitar Gerenciamento de Atualizações

Para começar a aplicar patch em sistemas, você precisa habilitar a solução de Gerenciamento de Atualizações. Há muitas maneiras de integrar computadores ao Gerenciamento de Atualizações. A seguir estão as maneiras recomendadas e compatíveis de integrar a solução:

* [De uma máquina virtual](automation-onboard-solutions-from-vm.md)
* [Da navegação em várias máquinas](automation-onboard-solutions-from-browse.md)
* [Da sua conta de Automação](automation-onboard-solutions-from-automation-account.md)
* [Com um runbook de Automação do Azure](automation-onboard-solutions.md)

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Confirmar se computadores não Azure estão integrados

Para confirmar se os computadores conectados diretamente estão se comunicando com os logs de Azure Monitor, após alguns minutos, você pode executar uma das seguintes pesquisas de log.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Em um computador com Windows, você pode examinar as seguintes informações para verificar a conectividade do agente com os logs de Azure Monitor:

1. No painel de controle, abra o **Microsoft Monitoring Agent**. Na guia **Azure Log Analytics**, o agente exibirá a seguinte mensagem: **O Microsoft Monitoring Agent conectou-se com êxito ao Log Analytics**.
2. Abra o Log de Eventos do Windows. Navegue até **Logs de Aplicativos e Serviços\Operations Manager** e procure as IDs de Evento 3000 e 5002 do **Conector de Serviço** de origem. Esses eventos indicam que o computador foi registrado com o espaço de trabalho do Log Analytics e está recebendo a configuração.

Se o agente não puder se comunicar com os logs de Azure Monitor e o agente estiver configurado para se comunicar com a Internet por meio de um servidor proxy ou firewall, confirme se o firewall ou o servidor proxy está configurado corretamente. Para saber como verificar se o firewall ou o servidor proxy está configurado corretamente, veja [Configuração de rede para agente do Windows](../azure-monitor/platform/agent-windows.md) ou [Configuração de rede para agente do Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Se os seus sistemas Linux estiverem configurados para se comunicar com um proxy ou com o Log Analytics Gateway e você estiver integrando essa solução, atualize as permissões *proxy.conf* para conceder ao grupo omiuser permissão de leitura no arquivo usando os seguintes comandos:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Agentes do Linux recém-adicionados mostram um status de **Atualizado** após ter sido realizada uma avaliação. Esse processo pode levar até seis horas.

Para confirmar que um grupo de gerenciamento de Operations Manager está se comunicando com os logs de Azure Monitor, consulte [validar Operations Manager integração com os logs de Azure monitor](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

## <a name="data-collection"></a>Coleta de dados

### <a name="supported-agents"></a>Agentes com suporte

A seguinte tabela descreve as fontes conectadas que têm suporte nessa solução:

| Fonte conectada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |A solução coleta informações sobre atualizações do sistema de agentes do Windows e inicia a instalação de atualizações necessárias. |
| Agentes do Linux |Sim |A solução coleta informações sobre atualizações do sistema de agentes para Linux e, em seguida, inicia a instalação das atualizações necessárias nas distribuições com suporte. |
| Grupo de gerenciamento do Operations Manager |Sim |A solução coleta informações sobre atualizações do sistema de agentes em um grupo de gerenciamento conectados.<br/>Não é necessária uma conexão direta do agente de Operations Manager para os logs do Azure Monitor. Os dados são encaminhados do grupo de gerenciamento para o espaço de trabalho do Log Analytics. |

### <a name="collection-frequency"></a>Frequência de coleta

Uma verificação é executada duas vezes por dia para cada computador Windows gerenciado. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização para determinar se o status foi alterado. Se o status foi alterado, uma verificação de conformidade é iniciada.

Uma verificação é executada a cada hora para cada computador Linux gerenciado.

Pode demorar entre 30 minutos e 6 horas para o painel exibir os dados atualizados dos computadores gerenciados.

A Azure Monitor média registra o uso de dados de um computador usando Gerenciamento de Atualizações é de aproximadamente 25 MB por mês. Esse valor é somente uma aproximação e está sujeito a alterações com base em seu ambiente. É recomendável que você monitore seu ambiente para ver o uso exato que você tem.

## <a name="viewing-update-assessments"></a>Exibir avaliação de atualização

Na sua conta de Automação, selecione **Gerenciamento de Atualizações** para exibir o status de dos computadores.

Essa exibição fornece informações sobre os computadores, atualizações ausentes, implantações de atualização e implementações de atualização agendada. No **COLUNA DE CONFORMIDADE**, você pode ver a última vez em que a máquina foi avaliada. Na coluna **PREPARAÇÃO DO AGENTE DE ATUALIZAÇÃO**, você pode ver se a integridade do agente de atualização. Se houver um problema, selecione o link para ir para a documentação de solução problemas que pode ajudá-lo a conhecer as etapas necessárias para corrigir o problema.

Para executar uma pesquisa de logs sobre as informações do computador, atualização ou implantação, selecione o item na lista. O painel **Pesquisa de Logs** abre com uma consulta para o item selecionado:

![Exibição padrão do Gerenciamento de Atualizações](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Instalar as atualizações

Depois que as atualizações são avaliadas para todos os computadores com Windows e Linux em seu workspace, você pode instalar as necessárias atualizações, criando uma *implantação de atualizações*. Para criar uma implantação de atualização, você deve ter acesso de gravação à conta de automação e acesso de gravação para as VMs do Azure que são direcionadas na implantação. Uma implantação de atualizações é uma instalação agendada de atualizações necessárias para um ou mais computadores. Você especifica a data e hora para a implantação e um computador ou um grupo de computadores para incluir no escopo de uma implantação. Para saber mais sobre grupos de computadores, confira [grupos de computadores em logs de Azure monitor](../azure-monitor/platform/computer-groups.md).

Quando você inclui grupos de computadores em sua implantação de atualização, a associação de grupo é avaliada apenas uma vez no momento da criação da agenda. As alterações subsequentes em um grupo não são refletidas. Para contornar esse uso de [grupos dinâmicos](#using-dynamic-groups), esses grupos são resolvidos no momento da implantação e definidos por uma consulta para VMs do Azure ou uma pesquisa salva para VMs não Azure.

> [!NOTE]
> Máquinas virtuais do Windows que são implantadas no Azure Marketplace por padrão são definidas para receber atualizações automáticas do Serviço Windows Update. Esse comportamento não é alterado quando você adiciona essa solução ou adiciona máquinas virtuais do Windows para seu workspace. Se você não gerenciou atualizações ativamente usando essa solução, o comportamento padrão (para aplicar automaticamente as atualizações) é aplicado.

Para evitar atualizações aplicadas fora da janela de manutenção no Ubuntu, reconfigure o pacote de atualização automática para desabilitar as atualizações automáticas. Para saber mais sobre como configurar o pacote, veja [o tópico Atualizações automáticas no Guia do servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

As máquinas virtuais que foram criadas a partir das imagens do Red Hat Enterprise Linux (RHEL) sob demanda que estão disponíveis no Azure Marketplace são registradas para acessar a [Infraestrutura de Atualização do Red Hat (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) que é implantada no Azure. Qualquer distribuição do Linux deve ser atualizada nos repositórios de distribuição de arquivo online seguindo os métodos de distribuição com suporte.

Para criar uma nova implantação de atualização, selecione **Agendar implantação de atualização**. A página **nova implantação de atualizações** é aberta. Insira valores para as propriedades descritas na tabela a seguir e clique em **Criar**:

| Propriedade | Descrição |
| --- | --- |
| Nome |Nome exclusivo para identificar a Implantação de Atualizações. |
|Sistema operacional| Linux ou Windows|
| Grupos a serem atualizados |Para computadores do Azure, defina uma consulta com base em uma combinação de assinatura, grupos de recursos, localizações e marcas para criar um grupo dinâmico de VMs do Azure a ser incluído na implantação. </br></br>Para computadores que não são Azure, selecione uma pesquisa salva existente para selecionar um grupo de computadores que não são Azure a serem incluídos na implantação. </br></br>Para obter mais informações, consulte [grupos dinâmicos](automation-update-management.md#using-dynamic-groups)|
| Computadores para atualizar |Selecione uma pesquisa salva, um grupo importado ou selecione a máquina na lista suspensa e selecione máquinas individuais. Se você escolher **Machines**, a prontidão da máquina é mostrada na coluna **UPDATE AGENT READINESS**.</br> Para saber mais sobre os diferentes métodos de criação de grupos de computadores nos logs do Azure Monitor, veja [Grupos de computadores nos logs do Azure Monitor](../azure-monitor/platform/computer-groups.md) |
|Atualizar classificações|Selecione todas as classificações de atualização necessárias|
|Incluir/excluir atualizações|Isso abre o **incluir/excluir** página. As atualizações a serem incluídas ou excluídas estão em guias separadas. Para mais informações sobre como a inclusão é tratada, consulte o [comportamento de inclusão](automation-update-management.md#inclusion-behavior) |
|Configurações de agenda|Selecione o tempo para iniciar e selecione Uma Vez ou recorrente para a recorrência|
| Pré-scripts + pós-scripts|Selecione os scripts sejam executados antes e após sua implantação|
| Janela de manutenção |Número de minutos definido para atualizações. O valor não pode ser inferior a 30 minutos e não superior a 6 horas |
| Reinicialize o controle| Determina como as reinicializações devem ser tratadas. As opções disponíveis são:</br>Reinicialização, se necessário (Padrão)</br>Sempre reinicializar</br>Nunca reinicializar</br>Somente reinicialização - não instalará as atualizações|

As implantações de atualização também podem ser criadas programaticamente. Para aprender a criar uma Implantação de atualização com a API REST, consulte [Configurações de atualização de software - Criar](/rest/api/automation/softwareupdateconfigurations/create). Também é um exemplo de runbook que pode ser usado para criar uma implantação de atualização semanal. Para saber mais sobre este runbook, consulte [Criar uma implantação de atualização semanal para uma ou mais VMs em um grupo de recursos](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="multi-tenant"></a>Implantações de Atualização entre locatários

Se você tiver máquinas em outro locatário do Azure relatando ao Gerenciamento de Atualizações que você precisa aplicar patches, você precisará usar a seguinte solução alternativa para agendá-los. Use o cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) com a opção `-ForUpdate` para criar um agendamento e use o cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passe os computadores no outro locatário para o parâmetro `-NonAzureComputer`. O seguinte exemplo mostra um exemplo de como fazer isso:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="view-missing-updates"></a>Exibir atualizações ausentes

Selecione **Atualizações ausentes** para exibir a lista de atualizações que estão faltando nos computadores. Cada atualização é listada e pode ser selecionada. Informações sobre o número de computadores que exigem a atualização, o sistema operacional e um link para obter mais informações são mostradas. O painel de **Pesquisa de Log** mostra mais detalhes sobre as atualizações.

## <a name="view-update-deployments"></a>Exibir implantações de atualização

Selecione a guia **Atualizar Implantações** para exibir a lista das implantações com atualizações existentes. Selecione qualquer uma das implementações de atualização na tabela para abrir o painel **Atualizar Execução de Implantação** para essa implementação de atualização. Os logs de trabalho são armazenados por 30 dias, no máximo.

![Visão geral dos resultados da implantação de atualizações](./media/automation-update-management/update-deployment-run.png)

Para visualizar uma implementação de atualização da API REST, consulte [Execuções de configuração de atualização de software](/rest/api/automation/softwareupdateconfigurationruns).

## <a name="update-classifications"></a>Atualizar classificações

As tabelas a seguir listam as classificações de atualização no Gerenciamento de Atualizações, com uma definição de cada classificação.

### <a name="windows"></a>Windows

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Uma atualização para um problema específico que aborda um bug crítico não relacionado à segurança.        |
|Atualizações de segurança     | Uma atualização para um problema específico do produto relacionadas à segurança.        |
|Pacotes cumulativos de atualização     | Um conjunto cumulativo de hotfixes que são reunidos para facilitar a implantação.        |
|Feature packs     | Novos recursos do produto que são distribuídos fora de uma versão do produto.        |
|Service packs     | Um conjunto cumulativo de hotfixes que são aplicados a um aplicativo.        |
|Atualizações de definição     | Uma atualização para vírus ou outros arquivos de definição.        |
|Ferramentas     | Um utilitário ou recurso que ajuda a concluir uma ou mais tarefas.        |
|Atualizações     | Uma atualização para um aplicativo ou arquivo que está atualmente instalado.        |

### <a name="linux-2"></a>Linux

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas ou de segurança     | Atualizações para um problema específico ou um problema relacionado à segurança específico do produto.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas em atualizações de segurança ou de natureza.        |

Para o Linux, o Gerenciamento de Atualizações pode distinguir entre críticas e atualizações de segurança na nuvem ao exibir dados de avaliação devido a enriquecimento de dados na nuvem. Para aplicação de patch, o Gerenciamento de Atualizações se baseia em dados de classificação disponíveis no computador. Ao contrário de outras distribuições, CentOS não tem essas informações disponíveis fora da caixa. Se você tiver máquinas CentOS configuradas para retornar dados de segurança para o comando a seguir, o Gerenciamento de Atualizações poderá aplicar patch com base em classificações.

```bash
sudo yum -q --security check-update
```

Atualmente, não há nenhum método com suporte para habilitar a disponibilidade de dados nativos de classificação em CentOS. Neste momento, somente o suporte de melhor esforço é fornecido aos clientes que podem ter isso habilitado por conta própria.

## <a name="firstparty-predownload"></a>Configurações avançadas

O Gerenciamento de Atualizações se baseia no Windows Update para baixar e instalar Atualizações do Windows. Como resultado, respeitamos muitas das configurações usadas pelo Windows Update. Se você usar as configurações para habilitar atualizações que não sejam do Windows, o Gerenciamento de Atualizações também gerenciará essas atualizações. Se você quiser habilitar o download de atualizações antes que ocorra uma implantação de atualização, as implantações de atualização poderão ser mais rápidas e ter menos probabilidade de exceder a janela de manutenção.

### <a name="pre-download-updates"></a>Pré-download de atualizações

Para configurar automaticamente o download de atualizações na Política de Grupo, você pode definir a [configuração Configurar atualizações automáticas](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) como **3**. Isso baixa as atualizações necessárias em segundo plano, mas não as instala. Isso mantém o Gerenciamento de Atualizações no controle das agendas, mas permite que as atualizações sejam baixadas fora da janela de manutenção do Gerenciamento de Atualizações. Isso pode impedir erros de **Janela de manutenção excedida** no Gerenciamento de Atualizações.

Você também pode definir isso com o PowerShell. Execute o PowerShell a seguir em um sistema que você deseje baixar automaticamente as atualizações.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

### <a name="disable-automatic-installation"></a>Desabilitar instalação automática

As VMs do Azure têm a instalação automática de atualizações habilitadas por padrão. Isso pode fazer com que as atualizações sejam instaladas antes de você agendá-las para serem instaladas pelo Gerenciamento de Atualizações. Você pode desabilitar esse comportamento definindo a `NoAutoUpdate` chave do registro como. `1` O trecho do PowerShell a seguir mostra uma maneira de fazer isso.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

### <a name="enable-updates-for-other-microsoft-products"></a>Habilitar atualizações para outros produtos da Microsoft

Por padrão, apenas o Windows Update fornece atualizações para o Windows. Se você habilitar **fornecer atualizações para outros produtos da Microsoft ao atualizar o Windows**, você receberá atualizações para outros produtos, incluindo patches de segurança para SQL Server ou outros softwares de terceiros. Essa opção não pode ser configurada pela Política de Grupo. Execute o PowerShell a seguir nos sistemas em que você deseja habilitar outros patches internos, e o Gerenciamento de Atualizações obedecerá a essa configuração.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="third-party"></a> Patches de terceiros no Windows

Gerenciamento de Atualizações se baseia no repositório de atualização configurado localmente para corrigir os sistemas Windows com suporte. Este é o WSUS ou o Windows Update. Ferramentas como [System Center Updates Publisher](/sccm/sum/tools/updates-publisher
) (Updates Publisher) permitem que você publique atualizações personalizadas no WSUS. Esse cenário permite que Gerenciamento de Atualizações patch de máquinas que usam System Center Configuration Manager como seu repositório de atualizações com software de terceiros. Para saber como configurar o Updates Publisher, veja [Instalar o Updates Publisher](/sccm/sum/tools/install-updates-publisher).

## <a name="ports"></a>Planejamento de Rede

Os endereços a seguir são necessários especificamente para gerenciamento de atualizações. A comunicação para esses endereços ocorre pela porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.Azure automation.us|

Para obter mais informações sobre as portas que exige o Hybrid Runbook Worker, consulte [portas de função do Hybrid Worker](automation-hybrid-runbook-worker.md#hybrid-worker-role).

É recomendável usar os endereços listados ao definir exceções. Para endereços IP, baixe os [Intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Este arquivo é atualizado semanalmente e reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP, é postado semanalmente.

## <a name="search-logs"></a>Pesquisa da logs

Além dos detalhes fornecidos no portal do Azure, você pode fazer as pesquisas nos logs. Nas páginas de solução, selecione **Log Analytics**. O painel **Pesquisa de Log** é aberto.

Você também pode aprender a personalizar as consultas ou usá-las de clientes diferentes e mais visitando:  [Documentação da API de pesquisa do Log Analytics](
https://dev.loganalytics.io/).

### <a name="sample-queries"></a>Consultas de exemplo

As seções a seguir fornecem consultas de log de exemplo para registros de atualizações que são coletados por essa solução:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Consultas de Avaliação de VM única do Azure (Windows)

Substitua o valor VMUUID pelo GUID da VM da máquina virtual que você está consultando. Você pode encontrar o VMUUID que deve ser usado executando a seguinte consulta nos logs de Azure Monitor:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Resumo das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Lista das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>Consultas de avaliação de VM única do Azure (Linux)

Para alguns distribuições do Linux, há uma incompatibilidade de [endian](https://en.wikipedia.org/wiki/Endianness) com o valor de VMUUID que vem de Azure Resource Manager e o que é armazenado em logs de Azure monitor. A consulta a seguir procura uma correspondência em qualquer endianness. Substitua os valores VMUUID pelo formato big-endian e little-endian do GUID para retornar corretamente os resultados. Você pode encontrar o VMUUID que deve ser usado executando a seguinte consulta nos logs de Azure Monitor:`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Resumo das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Lista das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>Consultas de avaliação de várias VMs

##### <a name="computers-summary"></a>Resumo de computadores

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Resumo das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="computers-list"></a>Lista de computadores

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>Lista das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="using-dynamic-groups"></a>Usando grupos dinâmicos

Gerenciamento de Atualizações fornece a capacidade de direcionar um grupo dinâmico de VMs do Azure ou não Azure para implantações de atualização. Esses grupos são avaliados no momento da implantação para que você não precise editar sua implantação para adicionar computadores.

> [!NOTE]
> Você deve ter as permissões adequadas ao criar uma implantação de atualização. Para saber mais, consulte [instalar atualizações](#install-updates).

### <a name="azure-machines"></a>Computadores do Azure

Esses grupos são definidos por uma consulta, quando uma implantação de atualização é iniciada, os membros desse grupo são avaliados. Grupos dinâmicos não funcionam com VMs clássicas. Ao definir sua consulta, os seguintes itens podem ser usados juntos para preencher o grupo dinâmico

* Assinatura
* Grupos de recursos
* Localizações
* Marcas

![Selecionar grupos](./media/automation-update-management/select-groups.png)

Para visualizar os resultados de um grupo dinâmico, clique no botão **Visualizar**. Esta pré-visualização mostra a associação ao grupo nesse momento, neste exemplo, estamos pesquisando máquinas com a tag **A função** é igual a **BackendServer**. Se mais computadores tiverem essa marca adicionada, eles serão adicionados a quaisquer implantações futuras em relação a esse grupo.

![grupos da visualização](./media/automation-update-management/preview-groups.png)

### <a name="non-azure-machines"></a>Computadores não Azure

Para computadores não Azure, as pesquisas salvas também conhecidas como grupos de computadores são usadas para criar o grupo dinâmico. Para saber como criar uma pesquisa salva, consulte [criando um grupo de computadores](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Depois que o grupo for criado, você poderá selecioná-lo na lista de pesquisas salvas. Clique em **Visualizar** para visualizar os computadores na pesquisa salva naquele momento.

![Selecionar grupos](./media/automation-update-management/select-groups-2.png)

## <a name="integrate-with-system-center-configuration-manager"></a>Integração com o System Center Configuration Manager

Os clientes que investiram no System Center Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem da força e maturidade do Configuration Manager para ajudá-los a gerenciar as atualizações de software. Configuration Manager é parte de seu ciclo de gerenciamento de atualizações de software (SUM).

Para saber como integrar a solução de gerenciamento ao System Center Configuration Manager, veja [Integrar o System Center Configuration Manager ao Gerenciamento de Atualizações](oms-solution-updatemgmt-sccmintegration.md).

## <a name="inclusion-behavior"></a>Comportamento de inclusão

Atualizar inclusão permite que você especifique atualizações específicas para aplicar. Patches ou pacotes incluídos estão instalados. Quando Patches ou pacotes são incluídos e uma classificação também é selecionada, os itens incluídos e os itens que atendem à classificação são instalados.

É importante saber que as exclusões substituem as inclusões. Por exemplo, se você definir uma regra de exclusão de `*`, nenhuma correção ou pacote será instalado, pois todos serão excluídos. Correções excluídas ainda são mostradas como ausentes do computador. Para máquinas Linux, se um pacote estiver incluído, mas tiver um pacote dependente excluído, o pacote não será instalado.

## <a name="patch-linux-machines"></a>Aplicar patch em computadores Linux

As seções a seguir explicam possíveis problemas com aplicação de patch do Linux.

### <a name="unexpected-os-level-upgrades"></a>Atualizações de nível de sistema operacional inesperadas

Em algumas variantes do Linux, como o Red Hat Enterprise Linux, as atualizações no nível do SO podem ocorrer através de pacotes. Isso pode originar execuções de Gerenciamento de Atualizações onde o número de versão do SO é alterado. Como o Gerenciamento de Atualizações usa os mesmos métodos para atualizar pacotes que um administrador usaria localmente no computador Linux, esse comportamento é intencional.

Para evitar atualização da versão do SO por meio de execuções do Gerenciamento de Atualizações, utilize o recurso **Exclusão**.

No Red Hat Enterprise Linux, o nome do pacote a ser excluído seria: redhat-release-server.x86_64.

![Pacotes a serem excluídos do Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Patches críticos / de segurança não são aplicados

Ao implantar atualizações em um computador Linux, você poderá selecionar classificações de atualização. Isso filtra as atualizações aplicadas à máquina que atendem aos critérios especificados. Esse filtro é aplicado localmente no computador quando a atualização é implantada.

Como Gerenciamento de Atualizações executa o enriquecimento de atualização na nuvem, algumas atualizações podem ser sinalizadas em Gerenciamento de Atualizações como tendo impacto sobre a segurança, mesmo que o computador local não tenha essas informações. Como resultado, se você aplicar atualizações críticas a um computador Linux, pode haver atualizações, que não estão marcadas como tendo impacto de segurança nesse computador e as atualizações não são aplicadas.

No entanto, o Gerenciamento de Atualizações ainda pode relatar que o computador não é compatível porque possui informações adicionais sobre a atualização relevante.

Implantar atualizações por classificação de atualização não funciona no CentOS pronto para uso. Para implantar corretamente atualizações para CentOS, selecione todas as classificações para garantir que as atualizações sejam aplicadas. Para SUSE, selecionar *somente* 'Outras atualizações', já que a classificação pode resultar em que algumas atualizações de segurança também sejam instaladas se as atualizações de segurança relacionados ao zypper (gerenciamento de pacotes) ou suas dependências forem necessárias primeiro. Esse comportamento é uma limitação do zypper. Em alguns casos, talvez você precise executar novamente a implantação de atualização. Para verificar, veja o log de atualização.

## <a name="remove-a-vm-from-update-management"></a>Remover uma VM do Gerenciamento de Atualizações

Para remover uma VM do Gerenciamento de Atualizações:

* No espaço de trabalho do Log Analytics, remova a VM da pesquisa salva para a Configuração de Escopo `MicrosoftDefaultScopeConfig-Updates`. As pesquisas salvas podem ser encontradas em **Geral** no espaço de trabalho.
* Remover o [agente do Microsoft Monitoring](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou o [agente do Log Analytics para Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

Continue no tutorial para saber como gerenciar atualizações para as máquinas virtuais do Windows.

> [!div class="nextstepaction"]
> [Gerenciar atualizações e patches para VMs do Microsoft Azure](automation-tutorial-update-management.md)

* Use pesquisas de log em [logs de Azure monitor](../log-analytics/log-analytics-log-searches.md) para exibir dados de atualização detalhados.
* [Criar alertas](automation-tutorial-update-management.md#configure-alerts) para status de implantação de atualização.

* Para aprender a interagir com o Gerenciamento de Atualizações por meio da API REST, consulte [Configurações de atualização de software](/rest/api/automation/softwareupdateconfigurations)
* Para saber como solucionar problemas no seu Gerenciamento de Atualizações, consulte [Solucionar problemas no Gerenciador de Atualizações](troubleshoot/update-management.md)
