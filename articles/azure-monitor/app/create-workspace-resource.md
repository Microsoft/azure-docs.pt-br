---
title: Criar um novo recurso baseado em espaço de trabalho do Azure Monitor Application Insights | Microsoft Docs
description: Saiba mais sobre as etapas necessárias para habilitar os novos recursos baseados em espaço de trabalho do Azure Monitor Application Insights.
ms.topic: conceptual
ms.date: 10/06/2020
ms.openlocfilehash: 3ec0b25a24af13b29a3568165009f8a6d66e0218
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578335"
---
# <a name="workspace-based-application-insights-resources"></a>Recursos baseados em workspace do Application Insights

Os recursos baseados em espaço de trabalho dão suporte à integração completa entre Application Insights e Log Analytics. Agora você pode optar por enviar sua telemetria do Application Insights para um espaço de trabalho comum do Log Analytics, que permite acesso completo a todos os recursos do Log Analytics mantendo os logs de aplicativos, infraestrutura e plataforma em um único local consolidado.

Isso também permite o controle de acesso baseado em função (RBAC) comum do Azure em seus recursos e elimina a necessidade de consultas entre aplicativos/espaços de trabalho.

> [!NOTE]
> A ingestão de dados e a retenção de recursos do Application Insights baseados em espaço de trabalho são cobradas por meio do espaço de trabalho do Log Analytics onde ficam os dados. [Saiba mais]( ./pricing.md#workspace-based-application-insights) sobre a cobrança de recursos do Application Insights baseados em espaço de trabalho.

## <a name="new-capabilities"></a>Novos recursos

Os Application Insights baseados em espaço de trabalho permitem aproveitar os recursos mais recentes de Azure Monitor e Log Analytics, incluindo:

* [As chaves gerenciadas pelo cliente (CMK)](../logs/customer-managed-keys.md) fornecem criptografia em repouso para seus dados com chaves de criptografia somente às quais você tem acesso.
* O [Link Privado do Azure](../logs/private-link-security.md) permite vincular com segurança os serviços PaaS do Azure a sua rede virtual usando pontos de extremidade privados.
* [Traga seu próprio armazenamento (BYOS) para o criador de perfil e depurador de instantâneos](./profiler-bring-your-own-storage.md) oferece controle total sobre a política de criptografia em repouso, a política de gerenciamento de tempo de vida e o acesso à rede para todos os dados associados a Application Insights Profiler e depurador de instantâneos. 
* As [camadas de reserva de capacidade](../logs/manage-cost-storage.md#pricing-model) permitem que você economize até 25% em comparação com o preço pago conforme o uso. 
* Ingestão de dados mais rápida por meio de Log Analytics ingestão de streaming.

## <a name="create-workspace-based-resource"></a>Criar recurso baseado em espaço de trabalho

Entre no [portal do Azure](https://portal.azure.com) e crie um recurso do Application Insights:

> [!div class="mx-imgBorder"]
> ![Recurso baseado em espaço de trabalho do Application Insights](./media/create-workspace-resource/create-workspace-based.png)

Se você ainda não tiver um espaço de trabalho do Log Analytics, [confira a documentação de criação de espaço de trabalho do Log Analytics](../logs/quick-create-workspace.md).

**Os recursos baseados em espaço de trabalho estão disponíveis atualmente em todas as regiões comerciais e Azure governamental**

Depois que o recurso for criado, você verá as informações de espaço de trabalho correspondentes no painel **Visão Geral**:

![Nome do espaço de trabalho](./media/create-workspace-resource/workspace-name.png)

Clique no texto do link azul para ir para o espaço de trabalho do Log Analytics associado, onde você poderá aproveitar o novo ambiente unificado de consulta de espaço de trabalho.

> [!NOTE]
> Ainda fornecemos compatibilidade total com versões anteriores para as consultas de recursos clássicos, as pastas de trabalho e os alertas baseados em log do Application Insights na experiência do Application Insights. Para consultar/exibir em relação à [nova estrutura/esquema de tabela baseada em espaço de trabalho](apm-tables.md), você deverá primeiro navegar para seu espaço de trabalho do Log Analytics. A seleção de **logs (análise)** de dentro dos painéis de Application insights dará acesso à experiência de consulta de Application insights clássica.

## <a name="copy-the-connection-string"></a>Copiar a cadeia de conexão

A [cadeia de conexão](./sdk-connection-string.md?tabs=net) identifica o recurso com o qual você deseja associar os dados de telemetria. Ele também permite que você modifique os pontos de extremidade que o recurso usará como um destino para a telemetria. Você precisará copiar a cadeia de conexão e adicioná-la ao código do aplicativo ou a uma variável de ambiente.

## <a name="monitoring-configuration"></a>Configuração do monitoramento

Depois que um recurso baseado em espaço de trabalho do Application Insights tiver sido criado, a configuração do monitoramento será relativamente simples.

### <a name="code-based-application-monitoring"></a>Monitoramento de aplicativos baseado em código

Para o monitoramento de aplicativos baseados em código, basta instalar o SDK do Application Insights apropriado e apontá-lo para a chave de instrumentação ou a cadeia de conexão para o recurso recém-criado.  

Para obter uma documentação detalhada sobre como configurar um SDK do Application Insights para o monitoramento baseado em código, confira a documentação específica da linguagem/estrutura:

- [ASP.NET](./asp-net.md)
- [ASP.NET Core ](./asp-net-core.md)
- [Tarefas em segundo plano e aplicativos de console modernos (.NET/.NET Core)](./worker-service.md)
- [Aplicativos de console clássicos (.NET)](./console.md) 
- [Java ](./java-get-started.md?tabs=maven)
- [JavaScript](./javascript.md)
- [Node.js](./nodejs.md)
- [Python](./opencensus-python.md)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Monitoramento sem código e criação de recursos do Visual Studio

Para o monitoramento de serviços sem código, como Azure Functions e Serviços de Aplicativo do Azure, você também precisará primeiro criar o recurso baseado em espaço de trabalho do Application Insights e, em seguida, apontar para esse recurso durante a fase de configuração de monitoramento.

Embora esses serviços ofereçam a opção de criar um novo recurso do Application Insights dentro de seu próprio processo de criação de recursos, os recursos criados por meio dessas opções de interface do usuário estão atualmente restritos à experiência clássica do Application Insights.

O mesmo aplica-se à experiência de criação de recursos do Application Insights no Visual Studio para ASP.NET e ASP.NET Core. Você deve selecionar um recurso existente baseado em espaço de trabalho com a interface do usuário de habilitação de monitoramento do Visual Studio. Selecionar Criar novo recurso no Visual Studio limitará a criação de um recurso clássico do Application Insights.

## <a name="creating-a-resource-automatically"></a>Criando um recurso automaticamente

### <a name="azure-cli"></a>CLI do Azure

Para acessar a versão prévia dos comandos da CLI do Azure do Application Insights, primeiro você precisa executar:

```azurecli
 az extension add -n application-insights
```

Se você não executar o comando `az extension add`, verá a seguinte mensagem de erro: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Agora você pode executar o seguinte para criar o recurso do Application Insights:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>Exemplo

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Para obter a documentação completa de CLI do Azure para este comando, confira a [documentação CLI do Azure](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create).

### <a name="azure-powershell"></a>Azure PowerShell

No momento, o comando `New-AzApplicationInsights` do PowerShell não dá suporte à criação de um recurso do Application Insights baseado em espaço de trabalho. Para criar um recurso baseado em espaço de trabalho com o PowerShell, você poderá usar os modelos do Azure Resource Manager abaixo e implantar com o PowerShell.

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

#### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Arquivo de parâmetros

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Modificação do espaço de trabalho associado

Depois que um recurso do Application Insights baseado em espaço de trabalho tiver sido criado, você poderá modificar o espaço de trabalho associado do Log Analytics.

No painel de recursos do Application Insights, selecione **Propriedades** > **Alterar Espaços de Trabalho** > **Espaços de Trabalho do Log Analytics**

## <a name="export-telemetry"></a>Exportar telemetria

A funcionalidade de exportação contínua herdada não tem suporte para recursos baseados em espaço de trabalho. Em vez disso, selecione **Configurações de diagnóstico** > **Adicionar configurações de diagnóstico** de dentro de seu recurso do Application Insights. Você pode selecionar todas as tabelas ou um subconjunto de tabelas para arquivar em uma conta de armazenamento ou transmitir para um hub de eventos do Azure.

> [!NOTE]
> Não há encargos adicionais para a exportação de telemetria no momento. As informações de preço para esse recurso estarão disponíveis na [página de preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).  Antes do início da cobrança, as notificações serão enviadas. Se você optar por continuar usando <feature name> após o período de aviso, você será cobrado na taxa aplicável. 
 

## <a name="next-steps"></a>Próximas etapas

* [Explorar métricas](../essentials/metrics-charts.md)
* [Escrever consultas do Analytics](../logs/log-query-overview.md)
