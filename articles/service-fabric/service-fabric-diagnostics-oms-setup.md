---
title: Configurar o monitoramento com logs de Azure Monitor
description: Saiba como configurar logs de Azure Monitor para visualizar e analisar eventos para monitorar seus clusters de Service Fabric do Azure.
ms.topic: conceptual
ms.date: 02/20/2019
ms.openlocfilehash: 79dbc38f9d177bc3decf11673a68ce434f9a83bc
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625952"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Configurar logs de Azure Monitor para um cluster

Os logs do Azure Monitor é nossa recomendação para monitorar eventos de nível de cluster. É possível configurar um espaço de trabalho do Log Analytics por meio do Azure Resource Manager, PowerShell ou Azure Marketplace. Se você mantiver um modelo atualizado do Resource Manager de sua implantação para uso futuro, use o mesmo modelo para configurar o ambiente de logs de Azure Monitor. A implantação por meio do Marketplace será mais fácil se você já tiver um cluster implantado com o diagnóstico habilitado. Caso não tenha acesso ao nível de assinatura na conta na qual está implantando, faça a implantação usando o PowerShell ou o modelo do Resource Manager.

> [!NOTE]
> Para configurar logs de Azure Monitor para monitorar o cluster, você precisa ter o diagnóstico habilitado para exibir eventos em nível de cluster ou de plataforma. Consulte [como configurar o diagnóstico em clusters do Windows](service-fabric-diagnostics-event-aggregation-wad.md) e [como configurar o diagnóstico em clusters do Linux](service-fabric-diagnostics-oms-syslog.md) para obter mais informações

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Implantar um espaço de trabalho do Log Analytics usando o Azure Marketplace

Se você deseja adicionar um espaço de trabalho do Log Analytics depois de implantar um cluster, vá para o Azure Marketplace no portal e procure **Análise do Service Fabric**. Esta é uma solução personalizada para implantações do Service Fabric que tenha dados específicos do Service Fabric. Neste processo, você criará a solução (o painel para exibir informações) e o workspace (a agregação dos dados subjacentes do cluster).

1. Selecione **Novo** no menu de navegação esquerdo. 

2. Pesquise **Análise do Service Fabric**. Selecione o recurso que é exibido.

3. Selecione **Criar**.

    ![Análise do Service Fabric no Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Na janela de criação de Análise do Service Fabric, selecione **Selecionar um workspace** para o campo **Workspace do OMS** e, em seguida, **Criar um novo workspace**. Preencha as entradas necessárias. O único requisito é que a assinatura para o cluster do Service Fabric e o workspace devem ser iguais. Quando as entradas forem validadas, o workspace começará a ser implantado. A implantação leva apenas alguns minutos.

5. Quando terminar, selecione **Criar** novamente na parte inferior da janela de criação de Análise do Service Fabric. Verifique se o novo workspace será exibido em **Workspace do OMS**. Essa ação adiciona a solução ao workspace criado.

Se você estiver usando o Windows, continue com as etapas a seguir para conectar Azure Monitor logs à conta de armazenamento em que os eventos de cluster estão armazenados. 

>[!NOTE]
>Há suporte para a solução Análise do Service Fabric apenas para clusters do Windows. Para clusters do Linux, confira nosso artigo sobre [como configurar logs de Azure monitor para clusters do Linux](service-fabric-diagnostics-oms-syslog.md).  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Conecte-se ao espaço de trabalho do Log Analytics para o cluster 

1. O workspace precisa estar conectado aos dados de diagnóstico provenientes do seu cluster. Vá para o grupo de recursos em que você criou a solução Análise do Service Fabric. Selecione o **infabric \<nameOfWorkspace\>** e vá para sua página de visão geral. A partir daí, você pode alterar as configurações da solução,as configurações do espaço de trabalho e acessar o espaço de trabalho do Log Analytics.

2. No menu de navegação à esquerda, selecione **Logs das contas de armazenamento** em **Fontes de Dados de Workspace**.

3. Na página **Logs das contas de armazenamento**, selecione **Adicionar** na parte superior para adicionar os logs do cluster ao workspace.

4. Selecione **Conta de armazenamento** para adicionar a conta apropriada criada no cluster. Se você usou o nome padrão, a conta de armazenamento **será \<resourceGroupName\> sfdg**. Você também pode confirmar isso com o modelo do Azure Resource Manager usado para implantar o cluster verificando o valor usado para o **applicationDiagnosticsStorageAccountName**. Se o nome não aparecer, role para baixo e selecione **Carregar mais**. Selecione o nome da conta de armazenamento.

5. Especifique o tipo de dados. Defina-o como **Eventos do Service Fabric**.

6. Verifique se a Origem está definida automaticamente como **WADServiceFabric\*EventTable**.

7. Selecione **OK** para conectar o workspace aos logs do seu cluster.

    ![Adicionar logs de conta de armazenamento a logs de Azure Monitor](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

A conta agora aparece como parte dos seus logs de conta de armazenamento nas fontes de dados do seu workspace.

Você adicionou a solução Análise do Service Fabric em um espaço de trabalho do Log Analytics que agora está conectado corretamente à plataforma do cluster e à tabela de log do aplicativo. Você pode adicionar outras fontes ao workspace dessa mesma forma.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Implantar logs de Azure Monitor com Azure Resource Manager

Ao implantar um cluster usando um modelo do Gerenciador de Recursos, o modelo cria um novo espaço de trabalho do Log Analytics, adiciona a Solução Service Fabric a ele e o configura para ler dados das tabelas de armazenamento apropriadas.

Você pode usar e modificar [este modelo de exemplo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) para atender às suas necessidades. Esse modelo faz o seguinte

* Cria um cluster do Service Fabric de cinco nós
* Cria um espaço de trabalho do Log Analytics e a solução do Service Fabric
* Configura o agente do Log Analytics para coletar e enviar contadores de desempenho de exemplo 2 para o workspace
* Configura o WAD para coletar o Service Fabric e as envia-o para as tabelas de armazenamento do Azure (WADServiceFabric*EventTable)
* Configura o espaço de trabalho do Log Analytics para ler os eventos dessas tabelas


Você pode implantar o modelo como uma atualização do Resource Manager para o cluster usando a `New-AzResourceGroupDeployment` API no módulo Azure PowerShell. Um exemplo de comando pode ser:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

O Azure Resource Manager detecta que esse comando é uma atualização de um recurso existente. Ele processa somente as alterações entre o modelo que gera a implantação existente e o novo modelo fornecido.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Implantar logs de Azure Monitor com Azure PowerShell

Você também pode implantar o recurso do log Analytics por meio do PowerShell usando o `New-AzOperationalInsightsWorkspace` comando. Para usar esse método, verifique se o [Azure PowerShell](/powershell/azure/install-az-ps) está instalado. Use esse script para criar um novo espaço de trabalho do Log Analytics e adicionar a solução do Service Fabric a ele: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Quando terminar, siga as etapas na seção anterior para conectar Azure Monitor logs à conta de armazenamento apropriada.

Também é possível adicionar outras soluções ou fazer outras modificações no espaço de trabalho do Log Analytics usando o PowerShell. Para saber mais, consulte [gerenciar logs de Azure monitor usando o PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Próximas etapas
* [Implantar o Agente do Log Analytics](service-fabric-diagnostics-oms-agent.md) para os nós para coletar os contadores de desempenho e coletar logs e estatísticas do docker para seus contêineres
* Familiarize-se com os recursos de [pesquisa e consulta de logs](../azure-monitor/logs/log-query-overview.md) oferecidos como parte dos logs do Azure Monitor
* [Usar o designer de exibição para criar exibições personalizadas em logs de Azure Monitor](../azure-monitor/visualize/view-designer.md)
