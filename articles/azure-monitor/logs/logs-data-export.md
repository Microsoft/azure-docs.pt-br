---
title: Log Analytics exportar dados de espaço de trabalho no Azure Monitor (versão prévia)
description: Log Analytics exportação de dados permite que você exporte continuamente os dados das tabelas selecionadas do seu espaço de trabalho Log Analytics para uma conta de armazenamento do Azure ou hubs de eventos do Azure, conforme ele é coletado.
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 02/07/2021
ms.openlocfilehash: ea33eff30e712c1597c3606d74cb6d56683211ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615577"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Log Analytics exportar dados de espaço de trabalho no Azure Monitor (versão prévia)
Log Analytics exportação de dados de espaço de trabalho no Azure Monitor permite que você exporte continuamente os dados de tabelas selecionadas no espaço de trabalho Log Analytics para uma conta de armazenamento do Azure ou hubs de eventos do Azure conforme ele é coletado. Este artigo fornece detalhes sobre esse recurso e as etapas para configurar a exportação de dados em seus espaços de trabalho.

## <a name="overview"></a>Visão geral
Quando a exportação de dados estiver configurada para seu espaço de trabalho do Log Analytics, todos os novos dados enviados para as tabelas selecionadas no espaço de trabalho serão exportados automaticamente para sua conta de armazenamento em blobs de acréscimo por hora ou para o Hub de eventos em tempo quase real.

![Visão geral da exportação de dados](media/logs-data-export/data-export-overview.png)

Todos os dados das tabelas incluídas são exportados sem um filtro. Por exemplo, quando você configura uma regra de exportação de dados para a tabela *SecurityEvent* , todos os dados enviados para a tabela *SecurityEvent* são exportados a partir do tempo de configuração.


## <a name="other-export-options"></a>Outras opções de exportação
Log Analytics exportação de dados de espaço de trabalho exporta dados continuamente de um espaço de trabalho Log Analytics. Outras opções para exportar dados para cenários específicos incluem o seguinte:

- Exportação agendada de uma consulta de log usando um aplicativo lógico. Isso é semelhante ao recurso de exportação de dados, mas permite que você envie dados filtrados ou agregados para o armazenamento do Azure. Embora esse método esteja sujeito a [limites de consulta de log](../service-limits.md#log-analytics-workspaces), consulte [arquivar dados do espaço de trabalho log Analytics no armazenamento do Azure usando o aplicativo lógico](logs-export-logic-app.md).
- Exportar uma vez para o computador local usando o script do PowerShell. Consulte [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).


## <a name="limitations"></a>Limitações

- A configuração pode ser executada usando a CLI ou solicitações REST no momento. Portal do Azure ou PowerShell ainda não têm suporte.
- A ```--export-all-tables``` opção na CLI e no REST não tem suporte e será removida. Você deve fornecer a lista de tabelas em regras de exportação explicitamente.
- No momento, as tabelas com suporte estão limitadas às especificadas na seção [tabelas com suporte](#supported-tables) abaixo. Por exemplo, as tabelas de log personalizadas não têm suporte no momento.
- Se a regra de exportação de dados incluir uma tabela sem suporte, a operação terá sucesso, mas nenhum dado será exportado para essa tabela até que a tabela receba suporte. 
- Se a regra de exportação de dados incluir uma tabela que não existe, ela falhará com erro ```Table <tableName> does not exist in the workspace``` .
- Seu espaço de trabalho do Log Analytics pode estar em qualquer região, exceto para o seguinte:
  - Regiões do Azure Governamental
  - Oeste do Japão
  - Sudeste do Brasil
  - Leste da Noruega
  - Norte dos EAU
- Você pode criar duas regras de exportação em um espaço de trabalho – em pode ser uma regra para o Hub de eventos e uma regra para a conta de armazenamento.
- A conta de armazenamento de destino ou o Hub de eventos deve estar na mesma região que o espaço de trabalho Log Analytics.
- Os nomes das tabelas a serem exportadas não podem ter mais de 60 caracteres para uma conta de armazenamento e não mais de 47 caracteres para um hub de eventos. Tabelas com nomes mais longos não serão exportadas.
- O suporte ao blob de acréscimo para Azure Data Lake Storage agora está em [Visualização pública limitada](https://azure.microsoft.com/updates/append-blob-support-for-azure-data-lake-storage-preview/)

## <a name="data-completeness"></a>Integridade dos dados
A exportação de dados continuará a tentar enviar dados por até 30 minutos caso o destino não esteja disponível. Se ainda não estiver disponível após 30 minutos, os dados serão descartados até que o destino fique disponível.

## <a name="cost"></a>Cost
No momento, não há encargos adicionais para o recurso de exportação de dados. Os preços para exportação de dados serão anunciados no futuro e um aviso fornecido antes do início da cobrança. Se você optar por continuar usando a exportação de dados após o período de aviso, você será cobrado na taxa aplicável.

## <a name="export-destinations"></a>Destinos de exportação

### <a name="storage-account"></a>Conta de armazenamento
Os dados são enviados para contas de armazenamento à medida que atingem Azure Monitor e armazenados em blobs de acréscimo por hora. A configuração de exportação de dados cria um contêiner para cada tabela na conta de armazenamento com o nome *am-* seguido pelo nome da tabela. Por exemplo, a tabela *SecurityEvent* seria enviada a um contêiner chamado *am-SecurityEvent*.

O caminho do blob da conta de armazenamento é *WorkspaceResourceId =/subscriptions/Subscription-ID/resourcegroups/ \<resource-group\> /Providers/Microsoft.operationalinsights/Workspaces/ \<workspace\> /y = \<four-digit numeric year\> /m = \<two-digit numeric month\> /d = \<two-digit numeric day\> /h = \<two-digit 24-hour clock hour\> /m = 00/PT1H.json*. Como os blobs de acréscimo são limitados a gravações 50 mil no armazenamento, o número de BLOBs exportados pode se estender se o número de acréscimos for alto. O padrão de nomenclatura para BLOBs nesse caso seria PT1H_ #. JSON, em que # é a contagem de BLOBs incremental.

O formato de dados da conta de armazenamento é uma [linha JSON](../essentials/resource-logs-blob-format.md). Isso significa que cada registro é delimitado por uma nova linha, sem nenhuma matriz de registros externos e sem vírgulas entre os registros JSON. 

[![Dados de exemplo de armazenamento](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

Log Analytics exportação de dados pode gravar blobs de acréscimo em contas de armazenamento imutáveis quando as políticas de retenção baseadas em tempo têm a configuração *allowProtectedAppendWrites* habilitada. Isso permite gravar novos blocos em um blob de acréscimo, mantendo a proteção contra imutabilidade e a conformidade. Consulte [permitir gravações de blobs de acréscimo protegidos](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes).

> [!NOTE]
> O suporte ao blob de acréscimo para o armazenamento de Azure Data Lake agora está disponível em versão prévia em todas as regiões do Azure. [Registre-se na visualização pública limitada](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pURDk2NjMzUTVEVzU5UU1XUlRXSTlHSlkxQS4u) antes de criar uma regra de exportação para Azure data Lake armazenamento. A exportação não funcionará sem esse registro.

### <a name="event-hub"></a>Hub de Eventos
Os dados são enviados ao seu hub de eventos quase em tempo real à medida que atingem Azure Monitor. Um hub de eventos é criado para cada tipo de dados que você exporta com o nome *am-* seguido pelo nome da tabela. Por exemplo, a tabela *SecurityEvent* seria enviada a um hub de eventos chamado *am-SecurityEvent*. Se você quiser que os dados exportados atinjam um hub de eventos específico ou se tiver uma tabela com um nome que exceda o limite de 47 caracteres, você poderá fornecer seu próprio nome de Hub de eventos e exportar todos os dados para tabelas definidas para ele.

> [!IMPORTANT]
> O [número de hubs de eventos com suporte por namespace é 10](../../event-hubs/event-hubs-quotas.md#common-limits-for-all-tiers). Se você exportar mais de 10 tabelas, forneça seu próprio nome de Hub de eventos para exportar todas as tabelas para esse Hub de eventos.

Considerações:
1. A SKU do hub de eventos ' Basic ' dá suporte ao [limite](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-tiers) de tamanho de evento inferior e alguns logs em seu espaço de trabalho podem excedê-lo e ser Descartado. É recomendável usar o Hub de eventos ' Standard ' ou ' dedicado ' como destino de exportação.
2. O volume de dados exportados geralmente aumenta com o tempo e a escala do hub de eventos precisa ser aumentada para lidar com taxas de transferência maiores e evitar cenários de limitação e latência de dados. Você deve usar o recurso de inflar automaticamente dos hubs de eventos para escalar verticalmente e aumentar o número de unidades de produtividade e atender às necessidades de uso. Consulte [dimensionar automaticamente as unidades de produtividade dos hubs de eventos do Azure](../../event-hubs/event-hubs-auto-inflate.md) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos
Veja a seguir os pré-requisitos que devem ser concluídos antes de configurar Log Analytics exportação de dados.

- A conta de armazenamento e o Hub de eventos já devem ser criados e devem estar na mesma região que o espaço de trabalho Log Analytics. Se você precisar replicar seus dados para outras contas de armazenamento, poderá usar qualquer uma das [Opções de redundância do armazenamento do Azure](../../storage/common/storage-redundancy.md).  
- A conta de armazenamento deve ser StorageV1 ou StorageV2. Não há suporte para o armazenamento clássico  
- Se você tiver configurado sua conta de armazenamento para permitir o acesso de redes selecionadas, será necessário adicionar uma exceção nas configurações da conta de armazenamento para permitir que Azure Monitor gravar no armazenamento.

## <a name="enable-data-export"></a>Habilitar exportação de dados
As etapas a seguir devem ser executadas para habilitar a exportação de dados de Log Analytics. Consulte as seções a seguir para obter mais detalhes sobre cada uma delas.

- Registrar provedor de recursos.
- Permitir serviços confiáveis da Microsoft.
- Crie uma ou mais regras de exportação de dados que definam as tabelas a serem exportadas e seu destino.

### <a name="register-resource-provider"></a>Registrar provedor de recursos
O provedor de recursos do Azure a seguir precisa ser registrado para sua assinatura para habilitar a exportação de dados Log Analytics. 

- Microsoft.insights

Esse provedor de recursos provavelmente já estará registrado para a maioria dos Azure Monitor usuários. Para verificar, acesse **assinaturas** no portal do Azure. Selecione sua assinatura e clique em **provedores de recursos** na seção **configurações** do menu. Localize **Microsoft. insights**. Se seu status for **registrado**, ele já estará registrado. Caso contrário, clique em **registrar** para registrá-lo.

Você também pode usar qualquer um dos métodos disponíveis para registrar um provedor de recursos, conforme descrito em [provedores de recursos e tipos do Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Veja a seguir um comando de exemplo usando o PowerShell:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>Permitir serviços confiáveis da Microsoft
Se você tiver configurado sua conta de armazenamento para permitir o acesso de redes selecionadas, será necessário adicionar uma exceção para permitir que Azure Monitor gravar na conta. Em **firewalls e redes virtuais** para sua conta de armazenamento, selecione **permitir que os serviços confiáveis da Microsoft acessem essa conta de armazenamento**.

[![Firewalls e redes virtuais da conta de armazenamento](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)

### <a name="create-or-update-data-export-rule"></a>Criar ou atualizar regra de exportação de dados
Uma regra de exportação de dados define as tabelas para as quais os dados são exportados e o destino. Você pode criar uma única regra para cada destino no momento.

A regra de exportação deve incluir tabelas que você tem em seu espaço de trabalho. Execute esta consulta para obter uma lista de tabelas disponíveis no seu espaço de trabalho.

```kusto
find where TimeGenerated > ago(24h) | distinct Type
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando a seguir para criar uma regra de exportação de dados para uma conta de armazenamento usando a CLI.

```azurecli
$storageAccountResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountResourceId
```

Use o comando a seguir para criar uma regra de exportação de dados para um hub de eventos usando a CLI. Um hub de eventos separado é criado para cada tabela.

```azurecli
$eventHubsNamespacesResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesResourceId
```

Use o comando a seguir para criar uma regra de exportação de dados para um hub de eventos específico usando a CLI. Todas as tabelas são exportadas para o nome do hub de eventos fornecido. 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventHubName/eventhub-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubResourceId
```

# <a name="rest"></a>[REST](#tab/rest)

Use a solicitação a seguir para criar uma regra de exportação de dados usando a API REST. A solicitação deve usar a autorização de token de portador e o tipo de conteúdo application/json.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

O corpo da solicitação especifica o destino das tabelas. A seguir está um corpo de exemplo para a solicitação REST para uma conta de armazenamento.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

A seguir está um corpo de exemplo para a solicitação REST para um hub de eventos.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

Veja a seguir um corpo de exemplo para a solicitação REST para um hub de eventos em que o nome do hub de eventos é fornecido. Nesse caso, todos os dados exportados são enviados para esse Hub de eventos.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```

# <a name="template"></a>[Modelo](#tab/json)

Use o comando a seguir para criar uma regra de exportação de dados para uma conta de armazenamento usando o modelo.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "storageAccountRuleName": {
            "defaultValue": "storage-account-rule-name",
            "type": "string"
        },
        "storageAccountResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
                {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/' , parameters('storageAccountRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('storageAccountResourceId')]"
                      },
                      "tableNames": [
                          "Heartbeat",
                          "InsightsMetrics",
                          "VMConnection",
                          "Usage"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Use o comando a seguir para criar uma regra de exportação de dados para um hub de eventos usando o modelo. Um hub de eventos separado é criado para cada tabela.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]"
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Use o comando a seguir para criar uma regra de exportação de dados para um hub de eventos específico usando o modelo. Todas as tabelas são exportadas para o nome do hub de eventos fornecido.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        },
        "eventhubName": {
            "defaultValue": "event-hub-name",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]",
                          "metaData": {
                              "eventHubName": "[parameters('eventhubName')]"
                          }
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

---

## <a name="view-data-export-rule-configuration"></a>Exibir configuração da regra de exportação de dados

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando a seguir para exibir a configuração de uma regra de exportação de dados usando a CLI.

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Use a solicitação a seguir para exibir a configuração de uma regra de exportação de dados usando a API REST. A solicitação deve usar a autorização de token de portador.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Modelo](#tab/json)

N/D

---

## <a name="disable-an-export-rule"></a>Desabilitar uma regra de exportação

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

As regras de exportação podem ser desabilitadas para permitir que você interrompa a exportação quando não precisar reter dados por um determinado período, como quando o teste está sendo executado. Use o comando a seguir para desabilitar uma regra de exportação de dados usando a CLI.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

As regras de exportação podem ser desabilitadas para permitir que você interrompa a exportação quando não precisar reter dados por um determinado período, como quando o teste está sendo executado. Use a solicitação a seguir para desabilitar uma regra de exportação de dados usando a API REST. A solicitação deve usar a autorização de token de portador.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

# <a name="template"></a>[Modelo](#tab/json)

As regras de exportação podem ser desabilitadas para permitir que você interrompa a exportação quando não precisar reter dados por um determinado período, como quando o teste está sendo executado. Defina ```"enable": false``` no modelo para desabilitar uma exportação de dados.

---

## <a name="delete-an-export-rule"></a>Excluir uma regra de exportação

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando a seguir para excluir uma regra de exportação de dados usando a CLI.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Use a solicitação a seguir para excluir uma regra de exportação de dados usando a API REST. A solicitação deve usar a autorização de token de portador.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Modelo](#tab/json)

N/D

---

## <a name="view-all-data-export-rules-in-a-workspace"></a>Exibir todas as regras de exportação de dados em um espaço de trabalho

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando a seguir para exibir todas as regras de exportação de dados em um espaço de trabalho usando a CLI.

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST](#tab/rest)

Use a solicitação a seguir para exibir todas as regras de exportação de dados em um espaço de trabalho usando a API REST. A solicitação deve usar a autorização de token de portador.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

# <a name="template"></a>[Modelo](#tab/json)

N/D

---

## <a name="unsupported-tables"></a>Tabelas sem suporte
Se a regra de exportação de dados incluir uma tabela sem suporte, a configuração terá sucesso, mas nenhum dado será exportado para essa tabela. Se a tabela for suportada posteriormente, seus dados serão exportados nesse momento.

Se a regra de exportação de dados incluir uma tabela que não existe, ela falhará com o erro "a tabela não <tableName> existe no espaço de trabalho".


## <a name="supported-tables"></a>Tabelas com suporte
No momento, as tabelas com suporte são limitadas às especificadas abaixo. Todos os dados da tabela serão exportados, a menos que sejam especificadas limitações. Esta lista será atualizada conforme o suporte para tabelas adicionais for adicionado.


| Tabela | Limitações |
|:---|:---|
| AADDomainServicesAccountLogon |  |
| AADDomainServicesAccountManagement |  |
| AADDomainServicesDirectoryServiceAccess |  |
| AADDomainServicesLogonLogoff |  |
| AADDomainServicesPolicyChange |  |
| AADDomainServicesPrivilegeUse |  |
| AADManagedIdentitySignInLogs |  |
| AADNonInteractiveUserSignInLogs |  |
| AADProvisioningLogs |  |
| AADServicePrincipalSignInLogs |  |
| ABSBotRequests |  |
| ACSBillingUsage |  |
| ACSSMSIncomingOperations |  |
| ADAssessmentRecommendation |  |
| ADFActivityRun |  |
| ADFPipelineRun |  |
| ADFTriggerRun |  |
| ADReplicationResult |  |
| ADSecurityAssessmentRecommendation |  |
| ADTDigitalTwinsOperation |  |
| ADTEventRoutesOperation |  |
| ADTModelsOperation |  |
| ADTQueryOperation |  |
| ADXCommand |  |
| ADXQuery |  |
| AegDeliveryFailureLogs |  |
| AegPublishFailureLogs |  |
| Alerta |  |
| AmlOnlineEndpointConsoleLog |  |
| ApiManagementGatewayLogs |  |
| AppCenterError |  |
| AppPlatformSystemLogs |  |
| AppServiceAppLogs |  |
| AppServiceAuditLogs |  |
| AppServiceConsoleLogs |  |
| AppServiceFileAuditLogs |  |
| AppServiceHTTPLogs |  |
| AppServicePlatformLogs |  |
| AuditLogs |  |
| AutoscaleEvaluationsLog |  |
| AutoscaleScaleActionsLog |  |
| AWSCloudTrail |  |
| AzureAssessmentRecommendation |  |
| AzureDevOpsAuditing |  |
| BehaviorAnalytics |  |
| BlockchainApplicationLog |  |
| BlockchainProxyLog |  |
| CommonSecurityLog |  |
| ComputerGroup |  |
| ConfigurationData | Suporte parcial – alguns dos dados são ingeridos por meio de serviços internos que não têm suporte para exportação. Esta parte está ausente na exportação no momento. |
| ContainerImageInventory |  |
| ContainerInventory |  |
| ContainerLog |  |
| ContainerNodeInventory |  |
| ContainerServiceLog |  |
| CoreAzureBackup |  |
| DatabricksAccounts |  |
| DatabricksClusters |  |
| DatabricksDBFS |  |
| DatabricksInstancePools |  |
| DatabricksJobs |  |
| DatabricksNotebook |  |
| DatabricksSecrets |  |
| DatabricksSQLPermissions |  |
| DatabricksSSH |  |
| DatabricksWorkspace |  |
| DnsEvents |  |
| DnsInventory |  |
| Dynamics365Activity |  |
| Evento | Suporte parcial – alguns dos dados para essa tabela são ingeridos por meio da conta de armazenamento. Esta parte está ausente na exportação no momento. |
| ExchangeAssessmentRecommendation |  |
| FailedIngestion |  |
| FunctionAppLogs |  |
| Pulsação |  |
| HuntingBookmark |  |
| InsightsMetrics | Suporte parcial – alguns dos dados são ingeridos por meio de serviços internos que não têm suporte para exportação. Esta parte está ausente na exportação no momento. |
| IntuneAuditLogs |  |
| IntuneDevices |  |
| IntuneOperationalLogs |  |
| KubeEvents |  |
| KubeHealth |  |
| KubeMonAgentEvents |  |
| KubeNodeInventory |  |
| KubePodInventory |  |
| KubeServices |  |
| LAQueryLogs |  |
| McasShadowItReporting |  |
| MicrosoftAzureBastionAuditLogs |  |
| MicrosoftDataShareReceivedSnapshotLog |  |
| MicrosoftDataShareSentSnapshotLog |  |
| MicrosoftHealthcareApisAuditLogs |  |
| NWConnectionMonitorPathResult |  |
| NWConnectionMonitorTestResult |  |
| OfficeActivity | Suporte parcial – alguns dos dados a serem ingeridos por meio de WebHooks do O365 à LA. Esta parte está ausente na exportação no momento. |
| Operação | Suporte parcial – alguns dos dados são ingeridos por meio de serviços internos que não têm suporte para exportação. Esta parte está ausente na exportação no momento. |
| Perf | Suporte parcial – somente os dados de desempenho do Windows têm suporte no momento. Os dados de desempenho do Linux estão ausentes na exportação no momento. |
| PowerBIDatasetsTenant |  |
| PowerBIDatasetsWorkspace |  |
| PowerBIDatasetsWorkspacePreview |  |
| SCCMAssessmentRecommendation |  |
| SCOMAssessmentRecommendation |  |
| SecurityAlert |  |
| SecurityBaseline |  |
| SecurityBaselineSummary |  |
| SecurityDetection |  |
| SecurityEvent | Suporte parcial – alguns dos dados para essa tabela são ingeridos por meio da conta de armazenamento. Esta parte está ausente na exportação no momento. |
| SecurityIncident |  |
| SecurityIoTRawEvent |  |
| SecurityNestedRecommendation |  |
| SecurityRecommendation |  |
| SfBAssessmentRecommendation |  |
| SfBOnlineAssessmentRecommendation |  |
| SharePointOnlineAssessmentRecommendation |  |
| SignalRServiceDiagnosticLogs |  |
| SigninLogs |  |
| SPAssessmentRecommendation |  |
| SQLAssessmentRecommendation |  |
| SucceededIngestion |  |
| SynapseBigDataPoolApplicationsEnded |  |
| SynapseBuiltinSqlPoolRequestsEnded |  |
| SynapseGatewayApiRequests |  |
| SynapseIntegrationActivityRuns |  |
| SynapseIntegrationPipelineRuns |  |
| SynapseIntegrationTriggerRuns |  |
| SynapseRbacOperations |  |
| SynapseSqlPoolDmsWorkers |  |
| SynapseSqlPoolExecRequests |  |
| SynapseSqlPoolRequestSteps |  |
| SynapseSqlPoolSqlRequests |  |
| SynapseSqlPoolWaits |  |
| syslog | Suporte parcial – alguns dos dados para essa tabela são ingeridos por meio da conta de armazenamento. Esta parte está ausente na exportação no momento. |
| ThreatIntelligenceIndicator |  |
| Atualizar | Suporte parcial – alguns dos dados são ingeridos por meio de serviços internos que não têm suporte para exportação. Esta parte está ausente na exportação no momento. |
| UpdateRunProgress |  |
| UpdateSummary |  |
| Uso |  |
| Watchlist |  |
| WindowsEvent |  |
| WindowsFirewall |  |
| WireData | Suporte parcial – alguns dos dados são ingeridos por meio de serviços internos que não têm suporte para exportação. Esta parte está ausente na exportação no momento. |
| WVDCheckpoints |  |
| WVDConnections |  |
| WVDErrors |  |
| WVDFeeds |  |
| WVDManagement |  |


## <a name="next-steps"></a>Próximas etapas

- [Consultar os dados exportados do data Explorer do Azure](../logs/azure-data-explorer-query-storage.md).
