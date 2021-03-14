---
title: Configurar o espaço de trabalho Log Analytics para o VM insights
description: Descreve como criar e configurar o espaço de trabalho de Log Analytics usado pelo virtual insights.
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: 5a0e04772cfc1c9be77c0ad8b32b0e93be8b9f54
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046713"
---
# <a name="configure-log-analytics-workspace-for-vm-insights"></a>Configurar o espaço de trabalho Log Analytics para o VM insights
As informações de VM coletam seus dados de um ou mais espaços de trabalho Log Analytics no Azure Monitor. Antes de realizar a integração de agentes, você deve criar e configurar um espaço de trabalho. Este artigo descreve os requisitos do espaço de trabalho e para configurá-lo para o virtual insights.

## <a name="overview"></a>Visão geral
Uma única assinatura pode usar qualquer número de espaços de trabalho, dependendo de suas necessidades. O único requisito do espaço de trabalho é que ele esteja localizado em um local com suporte e seja configurado com a solução *VMInsights* .

Depois que o espaço de trabalho tiver sido configurado, você poderá usar qualquer uma das opções disponíveis para instalar os agentes necessários na máquina virtual e no conjunto de dimensionamento de máquinas virtuais e especificar um espaço de trabalho para que eles enviem seus dados. As informações da VM coletarão dados de qualquer espaço de trabalho configurado em sua assinatura.

> [!NOTE]
> Quando você habilita as informações de VM em uma única máquina virtual ou conjunto de dimensionamento de máquinas virtuais usando o portal do Azure, você tem a opção de selecionar um espaço de trabalho existente ou criar um novo. A solução *VMInsights* será instalada nesse espaço de trabalho se ainda não estiver. Você pode usar esse espaço de trabalho para outros agentes.


## <a name="create-log-analytics-workspace"></a>Criar espaço de trabalho do Log Analytics

>[!NOTE]
>As informações descritas nesta seção também se aplicam à [solução de mapa do serviço](service-map.md). 

Acesse Log Analytics espaços de trabalho no portal do Azure no menu **log Analytics espaços de trabalho** .

[![Espaços de trabalho do Anlytics de log](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Você pode criar um novo espaço de trabalho Log Analytics usando qualquer um dos métodos a seguir. Consulte [projetando sua implantação de logs de Azure monitor](../logs/design-logs-deployment.md) para obter orientação sobre como determinar o número de espaços de trabalho que você deve usar em seu ambiente e como projetar sua estratégia de acesso.


* [Azure portal](../logs/quick-create-workspace.md)
* [CLI do Azure](../logs/quick-create-workspace-cli.md)
* [PowerShell](../logs/powershell-workspace-configuration.md)
* [Azure Resource Manager](../logs/resource-manager-workspace.md)

## <a name="supported-regions"></a>Regiões com suporte
As informações de VM dão suporte a um espaço de trabalho Log Analytics em qualquer uma das [regiões com suporte pelo log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all) , exceto para o seguinte:

- Centro-Oeste da Alemanha
- Coreia Central

>[!NOTE]
>Você pode monitorar as VMs do Azure em qualquer região. As VMs em si não são limitadas às regiões com suporte no espaço de trabalho Log Analytics.

## <a name="azure-role-based-access-control"></a>Controle de acesso baseado em função do Azure
Para habilitar e acessar os recursos no Revisions da VM, você deve ter a [função de colaborador de log Analytics](../logs/manage-access.md#manage-access-using-azure-permissions) no espaço de trabalho. Para exibir o desempenho, a integridade e os dados do mapa, você deve ter a [função leitor de monitoramento](../roles-permissions-security.md#built-in-monitoring-roles) para a VM do Azure. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, veja [Gerenciar espaços de trabalho](../logs/manage-access.md).

## <a name="add-vminsights-solution-to-workspace"></a>Adicionar a solução VMInsights ao espaço de trabalho
Antes que um espaço de trabalho Log Analytics possa ser usado com o VM insights, ele deve ter a solução *VMInsights* instalada. Os métodos para configurar o espaço de trabalho são descritos nas seções a seguir.

> [!NOTE]
> Quando você adiciona a solução *VMInsights* ao espaço de trabalho, todas as máquinas virtuais existentes conectadas ao espaço de trabalho começarão a enviar dados para o InsightsMetrics. Os dados para os outros tipos de dados não serão coletados até que você adicione o Dependency Agent às máquinas virtuais existentes conectadas ao espaço de trabalho.

### <a name="azure-portal"></a>Portal do Azure
Há três opções para configurar um espaço de trabalho existente usando o portal do Azure. Cada um deles está descrito abaixo.

Para configurar um único espaço de trabalho, acesse a opção **máquinas virtuais** no menu **Azure monitor** , selecione as **outras opções de integração** e, em seguida, **Configure um espaço de trabalho**. Selecione uma assinatura e um espaço de trabalho e clique em **Configurar**.

[![Configurar o workspace](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

Para configurar vários espaços de trabalho, selecione a guia **configuração do espaço de trabalho** no menu **máquinas virtuais** no menu **monitorar** da portal do Azure. Defina os valores de filtro para exibir uma lista de espaços de trabalho existentes. Selecione a caixa ao lado de cada espaço de trabalho para habilitar e clique em **Configurar selecionado**.

[![Configuração do workspace](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


Quando você habilita as informações de VM em uma única máquina virtual ou conjunto de dimensionamento de máquinas virtuais usando o portal do Azure, você tem a opção de selecionar um espaço de trabalho existente ou criar um novo. A solução *VMInsights* será instalada nesse espaço de trabalho se ainda não estiver. Você pode usar esse espaço de trabalho para outros agentes.

[![Habilitar VM única no portal](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Modelo do Resource Manager
Os modelos de Azure Resource Manager para as informações de VM são fornecidos em um arquivo morto (. zip) que você pode [baixar de nosso repositório GitHub](https://aka.ms/VmInsightsARMTemplates). Isso inclui um modelo chamado **ConfigureWorkspace** que configura um espaço de trabalho log Analytics para o VM insights. Você implanta esse modelo usando qualquer um dos métodos padrão, incluindo os comandos de exemplo do PowerShell e da CLI abaixo: 

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>Próximas etapas
- Confira [agentes integrados ao insights da VM](vminsights-enable-overview.md) para conectar agentes ao insights da VM.
- Confira [direcionamento de soluções de monitoramento em Azure monitor (versão prévia)](../insights/solution-targeting.md) para limitar a quantidade de dados enviados de uma solução para o espaço de trabalho.
