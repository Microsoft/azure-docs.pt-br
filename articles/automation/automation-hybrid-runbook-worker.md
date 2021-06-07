---
title: Visão geral do Hybrid Runbook Worker da Automação do Azure
description: Este artigo fornece uma visão geral do Hybrid Runbook Worker, que você pode usar para executar runbooks em computadores no seu datacenter ou provedor de nuvem local.
services: automation
ms.subservice: process-automation
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: c95ccb5ea1a23e8173d58bd3a18490e9b8e630e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581259"
---
# <a name="hybrid-runbook-worker-overview"></a>Visão geral do Hybrid Runbook Worker

Os runbooks na Automação do Azure talvez não tenham acesso aos recursos em outras nuvens ou no seu ambiente local por serem executados na plataforma de nuvem do Azure. Você pode usar o recurso Hybrid Runbook Worker da automação do Azure para executar runbooks diretamente no computador que está hospedando a função e em recursos no ambiente para gerenciar esses recursos locais. Os Runbooks são armazenados e gerenciados na automação do Azure e entregues a um ou mais computadores atribuídos.

## <a name="runbook-worker-types"></a>Tipos de trabalho do runbook

Há dois tipos de runbook Workers-System e User. A tabela a seguir descreve a diferença entre eles.

|Type | Descrição |
|-----|-------------|
|**System** |O oferece suporte a um conjunto de runbooks ocultos usados pelo recurso de Gerenciamento de Atualizações que foram criados para instalar atualizações especificadas pelo usuário em computadores Windows e Linux.<br> Esse tipo de Hybrid Runbook Worker não é um membro de um grupo de Hybrid Runbook Worker e, portanto, não executa runbooks direcionados a um grupo de runbook Worker. |
|**Usuário** |Dá suporte a runbooks definidos pelo usuário destinados a serem executados diretamente no computador Windows e Linux que são membros de um ou mais grupos do runbook Worker. |

Um Hybrid Runbook Worker pode ser executado no sistema operacional Windows ou Linux, e essa função depende do [agente de log Analytics](../azure-monitor/agents/log-analytics-agent.md) reportando para um espaço de [trabalho Azure monitor log Analytics](../azure-monitor/logs/design-logs-deployment.md). O espaço de trabalho não é apenas para monitorar o computador para o sistema operacional com suporte, mas também para baixar os componentes necessários para instalar o Hybrid Runbook Worker.

Quando a [Gerenciamento de atualizações](./update-management/overview.md) de automação do Azure estiver habilitada, qualquer computador conectado ao seu espaço de trabalho do log Analytics será automaticamente configurado como um Hybrid runbook Worker do sistema. Para configurá-lo como um usuário do Windows Hybrid Runbook Worker, consulte [implantar um windows Hybrid runbook Worker](automation-windows-hrw-install.md) e para Linux, consulte [implantar um Hybrid runbook Worker do Linux](automation-linux-hrw-install.md).

## <a name="how-does-it-work"></a>Como ele funciona?

![Visão geral do Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Cada usuário Hybrid Runbook Worker é um membro de um grupo de Hybrid Runbook Worker que você especifica ao instalar o trabalho. Um grupo pode incluir um único trabalho, mas você pode incluir vários trabalhadores em um grupo para alta disponibilidade. Cada computador pode hospedar um Hybrid Runbook Worker relatórios para uma conta de automação; Você não pode registrar o Hybrid Worker em várias contas de automação. Um Hybrid Worker só pode escutar trabalhos de uma única conta de automação. Para computadores que hospedam o Hybrid runbook Worker do sistema gerenciado pelo Gerenciamento de Atualizações, eles podem ser adicionados a um grupo de Hybrid Runbook Worker. Mas você deve usar a mesma conta de automação para Gerenciamento de Atualizações e a associação de grupo de Hybrid Runbook Worker.

Ao iniciar um runbook em um Hybrid Runbook Worker de usuário, você especifica o grupo em que ele é executado. Cada operador no grupo de sonda de automação do Azure para ver se todos os trabalhos estão disponíveis. Se um trabalho estiver disponível, o primeiro funcionário a obter o trabalho o fará. O tempo de processamento da fila de tarefas depende do perfil e da carga do hardware do trabalho híbrido. Você não pode especificar um trabalhador específico. O Hybrid Worker funciona em um mecanismo de sondagem (a cada 30 segundos) e segue uma ordem de primeiro a entrar, primeiro a servir. Dependendo de quando um trabalho foi enviado por push, qualquer trabalho híbrido efetua ping do serviço de automação seleciona o trabalho. Um único operador híbrido pode, em geral, pegar quatro trabalhos por ping (ou seja, a cada 30 segundos). Se a taxa de trabalhos de envio por push for maior que quatro por 30 segundos, haverá uma grande possibilidade de outro operador híbrido no grupo de Hybrid Runbook Worker que selecionou o trabalho.

Uma Hybrid Runbook Worker não tem muitos dos [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) de recursos de [área restrita do Azure](automation-runbook-execution.md#runbook-execution-environment) no espaço em disco, memória ou soquetes de rede. Os limites em um Hybrid Worker estão relacionados apenas aos recursos próprios do trabalho e não são limitados pelo limite de tempo de [compartilhamento justo](automation-runbook-execution.md#fair-share) que as áreas restritas do Azure têm.

Para controlar a distribuição de runbooks em Hybrid runbook Workers e quando ou como os trabalhos são disparados, você pode registrar o Hybrid Worker em diferentes grupos de Hybrid Runbook Worker dentro de sua conta de automação. Direcione os trabalhos para o grupo ou grupos específicos para dar suporte à sua organização de execução.

## <a name="hybrid-runbook-worker-installation"></a>Instalar Hybrid Runbook Worker

O processo para instalar um usuário Hybrid Runbook Worker depende do sistema operacional. A tabela a seguir define os tipos de implantação.

|Sistema operacional  |Tipo de Implantação  |
|---------|---------|
|Windows     | [Automatizado](automation-windows-hrw-install.md#automated-deployment)<br>[Manual](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Manual](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

O método de instalação recomendado para um computador Windows é usar um runbook de automação do Azure para automatizar completamente o processo de configurá-lo. Se isso não for viável, você poderá seguir um procedimento passo a passo para instalar e configurar manualmente a função. Para máquinas Linux, você executa um script Python para instalar o agente na máquina.

## <a name="network-planning"></a><a name="network-planning"></a>Planejamento de rede

Verifique a [configuração da rede de automação do Azure](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker) para obter informações detalhadas sobre as portas, URLs e outros detalhes de rede necessários para o Hybrid runbook Worker.

### <a name="proxy-server-use"></a>Uso do servidor proxy

Se você usar um servidor proxy para a comunicação entre a automação do Azure e as máquinas que executam o agente de Log Analytics, verifique se os recursos apropriados estão acessíveis. O tempo limite para solicitações dos serviços Hybrid Runbook Worker e Automação é de 30 segundos. Após três tentativas, a solicitação falha.

### <a name="firewall-use"></a>Uso de firewall

Se você usar um firewall para restringir o acesso à Internet, deverá configurar o firewall para permitir o acesso. Se estiver usando o gateway do Log Analytics como proxy, verifique se ele está configurado para Hybrid Runbook Workers. Consulte [Configurar o gateway de log Analytics para Hybrid runbook Workers de automação](../azure-monitor/agents/gateway.md).

### <a name="service-tags"></a>Marcas de serviço

A automação do Azure dá suporte a marcas de serviço de rede virtual do Azure, começando com a marca de serviço [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Você pode usar marcas de serviço para definir os controles de acesso à rede em [grupos de segurança de rede](../virtual-network/network-security-groups-overview.md#security-rules) ou no [Firewall do Azure](../firewall/service-tags.md). As marcas de serviço podem ser usadas no lugar de endereços IP específicos quando você cria regras de segurança. Ao especificar o nome da marca de serviço **GuestAndHybridManagement**  no campo de origem ou de destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço de automação. Essa marca de serviço não dá suporte à permissão de controle mais granular restringindo intervalos de IP para uma região específica.

A marca de serviço para o serviço de automação do Azure fornece somente IPs usados para os seguintes cenários:

* Disparar WebHooks de dentro de sua rede virtual
* Permitir que os Hybrid runbook Workers ou os agentes de configuração de estado em sua VNet se comuniquem com o serviço de automação

>[!NOTE]
>A marca de serviço **GuestAndHybridManagement** atualmente não dá suporte à execução de trabalho de runbook em uma área restrita do Azure, somente diretamente em um Hybrid runbook Worker.

## <a name="support-for-impact-level-5-il5"></a>Suporte para impacto nível 5 (IL5)

A Hybrid Runbook Worker de automação do Azure pode ser usada no Azure governamental para dar suporte a cargas de trabalho de impacto nível 5 em uma das duas seguintes configurações:

* [Máquina virtual isolada](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). Quando implantados, eles consomem todo o host físico para esse computador, fornecendo o nível necessário de isolamento necessário para dar suporte a cargas de trabalho do IL5.

* Os [hosts dedicados do Azure](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host), que fornecem servidores físicos capazes de hospedar uma ou mais máquinas virtuais, dedicados a uma assinatura do Azure.

>[!NOTE]
>O isolamento de computação por meio da função Hybrid Runbook Worker está disponível para nuvens comerciais do Azure e do governo dos EUA.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Endereços de Gerenciamento de Atualizações para Hybrid Runbook Worker

Além dos endereços padrão e das portas necessárias para o Hybrid Runbook Worker, Gerenciamento de Atualizações tem outros requisitos de configuração de rede descritos na seção [planejamento de rede](./update-management/overview.md#ports) .

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>State Configuration da Automação do Azure em um Hybrid Runbook Worker

Você pode executar o [State Configuration da Automação do Azure](automation-dsc-overview.md) em um Hybrid Runbook Worker. Para gerenciar a configuração de servidores que oferecem suporte ao Hybrid Runbook Worker, você deve adicionar os servidores como nós DSC. Confira [Habilitar computadores para gerenciamento pelo State Configuration da Automação do Azure](automation-dsc-onboarding.md).

## <a name="runbook-worker-limits"></a>Limites de trabalho do runbook

O número máximo de grupos de Hybrid Worker por conta de automação é 4000 e é aplicável para os trabalhos híbridos do sistema & usuário. Se você tiver mais de 4.000 computadores para gerenciar, é recomendável criar outra conta de automação.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks em um Hybrid Runbook Worker

Você pode ter runbooks que gerenciam recursos no computador local ou executados em recursos no ambiente local onde um usuário Hybrid Runbook Worker está implantado. Nesse caso, você pode optar por executar seus runbooks no trabalho híbrido em vez de em uma conta da Automação. Os runbooks executados em um Hybrid Runbook Worker têm estrutura idêntica àqueles executados na conta da Automação. Confira [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Trabalhos do Hybrid Runbook Worker

Os trabalhos do Hybrid Runbook Workers são executados na conta **Sistema** local no Windows ou na [conta nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) no Linux. A automação do Azure lida com trabalhos em Hybrid runbook Workers de forma diferente dos trabalhos executados em áreas restritas do Azure. Confira [Ambiente de execução de runbook](automation-runbook-execution.md#runbook-execution-environment).

Se o computador host do Hybrid Runbook Worker reiniciar, qualquer trabalho de runbook em execução será reiniciado desde o início ou do último ponto de verificação para runbooks do Fluxo de trabalho do PowerShell. Se um trabalho do runbook for reiniciado mais de três vezes, será suspenso.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Permissões de runbook para um Hybrid Runbook Worker

Como eles acessam recursos que não são do Azure, os runbooks em execução em um usuário Hybrid Runbook Worker não podem usar o mecanismo de autenticação normalmente usado por runbooks Autenticando nos recursos do Azure. Um runbook fornece sua própria autenticação aos recursos locais, ou configura a autenticação usando [entidades gerenciadas para os recursos do Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Você também pode especificar uma conta Executar como para fornecer um contexto de usuário a todos os runbooks.

## <a name="view-system-hybrid-runbook-workers"></a>Exibir Hybrid runbook Workers do sistema

Depois que o recurso de Gerenciamento de Atualizações estiver habilitado em computadores Windows ou Linux, você poderá inventariar a lista de grupos de Hybrid runbook Workers do sistema na portal do Azure. Você pode exibir até 2.000 trabalhadores no portal selecionando a guia grupo de **trabalhos híbridos do sistema** da opção **Hybrid Workers grupo** no painel esquerdo da conta de automação selecionada.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Página de grupos de trabalho híbridos do sistema de conta de automação" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Se você tiver mais de 2.000 trabalhadores híbridos, para obter uma lista de todos eles, poderá executar o seguinte script do PowerShell:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Para saber como solucionar problemas de seus Hybrid Runbook Workers, veja [Solucionar Problemas do Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general).
