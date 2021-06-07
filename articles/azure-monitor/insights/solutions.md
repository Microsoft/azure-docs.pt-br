---
title: Soluções de monitoramento no Azure Monitor | Microsoft Docs
description: As soluções de monitoramento no Azure Monitor são uma coleção de regras de lógica, visualização e aquisição de dados que fornecem métricas centradas em torno de uma área específica do problema.  Este artigo fornece informações sobre como instalar e usar soluções de monitoramento.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/16/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: a63db154d055675b834e2949a330375633a5761d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728579"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Soluções de monitoramento no Azure Monitor

As soluções de monitoramento no Azure Monitor fornecem análise da operação de um determinado aplicativo ou serviço do Azure. Este artigo fornece uma breve visão geral das soluções de monitoramento no Azure e detalhes sobre como utilizá-las e instalá-las. É possível adicionar soluções de monitoramento ao Azure Monitor para qualquer aplicativo e serviço usado. Normalmente, estão disponíveis gratuitamente, mas coletam dados que podem invocar encargos de uso.

## <a name="use-monitoring-solutions"></a>Usar soluções de monitoramento

A página **visão geral** de soluções no Azure Monitor exibe um bloco para cada solução instalada em um espaço de trabalho log Analytics. Para abrir essa página, vá para **Azure monitor** na [portal do Azure](https://ms.portal.azure.com). No menu **insights** , selecione **mais** para abrir o **Hub de informações** e, em seguida, clique em **log Analytics espaços de trabalho**.

[![Hub de informações](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


Use as caixas suspensas na parte superior da tela para alterar o workspace ou o intervalo de tempo usado para os blocos. Clique no bloco para uma solução abrir a exibição dela que inclui uma análise mais detalhada dos dados coletados.

[![Captura de tela mostra o menu portal do Azure com soluções selecionadas e soluções exibidas no painel soluções.](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

As soluções de monitoramento podem conter vários tipos de recursos do Azure e é possível exibir todos os recursos incluídos em uma solução como qualquer outro recurso. Por exemplo, todas as consultas de log incluídas na solução são listadas em **consultas de solução** no [Gerenciador de consultas](../logs/log-analytics-tutorial.md). Você pode usar essas consultas ao executar análises ad hoc com [consultas de log](../logs/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Listar soluções de monitoramento instaladas

### <a name="portal"></a>[Portal](#tab/portal)

Use o procedimento a seguir para listar as soluções de monitoramento instaladas na assinatura.

1. Vá para o [Portal do Azure](https://ms.portal.azure.com). Pesquise e selecione **Soluções**.
1. As soluções instaladas em todos os workspaces estão listadas. O nome da solução é seguido pelo nome do workspace no qual ela está instalada.
1. Use as caixas suspensas na parte superior da tela para filtrar por assinatura ou grupo de recursos.

![Listar todas as soluções](media/solutions/list-solutions-all.png)

Clique no nome de uma solução para abrir a página de resumo. Essa página exibe todas as exibições incluídas na solução e fornece opções diferentes para a própria solução e seu workspace. Visualize a página de resumo de uma solução usando um dos procedimentos acima para listar as soluções e, em seguida, clique no nome da solução.

![Propriedades da solução](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando [AZ monitor log-Analytics Solution List](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-list) para listar as soluções de monitoramento instaladas em sua assinatura.   Antes de executar o `list` comando, siga os pré-requisitos encontrados em [instalar uma solução de monitoramento](#install-a-monitoring-solution).

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Use o cmdlet [Get-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) para listar as soluções de monitoramento instaladas em sua assinatura. Antes de executar esses comandos, siga os pré-requisitos encontrados em [instalar uma solução de monitoramento](#install-a-monitoring-solution).

```azurepowershell-interactive
# List all log-analytics solutions in the current subscription.
Get-AzMonitorLogAnalyticsSolution

# List all log-analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all log-analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>Instalar uma solução de monitoramento

### <a name="portal"></a>[Portal](#tab/portal)

As soluções de monitoramento da Microsoft e parceiros estão disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com). Você pode procurar soluções disponíveis e instalá-las usando o procedimento a seguir. Ao instalar uma solução, você deverá selecionar um [Espaço de Trabalho do Log Analytics](../logs/manage-access.md) no qual a solução será instalada e onde seus dados serão coletados.

1. Na [lista de soluções para a assinatura](#list-installed-monitoring-solutions), clique em **Adicionar**.
1. Procure ou pesquise uma solução. Você também pode procurar soluções [neste link de pesquisa](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Localize a solução de monitoramento desejada e leia sua descrição.
1. Clique em **Criar** para iniciar o processo de instalação.
1. Quando o processo de instalação iniciar, você deverá especificar o workspace do Log Analytics e fornecer a configuração necessária para a solução.

![Instalar uma solução](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalar uma solução a partir da comunidade

Os membros da comunidade podem enviar soluções de gerenciamento para Modelos de Início Rápido do Azure. É possível instalar essas soluções diretamente ou baixá-las para instalação posterior.

1. Siga o processo descrito no [Espaço de Trabalho do Log Analytics e na Conta de automação](#log-analytics-workspace-and-automation-account) para vincular um espaço de trabalho e uma conta.
2. Acesse os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
3. Pesquise uma solução na qual você esteja interessado.
4. Selecione a solução nos resultados para exibir seus detalhes.
5. Clique no botão **Implantar no Azure**.
6. Você será solicitado a fornecer informações, como o grupo de recursos e o local, além dos valores de quaisquer parâmetros na solução.
7. Clique em **Comprar** para instalar a solução.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="prepare-your-environment"></a>Prepare o seu ambiente

1. Instalar a CLI do Azure

   Você precisa [instalar o CLI do Azure](/cli/azure/install-azure-cli) antes de executar comandos de referência da CLI.  Se preferir, você também pode usar Azure Cloud Shell para concluir as etapas neste artigo.  O Azure Cloud Shell é um ambiente de shell interativo usado por meio do navegador.  Inicie o Cloud Shell usando um destes métodos:

   - Abra o Cloud Shell acessando [https://shell.azure.com](https://shell.azure.com)

   - Selecione o botão **Cloud Shell** na barra de menus, no canto superior direito do [portal do Azure](https://portal.azure.com)

1. Entrar.

   Se você estiver usando uma instalação local da CLI, entre usando o comando [AZ login](/cli/azure/reference-index#az-login) .  Siga as etapas exibidas em nosso terminal para concluir o processo de autenticação.

    ```azurecli
    az login
    ```

1. Instalar a extensão `log-analytics-solution`

   O `log-analytics-solution` comando é uma extensão experimental do CLI do Azure principal. Saiba mais sobre referências de extensão na [extensão de uso com CLI do Azure](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   O seguinte aviso é esperado.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Instalar uma solução com o CLI do Azure

Ao instalar uma solução, você deverá selecionar um [Espaço de Trabalho do Log Analytics](../logs/manage-access.md) no qual a solução será instalada e onde seus dados serão coletados.  Com o CLI do Azure, você gerencia espaços de trabalho usando os comandos [AZ monitor log-Analytics Workspace](/cli/azure/monitor/log-analytics/workspace) Reference.  Siga o processo descrito no [Espaço de Trabalho do Log Analytics e na Conta de automação](#log-analytics-workspace-and-automation-account) para vincular um espaço de trabalho e uma conta.

Use a [solução AZ monitor log-Analytics Create](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution) para instalar uma solução de monitoramento.  Os parâmetros entre colchetes são opcionais.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

Aqui está um exemplo de código que cria uma solução de análise de log para o produto de plano de OMSGallery/contêineres.

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>Prepare o seu ambiente

1. Instalar o Azure PowerShell

   Você precisa [instalar o Azure PowerShell](/powershell/azure/install-az-ps) antes de executar Azure PowerShell comandos de referência. Se preferir, você também pode usar Azure Cloud Shell para concluir as etapas neste artigo. O Azure Cloud Shell é um ambiente de shell interativo usado por meio do navegador. Inicie o Cloud Shell usando um destes métodos:

   - Abra o Cloud Shell acessando [https://shell.azure.com](https://shell.azure.com)

   - Selecione o botão **Cloud Shell** na barra de menus, no canto superior direito do [portal do Azure](https://portal.azure.com)

   > [!IMPORTANT]
   > Enquanto o módulo **AZ. MonitoringSolutions** do PowerShell está em versão prévia, você deve instalá-lo separadamente usando o `Install-Module` cmdlet. Depois que esse módulo do PowerShell estiver em disponibilidade geral, ele fará parte das versões futuras do módulo Az PowerShell e estará disponível por padrão no Azure Cloud Shell.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. Entrar.

   Se você estiver usando uma instalação local do PowerShell, entre usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Siga as etapas exibidas no PowerShell para concluir o processo de autenticação.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Instalar uma solução com Azure PowerShell

Ao instalar uma solução, você deverá selecionar um [Espaço de Trabalho do Log Analytics](../logs/manage-access.md) no qual a solução será instalada e onde seus dados serão coletados. Com Azure PowerShell, você gerencia espaços de trabalho usando os cmdlets no módulo [AZ. MonitoringSolutions](/powershell/module/az.monitoringsolutions) do PowerShell. Siga o processo descrito no [Espaço de Trabalho do Log Analytics e na Conta de automação](#log-analytics-workspace-and-automation-account) para vincular um espaço de trabalho e uma conta.

Use o cmdlet [New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) para instalar uma solução de monitoramento. Os parâmetros entre colchetes são opcionais.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

O exemplo a seguir cria uma solução monitorar log Analytics para o espaço de trabalho do log Analytics.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Espaço de Trabalho do Log Analytics e Conta de automação

Todas as soluções de monitoramento exigem um [espaço de trabalho do Log Analytics](../logs/manage-access.md) para armazenar dados coletados pela solução e hospedar as exibições e pesquisas de logs. Algumas soluções também exigem uma [conta de Automação](../../automation/automation-security-overview.md) para conter runbooks e recursos relacionados. O workspace e a conta devem atender aos seguintes requisitos.

* Cada instalação de uma solução somente pode usar um espaço de trabalho do Log Analytics e uma conta de Automação. É possível instalar a solução separadamente em vários workspaces.
* Se uma solução exigir uma conta de Automação, o espaço de trabalho do Log Analytics e a conta de Automação deverão estar vinculadas entre si. Um espaço de trabalho do Log Analytics só pode ser vinculado a uma Conta de automação e uma Conta de automação só pode ser vinculada a um espaço de trabalho do Log Analytics.

Ao instalar uma solução por meio do Azure Marketplace, você será solicitado a fornecer um espaço de trabalho e uma conta de automação. Se ainda não estiverem vinculados, o link entre eles será criado.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verifique o link entre um espaço de trabalho do Log Analytics e uma conta de Automação

Você pode verificar o vínculo entre um espaço de trabalho do Log Analytics e uma Conta de automação usando o procedimento a seguir.

1. Selecione a conta de automação no portal do Azure.
1. Role até a seção **Recursos Relacionados** do menu e selecione **Workspace vinculado**.
1. Se o **Workspace** estiver vinculado a uma conta de Automação, essa página listará o workspace ao qual está vinculada. Se você selecionar o nome do workspace listado, será redirecionado à página de visão geral desse workspace.

## <a name="remove-a-monitoring-solution"></a>Remover uma solução de monitoramento

### <a name="portal"></a>[Portal](#tab/portal)

Para remover uma solução instalada usando o portal, localize-a na [lista de soluções instaladas](#list-installed-monitoring-solutions). Clique no nome da solução para abrir a página de resumo e, em seguida, clique em **Excluir**.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para remover uma solução instalada usando o CLI do Azure, use o comando [AZ monitor log-Analytics Solution](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-delete) .

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Para remover uma solução instalada usando Azure PowerShell, use o cmdlet [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) .

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>Próximas etapas

* Obter uma [lista de soluções de monitoramento da Microsoft](../monitor-reference.md).
* Saiba como [criar consultas](../logs/log-query-overview.md) para analisar dados coletados por soluções de monitoramento.
* Consulte todos os [comandos CLI do Azure para Azure monitor](/cli/azure/azure-cli-reference-for-monitor).