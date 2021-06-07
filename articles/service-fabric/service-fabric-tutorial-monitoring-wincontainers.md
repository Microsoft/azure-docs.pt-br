---
title: Monitorar e diagnosticar contêineres do Windows
description: Neste tutorial, você configura logs do Azure Monitor para monitorar e diagnosticar os contêineres do Windows no Azure Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: b7689d6e259055137a8d1d3c61552790ab9f28d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588237"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-azure-monitor-logs"></a>Tutorial: Monitorar os contêineres do Windows no Service Fabric usando logs do Azure Monitor

Essa é a parte três de um tutorial e explica como configurar os logs do Azure Monitor para monitorar seus contêineres do Windows organizados no Service Fabric.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar logs do Azure Monitor para seu cluster do Service Fabric
> * Usar um espaço de trabalho do Log Analytics para exibir e consultar logs dos seus contêineres e nós
> * Configurar o agente do Log Analytics para selecionar métricas de contêiner e de nó

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deverá:

* Ter um cluster no Azure ou [criar um com este tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Implantar um aplicativo em contêineres nele](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-azure-monitor-logs-with-your-cluster-in-the-resource-manager-template"></a>Configurando os logs do Azure Monitor com o cluster no modelo do Resource Manager

Caso você tenha usado o [modelo fornecido](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) na primeira parte deste tutorial, ele deverá incluir as seguintes adições a um modelo genérico do Azure Resource Manager do Service Fabric. Caso você tenha um cluster próprio que esteja procurando configurar para monitorar contêineres com os logs do Azure Monitor:

* Faça as seguintes alterações em seu modelo do Resource Manager.
* Implante-o usando o PowerShell para atualizar seu cluster [implantando o modelo](./service-fabric-cluster-creation-via-arm.md). O Azure Resource Manager detecta que o recurso existe, então ele o lançará como uma atualização.

### <a name="adding-azure-monitor-logs-to-your-cluster-template"></a>Adição de logs do Azure Monitor para o modelo de cluster

Faça as seguintes alterações no seu *template.json*:

1. Adicione o local do espaço de trabalho do Log Analytics e um nome à sua seção *parâmetros*:

    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    Para alterar o valor usado para qualquer um, adicione os mesmos parâmetros ao seu *template.parameters.json* e altere os valores usados lá.

2. Adicione o nome da solução e a solução às suas *variáveis*:

    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Adicione o Microsoft Monitoring Agent como uma extensão da máquina virtual. Encontre recursos de conjuntos de dimensionamento de máquinas virtuais: *recursos* >  *"apiVersion": "[variables('vmssApiVersion')]"* . Em *propriedades* > *virtualMachineProfile* > *extensionProfile* > *extensões*, adicione a seguinte descrição de extensão na extensão *ServiceFabricNode*: 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Adicione o espaço de trabalho do Log Analytics como um recurso individual. Em *recursos*, depois do recurso dos conjuntos de dimensionamento de máquinas virtuais, adicione o seguinte:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

[Veja](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/d2ffa318581fc23ac7f1b0ab2b52db1a0d7b4ba7/5-VM-Windows-OMS-UnSecure/sfclusteroms.json) um modelo de exemplo (usado na parte um deste tutorial) que tem todas essas alterações que você pode referenciar conforme necessário. Essas alterações adicionarão um espaço de trabalho do Log Analytics ao grupo de recursos. O workspace será configurado para selecionar eventos de plataforma do Service Fabric das tabelas de armazenamento configurados com o agente [Diagnóstico do Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md). O agente do Log Analytics (Microsoft Monitoring Agent) também foi adicionado a cada nó no cluster como uma extensão de máquina virtual – isso significa que, enquanto você dimensiona seu cluster, o agente é configurado automaticamente em cada computador e conectado ao mesmo workspace.

Implante o modelo com suas novas alterações para atualizar seu cluster atual. Você poderá ver os recursos do Log Analytics no seu grupo de recursos quando tiver terminado. Quando o cluster estiver pronto, implante seu aplicativo em contêiner nele. Na próxima etapa, configuraremos o monitoramento dos contêineres.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Adicione a Solução de monitoramento de contêiner ao seu espaço de trabalho do Log Analytics

Para configurar a solução de contêiner no workspace, pesquise *Solução de monitoramento de contêiner* e crie um recurso de contêineres (na categoria Monitoramento + gerenciamento).

![Adicionando a solução de Contêineres](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Quando o *workspace do Log Analytics* for solicitado, selecione o espaço de trabalho que foi criado no seu grupo de recursos e selecione **Criar**. Isso adiciona uma *Solução de Monitoramento de Contêiner* ao seu workspace, iniciando o agente do Log Analytics implantado pelo modelo para começar a coletar estatísticas e logs do docker.

Navegue de volta para seu *grupo de recursos*, em que você deverá ver a solução de monitoramento recém-adicionada. Se você selecioná-la, a página de aterrissagem deverá mostrar o número de imagens de contêiner que você tem em execução.

*Observe que executamos cinco instâncias do contêiner fabrikam da [parte dois](service-fabric-host-app-in-a-container.md) do tutorial*

![Página de aterrissagem da solução do contêiner](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Selecionar a **Solução de Monitoramento de Contêiner** direcionará você a um painel mais detalhado, que permite rolar entre vários painéis, bem como executar consultas nos logs do Azure Monitor.

Como o agente está selecionando logs do Docker, o padrão é definido como mostrar *stdout* e *stderr*. Se você rolar horizontalmente, verá o inventário de imagens de contêiner, status, métricas e consultas de exemplo que você pode executar para obter mais dados úteis.

![Painel da solução do contêiner](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Clicar em qualquer um desses painéis direcionará você à consulta do Kusto que está gerando o valor exibido. Altere a consulta para *\** para ver todos os diferentes tipos de logs estão sendo selecionados. Aqui é possível consultar ou filtrar o desempenho do contêiner, os logs ou examinar os eventos de plataforma do Service Fabric. Seus agentes também estarão constantemente emitindo uma pulsação em cada nó que você poderá examinar para se certificar de que os dados estejam sendo coletados de todos os seus computadores se a configuração do seu cluster for alterada.

![Consulta do contêiner](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>Configurar o agente do Log Analytics para selecionar os contadores de desempenho

Outro benefício de usar o agente do Log Analytics é a capacidade de alterar os contadores de desempenho que você deseja selecionar por meio da experiência de interface do usuário do Log Analytics em vez de ter que configurar o agente de diagnóstico do Azure e fazer uma atualização baseada no modelo do Resource Manager todas as vezes. Para fazer isso, selecione **Workspace do OMS** na página de aterrissagem de sua solução de Monitoramento de Contêiner (ou do Service Fabric).

Isso direcionará você ao seu Espaço de Trabalho do Log Analytics, em que será possível exibir suas soluções, criar painéis personalizados, além de configurar o agente do Log Analytics. 
* Selecione **Configurações Avançadas** para abrir o menu Configurações Avançadas.
* Selecione **Fontes Conectadas** > **Servidores Windows** para verificar se você tem *5 Computadores Windows conectados*.
* Selecione **Dados** > **Contadores de Desempenho do Windows** para pesquisar e adicionar novos contadores de desempenho. Aqui você verá uma lista de recomendações dos logs do Azure Monitor para contadores de desempenho que você pode coletar, bem como a opção de pesquisar outros contadores. Verifique se os contadores **Processor(_Total)\% Tempo de Processador** e **Memory(*)\Available MBytes** estão sendo coletados.

**Atualize** sua Solução de monitoramento de contêiner em alguns minutos e você deverá ver os dados de *Desempenho do computador* entrando. Isso ajudará você a entender como os recursos estão sendo usados. Também é possível usar essas métricas para tomar decisões apropriadas sobre como dimensionar seu cluster ou para confirmar se um cluster está balanceando a carga conforme o esperado.

*Observação: certifique-se de que os filtros de tempo estão definidos adequadamente para você consumir essas métricas.*

![Contadores de desempenho 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Configurar logs do Azure Monitor para seu cluster do Service Fabric
> * Usar um espaço de trabalho do Log Analytics para exibir e consultar logs dos seus contêineres e nós
> * Configurar o agente do Log Analytics para selecionar métricas de contêiner e de nó

Agora que você configurou o monitoramento para seu aplicativo em contêineres, experimente:

* Configurar os logs do Azure Monitor para um cluster do Linux, seguindo etapas semelhantes que as deste tutorial. Referencie [este modelo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS) para fazer alterações no seu modelo do Resource Manager.
* Configure os logs do Azure Monitor para definir [alertas automatizados](../azure-monitor/alerts/alerts-overview.md) para auxiliar na detecção e no diagnóstico.
* Explorar a lista de [contadores de desempenho recomendados](service-fabric-diagnostics-event-generation-perf.md) do Service Fabric a serem configurados para seus clusters.
* Familiarize-se com os recursos de [pesquisa e consulta de logs](../azure-monitor/logs/log-query-overview.md) oferecidos como parte dos logs do Azure Monitor.
