---
title: Log de Atividades do Azure
description: Exiba o log de atividades do Azure e envie-o para Azure Monitor logs, hubs de eventos do Azure e armazenamento do Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.openlocfilehash: 7583b4037d350b9190d6eae30c28b907b1d41d86
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722847"
---
# <a name="azure-activity-log"></a>Log de Atividades do Azure
O log de atividades é um [log de plataforma](./platform-logs-overview.md) no Azure que fornece insights sobre eventos no nível de assinatura. Isso inclui informações como quando um recurso é modificado ou quando uma máquina virtual é iniciada. Veja o log de atividades no portal do Azure ou recupere as entradas com o PowerShell e a CLI. Para funcionalidade adicional, você deve criar uma configuração de diagnóstico para enviar o log de atividades para [Azure monitor logs](../logs/data-platform-logs.md), para os hubs de eventos do Azure para encaminhar fora do Azure ou para o armazenamento do Azure para arquivamento. Este artigo fornece detalhes sobre como exibir o log de atividades e enviá-lo para diferentes destinos.

Consulte [criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes](./diagnostic-settings.md) para obter detalhes sobre como criar uma configuração de diagnóstico.

> [!NOTE]
> As entradas no log de atividades são geradas pelo sistema e não podem ser alteradas ou excluídas.

## <a name="view-the-activity-log"></a>Exibir o log de Atividades
Você pode acessar o log de atividades na maioria dos menus do portal do Azure. O filtro inicial é determinado pelo menu em que você abriu o log de atividades. Se você abri-lo no menu **monitorar** , o único filtro estará na assinatura. Se você abri-lo no menu de um recurso, o filtro será definido para esse recurso. Você sempre pode alterar o filtro para exibir todas as outras entradas. Clique em **Adicionar filtro** para adicionar propriedades adicionais ao filtro.

![Exibir log de atividades](./media/activity-log/view-activity-log.png)

Para obter uma descrição das categorias de log de atividades, consulte [esquema de eventos do log de atividades do Azure](activity-log-schema.md#categories).

## <a name="download-the-activity-log"></a>Baixar o log de atividades
Selecione **baixar como CSV** para baixar os eventos no modo de exibição atual.

![Baixar log de atividades](media/activity-log/download-activity-log.png)

### <a name="view-change-history"></a>Exibir histórico de alterações

Para alguns eventos, você pode exibir o histórico de alterações, que mostra as alterações ocorridas durante essa hora do evento. Selecione um evento do log de atividades que você deseja examinar melhor. Selecione a guia **Histórico de alterações (versão prévia)** para exibir as alterações associadas a esse evento.

![Lista de histórico de alterações para um evento](media/activity-log/change-history-event.png)

Se houver alterações associadas ao evento, você verá uma lista de alterações para selecionar. Isso abre a página **Histórico de alterações (versão prévia)** . Nessa página, você vê as alterações no recurso. No exemplo a seguir, você pode ver não apenas que a VM mudou de tamanhos, mas qual era o tamanho anterior da VM antes da alteração e para que ela foi alterada. Para saber mais sobre o histórico de alterações, consulte [obter alterações de recurso](../../governance/resource-graph/how-to/get-resource-changes.md).

![Página de histórico de alterações mostrando diferenças](media/activity-log/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>Outros métodos para recuperar eventos do log de atividades
Você também pode acessar eventos do log de atividades usando os métodos a seguir.

- Use o cmdlet [Get-AzLog](/powershell/module/az.monitor/get-azlog) para recuperar o log de atividades do PowerShell. Confira [Azure monitor exemplos do PowerShell](../powershell-samples.md#retrieve-activity-log).
- Use [az monitor activity-log](/cli/azure/monitor/activity-log) para recuperar o log de atividades da CLI.  Consulte [Exemplos de CLI do Azure Monitor](../cli-samples.md#view-activity-log).
- Use a [API REST do Azure Monitor](/rest/api/monitor/) para recuperar o log de atividades de um cliente REST. 


## <a name="send-to-log-analytics-workspace"></a>Enviar para o workspace do Log Analytics
 Envie o log de atividades para um espaço de trabalho Log Analytics para habilitar os recursos de [logs de Azure monitor](../logs/data-platform-logs.md) que incluem o seguinte:

- Correlacione os dados do log de atividades com outros dados de monitoramento coletados pelo Azure Monitor.
- Consolide entradas de log de várias assinaturas e locatários do Azure em um único local para análise em conjunto.
- Use consultas de log para executar uma análise complexa e obter informações aprofundadas sobre entradas do log de atividades.
- Use alertas de log com entradas de atividade, permitindo uma lógica de alerta mais complexa.
- Armazene entradas do log de atividades por mais de 90 dias.
- Não há encargos de ingestão de dados para dados de log de atividades armazenados em um espaço de trabalho Log Analytics.
- Não há encargos de retenção de dados até 90 dias para os dados do log de atividades armazenados em um espaço de trabalho Log Analytics.

[Crie uma configuração de diagnóstico](./diagnostic-settings.md) para enviar o log de atividades para um espaço de trabalho log Analytics. Você pode enviar o log de atividades de qualquer assinatura única para até cinco espaços de trabalho. A coleta de logs entre locatários requer o [Azure Lighthouse](../../lighthouse/index.yml).

Os dados do log de atividades em um espaço de trabalho Log Analytics são armazenados em uma tabela chamada *AzureActivity* que você pode recuperar com uma [consulta de log](../logs/log-query-overview.md) no [log Analytics](../logs/log-analytics-tutorial.md). A estrutura dessa tabela varia dependendo da [categoria da entrada de log](activity-log-schema.md). Para obter uma descrição das propriedades da tabela, consulte a [referência de dados do Azure monitor](/azure/azure-monitor/reference/tables/azureactivity).

Por exemplo, para exibir uma contagem de registros de log de atividades para cada categoria, use a consulta a seguir.

```kusto
AzureActivity
| summarize count() by CategoryValue
```

Para recuperar todos os registros na categoria administrativa, use a consulta a seguir.

```kusto
AzureActivity
| where CategoryValue == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>Enviar para hubs de eventos do Azure
Envie o log de atividades para os hubs de eventos do Azure para enviar entradas fora do Azure, por exemplo, para um SIEM de terceiros ou outras soluções do log Analytics. Os eventos do log de atividades dos hubs de eventos são consumidos no formato JSON com um `records` elemento que contém os registros em cada carga. O esquema depende da categoria e é descrito em [esquema da conta de armazenamento e dos hubs de eventos](activity-log-schema.md).

Veja a seguir exemplos de dados de saída dos hubs de eventos para um log de atividades:

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


## <a name="send-to--azure-storage"></a>Enviar para o armazenamento do Azure
Envie o log de atividades para uma conta de armazenamento do Azure se desejar manter seus dados de log por mais de 90 dias para auditoria, análise estática ou backup. Se você só precisa reter seus eventos por 90 dias ou menos, não é necessário configurar o arquivamento para uma conta de armazenamento, pois os eventos do log de atividades são mantidos na plataforma do Azure por 90 dias.

Quando você envia o log de atividades para o Azure, um contêiner de armazenamento é criado na conta de armazenamento assim que um evento ocorre. Os BLOBs no contêiner usam a seguinte convenção de nomenclatura:

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, um blob específico pode ter um nome semelhante ao seguinte:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro de uma hora especificada na URL do blob (por exemplo, h=12). Durante a hora presente, os eventos são acrescentados ao arquivo PT1H.json conforme eles ocorrem. O valor de minuto (m = 00) é sempre 00, pois os eventos do log de recursos são divididos em BLOBs individuais por hora.

Cada evento é armazenado na PT1H.jsno arquivo com o seguinte formato que usa um esquema de nível superior comum, mas, caso contrário, é exclusivo para cada categoria, conforme descrito no  [esquema do log de atividades](activity-log-schema.md).

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>Métodos de coleção herdados
Esta seção descreve os métodos herdados para coletar o log de atividades que foram usados antes das configurações de diagnóstico. Se você estiver usando esses métodos, deverá considerar a transição para as configurações de diagnóstico que fornecem melhor funcionalidade e consistência com os logs de recursos.

### <a name="log-profiles"></a>Perfis de log
Perfis de log são o método herdado para enviar o log de atividades para o armazenamento do Azure ou hubs de eventos. Use o procedimento a seguir para continuar trabalhando com um perfil de log ou para desabilitá-lo em preparação para a migração para uma configuração de diagnóstico.

1. No menu **Azure monitor** na portal do Azure, selecione log de **atividades**.
3. Clique em **Configurações do Diagnóstico**.

   ![Configurações de Diagnóstico](media/activity-log/diagnostic-settings.png)

4. Clique na faixa roxa para a experiência herdada.

    ![Experiência herdada](media/activity-log/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>Configurar o perfil de log usando o PowerShell

Se um perfil de log já existir, primeiro você precisará remover o perfil de log existente e, em seguida, criar um novo.

1. Use `Get-AzLogProfile` para identificar se já existe um perfil de log.  Se houver um perfil de log, observe a propriedade *Name* .

1. Use `Remove-AzLogProfile` para remover o perfil de log usando o valor da propriedade *nome*.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Use `Add-AzLogProfile` para criar um novo perfil de log:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Propriedade | Obrigatório | Descrição |
    | --- | --- | --- |
    | Nome |Sim |Nome de seu perfil de log. |
    | StorageAccountId |Não |ID de recurso da conta de armazenamento em que o log de atividades deve ser salvo. |
    | serviceBusRuleId |Não |ID da Regra de Barramento de Serviço para o namespace do Barramento de Serviço no qual você gostaria que os hubs de eventos fossem criados. Esta é uma cadeia de caracteres com o formato: `{service bus resource ID}/authorizationrules/{key name}` . |
    | Localização |Sim |Lista separada por vírgulas de regiões para as quais você gostaria de coletar eventos do Log de Atividades. |
    | RetentionInDays |Sim |Número de dias pelos quais os eventos devem ser retidos na conta de armazenamento, entre 1 e 365. Um valor de zero armazena os logs indefinidamente. |
    | Categoria |Não |Lista separada por vírgulas de categorias de eventos que devem ser coletados. Os valores possíveis são _gravação_, _exclusão_ e _ação_. |

### <a name="example-script"></a>Script de exemplo
Veja a seguir um exemplo de script do PowerShell para criar um perfil de log que grava o log de atividades em uma conta de armazenamento e no Hub de eventos.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -StorageAccountId  $storageAccountId -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Configurar o perfil de log usando CLI do Azure

Se um perfil de log já existir, primeiro será necessário remover o perfil de log existente e criar um novo.

1. Use `az monitor log-profiles list` para identificar se já existe um perfil de log.
2. Use `az monitor log-profiles delete --name "<log profile name>` para remover o perfil de log usando o valor da propriedade *nome*.
3. Use `az monitor log-profiles create` para criar um novo perfil de log:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Propriedade | Obrigatório | Descrição |
    | --- | --- | --- |
    | name |Sim |Nome de seu perfil de log. |
    | storage-account-id |Sim |A ID de Recurso da Conta de Armazenamento na qual os Logs de Atividades devem ser salvos. |
    | Locais |Sim |Lista separada por espaço de regiões para as quais você gostaria de coletar eventos do Log de Atividades. É possível exibir uma lista de todas as regiões para a assinatura usando `az account list-locations --query [].name`. |
    | dias |Sim |Número de dias pelos quais os eventos devem ser retidos, entre 1 e 365. Um valor de zero armazenará os logs indefinidamente (para sempre).  Se for zero, o parâmetro Enabled deverá ser definido como false. |
    |Habilitado | Sim |Verdadeiro ou falso.  Usado para habilitar ou desabilitar a política de retenção.  Se for Verdadeiro, o parâmetro de dias deverá ser um valor maior que 0.
    | Categorias |Sim |Lista separada por espaço de categorias de eventos que devem ser coletadas. Os valores possíveis são Gravação, Exclusão e Ação. |


### <a name="log-analytics-workspace"></a>Espaço de trabalho do Log Analytics
O método herdado para enviar o log de atividades para um Log Analytics espaço de trabalho está conectando o log na configuração do espaço de trabalho. 

1. No menu **log Analytics espaços de trabalho** na portal do Azure, selecione o espaço de trabalho para coletar o log de atividades.
1. Na seção **fontes de dados de espaço de trabalho** do menu do espaço de trabalho, selecione **log de atividades do Azure**.
1. Clique na assinatura que você deseja conectar.

    ![Captura de tela mostra Log Analytics espaço de trabalho com um log de atividades do Azure selecionado.](media/activity-log/workspaces.png)

2. Clique em **conectar** para conectar o log de atividades na assinatura ao espaço de trabalho selecionado. Se a assinatura já estiver conectada a outro espaço de trabalho, clique em **Desconectar** primeiro para desconectá-la.

    ![Conectar espaços de trabalho](media/activity-log/connect-workspace.png)


Para desabilitar a configuração, execute o mesmo procedimento e clique em **Desconectar** para remover a assinatura do espaço de trabalho.

### <a name="data-structure-changes"></a>Alterações na estrutura de dados
As configurações de diagnóstico enviam os mesmos dados que o método herdado usado para enviar o log de atividades com algumas alterações na estrutura da tabela *AzureActivity* .

As colunas na tabela a seguir foram preteridas no esquema atualizado. Eles ainda existem no *AzureActivity* , mas não terão dados. A substituição dessas colunas não é nova, mas elas contêm os mesmos dados que a coluna preterida. Elas estão em um formato diferente, portanto, talvez seja necessário modificar as consultas de log que as utilizam. 

| Coluna preterida | Coluna de substituição |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| Categoria          | Categoriavalue          |
| OperationName     | OperationNamevalue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> Em alguns casos, os valores nessas colunas podem estar em letras maiúsculas. Se você tiver uma consulta que inclua essas colunas, deve usar o [operador =~](/azure/kusto/query/datatypes-string-operators) para fazer uma comparação que não diferencia maiúsculas de minúsculas.

A seguinte coluna foi adicionada ao *AzureActivity* no esquema atualizado:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>Solução de monitoramento de Análise do Log de Atividades
A solução de monitoramento de Log Analytics do Azure será preterida em breve e substituída por uma pasta de trabalho usando o esquema atualizado no espaço de trabalho Log Analytics. Você ainda poderá usar a solução se ela já estiver habilitada, mas ela só poderá ser usada se você estiver coletando o log de atividades usando configurações herdadas. 



### <a name="use-the-solution"></a>Usar a solução
As soluções de monitoramento são acessadas no menu **monitorar** na portal do Azure. Selecione **mais** na seção **insights** para abrir a página **visão geral** com os blocos de solução. O bloco **logs de atividade do Azure** exibe uma contagem do número de registros **AzureActivity** em seu espaço de trabalho.

![Bloco de logs de atividade do Azure](media/activity-log/azure-activity-logs-tile.png)


Clique no bloco **logs de atividade do Azure** para abrir a exibição **logs de atividade do Azure** . A exibição inclui as partes de visualização na tabela a seguir. Cada parte lista até 10 itens que correspondem aos critérios das partes para o intervalo de tempo especificado. Você pode executar uma consulta de log que retorna todos os registros correspondentes clicando em **Ver todos** na parte inferior da parte.

![Painel Logs de Atividade do Azure](media/activity-log/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Habilitar a solução para novas assinaturas
Em breve, você não poderá mais adicionar a solução de análise de logs de atividades à sua assinatura usando o portal do Azure. Você pode adicioná-lo usando o procedimento a seguir com um modelo do Resource Manager. 

1. Copie o JSON a seguir em um arquivo chamado *ActivityLogTemplate*. JSON.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Implante o modelo usando os seguintes comandos do PowerShell:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>Próximas etapas

* [Leia uma visão geral dos logs de plataforma](./platform-logs-overview.md)
* [Revisar esquema de evento do log de atividades](activity-log-schema.md)
* [Criar configuração de diagnóstico para enviar logs de atividades para outros destinos](./diagnostic-settings.md)
