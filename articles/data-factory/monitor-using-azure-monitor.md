---
title: Monitorar data factories usando o Azure Monitor
description: Saiba como usar Azure Monitor para monitorar pipelines de Data Factory do/Azure habilitando logs de diagnóstico com informações de Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 55b2fd911b32eae4f04ad9690de091e9dbd46ea4
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643962"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Monitorar e alertar Data Factory usando Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Os aplicativos de nuvem são complexos e têm muitas partes móveis. Os monitores fornecem dados para ajudar a garantir que seus aplicativos permaneçam em funcionamento em um estado íntegro. Os monitores também ajudam a evitar possíveis problemas e a solucionar problemas anteriores. Você pode usar dados de monitoramento para obter informações detalhadas sobre seus aplicativos. Esse conhecimento ajuda a melhorar o desempenho e a manutenção do aplicativo. Ele também ajuda a automatizar ações que, caso contrário, exigem intervenção manual.

O Azure Monitor fornece logs e métricas de infraestrutura de nível básico para a maioria dos serviços do Azure. Os logs de diagnóstico do Azure são emitidos por um recurso e fornecem dados avançados e frequentes sobre a operação do recurso. Azure Data Factory (ADF) pode gravar logs de diagnóstico no Azure Monitor. Para uma introdução de sete minutos e uma demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

Para saber mais, confira [Visão geral do Azure Monitor](../azure-monitor/overview.md).

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Mantendo Azure Data Factory métricas e dados de execução de pipeline

Data Factory armazena dados de execução de pipeline por apenas 45 dias. Use Azure Monitor se desejar manter esses dados por mais tempo. Com o monitor, você pode rotear os logs de diagnóstico para análise em vários destinos diferentes.

* **Conta de armazenamento**: Salve os logs de diagnóstico em uma conta de armazenamento para auditoria ou inspeção manual. Você pode usar as configurações de diagnóstico para especificar o tempo de retenção em dias.
* **Hub de eventos**: transmita os logs para os hubs de eventos do Azure. Os logs tornam-se entrada para uma solução de serviço de parceiro/análise personalizada, como Power BI.
* **Log Analytics**: analise os logs com log Analytics. A integração do Data Factory com o Azure Monitor é útil nos seguintes cenários:
  * Você deseja escrever consultas complexas em um conjunto avançado de métricas que são publicadas pelo Data Factory para monitorar. Você pode criar alertas personalizados nessas consultas via monitor.
  * Você quer monitorar os data factories. Você pode rotear dados de várias fábricas de dados para um único espaço de trabalho de monitoramento.

Você também pode usar uma conta de armazenamento ou um namespace de Hub de eventos que não esteja na assinatura do recurso que emite logs. O usuário que define a configuração deve ter acesso apropriado ao RBAC (controle de acesso baseado em função) do Azure para ambas as assinaturas.

## <a name="configure-diagnostic-settings-and-workspace"></a>Definir configurações de diagnóstico e espaço de trabalho

Crie ou adicione configurações de diagnóstico para seu data factory.

1. No portal, vá para monitor. Selecione **configurações** configurações de  >  **diagnóstico**.

1. Selecione o data factory para o qual você deseja definir uma configuração de diagnóstico.

1. Se não existir nenhuma configuração no data factory selecionado, você será solicitado a criar uma configuração. Selecione **Ativar diagnóstico**.

   ![Criar uma configuração de diagnóstico se não existir nenhuma configuração](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Se houver configurações existentes no data factory, você verá uma lista de configurações já configuradas no data factory. Selecione **Adicionar configuração de diagnóstico**.

   ![Adicionar uma configuração de diagnóstico se houver configurações](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Dê um nome à sua configuração, selecione **Enviar para log Analytics** e, em seguida, selecione um espaço de trabalho no **espaço de trabalho log Analytics**.

    * No modo de _diagnóstico do Azure_ , os logs de diagnóstico fluem para a tabela _AzureDiagnostics_ .

    * No modo _específico do recurso_ , os logs de diagnóstico de Azure data Factory fluxo nas seguintes tabelas:
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      Você pode selecionar vários logs relevantes para suas cargas de trabalho para enviar para Log Analytics tabelas. Por exemplo, se você não usar SQL Server Integration Services (SSIS), não será necessário selecionar nenhum log do SSIS. Se você quiser registrar em log as operações de início/parada/manutenção do SSIS Integration Runtime (IR), poderá selecionar logs de IR do SSIS. Se você invocar execuções de pacote SSIS via T-SQL no SQL Server Management Studio (SSMS), SQL Server Agent ou outras ferramentas designadas, poderá selecionar logs de pacote SSIS. Se você invocar execuções de pacote SSIS por meio de executar atividades de pacote SSIS em pipelines do ADF, poderá selecionar todos os logs.

    * Se você selecionar as _biométricas_, várias métricas de ADF serão disponibilizadas para você monitorar ou gerar alertas no, incluindo as métricas para atividades de ADF, pipeline e execuções de gatilho, bem como para operações de ir do SSIS e execuções de pacote SSIS.

   ![Nomeie suas configurações e selecione um espaço de trabalho do log Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > Como uma tabela de log do Azure não pode ter mais de 500 colunas, é **altamente recomendável** selecionar o _modo específico do recurso_. Para obter mais informações, consulte [referência de logs do AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics).

1. Selecione **Salvar**.

Após alguns instantes, a nova configuração aparecerá na lista de configurações dessa data factory. Os logs de diagnóstico são transmitidos para esse espaço de trabalho assim que novos dados de evento são gerados. Até 15 minutos podem decorrer entre o momento em que um evento é emitido e quando ele aparece no Log Analytics.

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Instalar a solução de análise de Azure Data Factory do Azure Marketplace

Essa solução fornece um resumo da integridade geral do seu Data Factory, com opções para detalhar os detalhes e solucionar problemas de padrões de comportamento inesperados. Com exibições avançadas e prontas para uso, você pode obter informações sobre o processamento de chaves, incluindo:

* Resumo rápido de data factory pipeline, atividade e execuções de gatilho
* Capacidade de analisar data factory execuções de atividade por tipo
* Resumo de data factory pipeline superior, erros de atividade

1. Vá para o **Azure Marketplace**, escolha filtro de **análise** e procure **Azure data Factory Analytics (versão prévia)**

   ![Vá para "Azure Marketplace", insira "filtro de análise" e selecione "análise de Azure Data Factory (versão prévia")](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. Detalhes sobre a **análise de Azure data Factory (versão prévia)**

   ![Detalhes sobre "análise de Azure Data Factory (versão prévia)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. Selecione **criar** e, em seguida, criar ou selecionar o **espaço de trabalho log Analytics**.

   ![Criando uma nova solução](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorar Data Factory métricas

A instalação dessa solução cria um conjunto padrão de modos de exibição dentro da seção pastas de trabalho do espaço de trabalho Log Analytics escolhido. Como resultado, as seguintes métricas são habilitadas:

* Execuções do ADF-1) execuções de pipeline por Data Factory
* Execuções do ADF-2) execuções de atividade por Data Factory
* Execuções do ADF-3) execuções de gatilho por Data Factory
* Erros do ADF-1) 10 principais erros de pipeline por Data Factory
* Erros do ADF-2) primeiras 10 execuções de atividade por Data Factory
* Erros do ADF-3) 10 principais erros de gatilho por Data Factory
* Estatísticas do ADF-1) execuções de atividade por tipo
* Estatísticas do ADF-2) execuções de gatilho por tipo
* Estatísticas do ADF-3) duração máxima de execuções de pipeline

![Janela com "pastas de trabalho (visualização)" e "AzureDataFactoryAnalytics" realçadas](media/data-factory-monitor-oms/monitor-oms-image6.png)

Você pode visualizar as métricas anteriores, examinar as consultas por trás dessas métricas, editar as consultas, criar alertas e executar outras ações.

![Representação gráfica das execuções de pipeline por data factory "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> A análise de Azure Data Factory (versão prévia) envia logs de diagnóstico para tabelas _de destino específicas do recurso_ . Você pode escrever consultas nas seguintes tabelas: _ADFPipelineRun_, _ADFTriggerRun_ e _ADFActivityRun_.

## <a name="data-factory-metrics"></a>Métricas de Data Factory

Com o monitor, você pode obter visibilidade do desempenho e da integridade de suas cargas de trabalho do Azure. O tipo de dados de monitor mais importante é a métrica, que também é chamada de contador de desempenho. As métricas são emitidas pela maioria dos recursos do Azure. O monitor fornece várias maneiras de configurar e consumir essas métricas para monitoramento e solução de problemas.

Aqui estão algumas das métricas emitidas pelo Azure Data Factory versão 2:

| **Métrica**                           | **Nome de exibição da métrica**                  | **Unidade** | **Tipo de agregação** | **Descrição**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | Métricas de execuções de atividades canceladas           | Contagem    | Total                | O número total de execuções de atividade que foram canceladas em uma janela de minuto. |
| ActivityFailedRuns                   | Métricas de execução de atividades com falha             | Contagem    | Total                | O número total de execuções de atividade que falharam em uma janela de minuto. |
| ActivitySucceededRuns                | Métricas de execução de atividades bem-sucedidas          | Contagem    | Total                | O número total de execuções de atividade que tiveram êxito em uma janela de minuto. |
| PipelineCancelledRuns                 | Métricas de execução do pipeline canceladas           | Contagem    | Total                | O número total de execuções de pipeline que foram canceladas em uma janela de minuto. |
| PipelineFailedRuns                   | Métricas de execução do pipeline com falha             | Contagem    | Total                | O número total de execuções de pipeline que falharam em uma janela de minuto. |
| PipelineSucceededRuns                | Métricas de execução do pipeline bem-sucedido          | Contagem    | Total                | O número total de execuções de pipeline que tiveram êxito em uma janela de minuto. |
| TriggerCancelledRuns                  | Métricas de execuções do gatilho canceladas            | Contagem    | Total                | O número total de execuções de gatilho que foram canceladas em uma janela de minuto. |
| TriggerFailedRuns                    | Métricas de execuções do gatilho com falha              | Contagem    | Total                | O número total de execuções de gatilho que falharam em uma janela de minuto. |
| TriggerSucceededRuns                 | Métricas de execuções do gatilho bem-sucedidas           | Contagem    | Total                | O número total de execuções de gatilho que tiveram êxito em uma janela de minuto. |
| SSISIntegrationRuntimeStartCancelled  | Métricas de início do tempo de execução de integração SSIS canceladas           | Contagem    | Total                | O número total do tempo de execução de integração do SSIS que foi cancelado em uma janela de minuto. |
| SSISIntegrationRuntimeStartFailed    | Métricas de início do tempo de execução de integração SSIS com falha             | Contagem    | Total                | O número total de tempo de execução de integração do SSIS começa a falhar em uma janela de minuto. |
| SSISIntegrationRuntimeStartSucceeded | Métricas de início de tempo de execução de integração SSIS com êxito          | Contagem    | Total                | O número total de tempo de execução de integração do SSIS é iniciado com êxito em uma janela de minuto. |
| SSISIntegrationRuntimeStopStuck      | Métricas de parada de tempo de execução de integração SSIS travadas               | Contagem    | Total                | O número total de paradas do tempo de execução de integração do SSIS foi paralisado em uma janela de minuto. |
| SSISIntegrationRuntimeStopSucceeded  | Métricas de parada de tempo de execução de integração SSIS com êxito           | Contagem    | Total                | O número total do tempo de execução de integração do SSIS é interrompido com êxito em uma janela de minuto. |
| SSISPackageExecutionCancelled         | Métricas de execução de pacote SSIS canceladas  | Contagem    | Total                | O número total de execuções de pacote SSIS que foram canceladas em uma janela de minuto. |
| SSISPackageExecutionFailed           | Métricas de execução de pacote SSIS com falha    | Contagem    | Total                | O número total de execuções de pacote SSIS que falharam em uma janela de minuto. |
| SSISPackageExecutionSucceeded        | Métricas de execução de pacote SSIS com êxito | Contagem    | Total                | O número total de execuções de pacote SSIS que tiveram êxito em uma janela de minuto. |

Para acessar as métricas, conclua as instruções em [Azure monitor plataforma de dados](../azure-monitor/data-platform.md).

> [!NOTE]
> Somente eventos de concluídos, atividade disparada e execuções de pipeline são emitidos. Em andamento e as execuções de depuração **não** são emitidas. Por outro lado, os eventos de **todas as** execuções de pacote SSIS são emitidos, incluindo os que estão concluídos e em andamento, independentemente dos seus métodos de invocação. Por exemplo, você pode invocar execuções de pacote em SQL Server Data Tools habilitadas para o Azure (SSDT), via T-SQL no SSMS, SQL Server Agent ou outras ferramentas designadas, e como execuções disparadas ou de depuração de executar atividades de pacote SSIS em pipelines do ADF.

## <a name="data-factory-alerts"></a>Alertas de Data Factory

Entre no portal do Azure e selecione **monitorar**  >  **alertas** para criar alertas.

![Alertas no menu do portal](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Criar alertas

1. Selecione **+ Nova regra de alerta** para criar um novo alerta.

    ![Nova regra de alerta](media/monitor-using-azure-monitor/alerts_image4.png)

1. Defina a condição de alerta.

    > [!NOTE]
    > Certifique-se de selecionar **tudo** na lista suspensa **Filtrar por tipo de recurso** .

    !["Definir a condição de alerta" > "Selecionar destino", que abre o painel "selecionar um recurso" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Definir a condição de alerta" > "adicionar critérios", que abre o painel "configurar lógica de sinal"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Painel "configurar tipo de sinal"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Defina os detalhes do alerta.

    ![Detalhes do alerta](media/monitor-using-azure-monitor/alerts_image8.png)

1. Defina o grupo de ações.

    ![Criar uma regra, com "novo grupo de ações" realçado](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Criar um novo grupo de ações](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Configurar email, SMS, push e voz](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definir um grupo de ações](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Configurar logs de diagnóstico por meio da API REST do Azure Monitor

### <a name="diagnostic-settings"></a>Configurações de Diagnóstico

Use as configurações de diagnóstico para configurar os logs de diagnóstico para recursos que não são de computação. As configurações de um controle de recurso têm os seguintes recursos:

* Eles especificam onde os logs de diagnóstico são enviados. Os exemplos incluem uma conta de armazenamento do Azure, um hub de eventos do Azure ou logs de monitor.
* Eles especificam quais categorias de log são enviadas.
* Eles especificam quanto tempo cada categoria de log deve ser mantida em uma conta de armazenamento.
* Uma retenção de zero dias significa que os logs serão mantidos indefinidamente. Caso contrário, o valor pode ser qualquer número de dias de 1 a 2.147.483.647.
* Se as políticas de retenção forem definidas, mas o armazenamento de logs em uma conta de armazenamento estiver desabilitado, as políticas de retenção não terão nenhum efeito. Por exemplo, essa condição pode ocorrer quando apenas as opções hubs de eventos ou logs de monitor são selecionadas.
* As políticas de retenção são aplicadas por dia. O limite entre os dias ocorre no UTC (tempo universal coordenado da meia-noite). No final de um dia, os logs de dias que estão além da política de retenção são excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início de hoje, os logs de antes de ontem serão excluídos.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Habilitar logs de diagnóstico por meio da API REST do Azure Monitor

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Criar ou atualizar uma configuração de diagnóstico na API REST do monitor

##### <a name="request"></a>Solicitação

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Cabeçalhos

* Substitua `{api-version}` por `2016-09-01`.
* Substitua `{resource-id}` pela ID do recurso para o qual você deseja editar as configurações de diagnóstico. Para obter mais informações, consulte [usando grupos de recursos para gerenciar seus recursos do Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Defina o cabeçalho `Content-Type` como `application/json`.
* Defina o cabeçalho de autorização para o token Web JSON obtido do Azure Active Directory (Azure AD). Para obter mais informações, consulte [Autenticando solicitações](../active-directory/develop/authentication-vs-authorization.md).

##### <a name="body"></a>Corpo

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Propriedade | Type | Descrição |
| --- | --- | --- |
| **storageAccountId** |String | A ID de recurso da conta de armazenamento para a qual você deseja enviar os logs de diagnóstico. |
| **serviceBusRuleId** |String | A ID da regra de barramento de serviço do namespace do barramento de serviço no qual você deseja que os hubs de eventos sejam criados para os logs de diagnóstico de streaming. A ID da regra tem o formato `{service bus resource ID}/authorizationrules/{key name}` .|
| **workspaceId** | Tipo complexo | Uma matriz de detalhamentos de tempo de métrica e suas políticas de retenção. O valor desta propriedade está vazio. |
|**métricas**| Valores de parâmetro da execução do pipeline a serem passados para o pipeline invocado| Um objeto JSON que mapeia nomes de parâmetro para valores de argumento. |
| **logs**| Tipo complexo| O nome de uma categoria de log de diagnóstico para um tipo de recurso. Para obter a lista de categorias de log de diagnóstico para um recurso, execute uma operação obter diagnóstico-configurações. |
| **category**| String| Uma matriz de categorias de log e suas políticas de retenção. |
| **timeGrain** | String | A granularidade das métricas, que são capturadas no formato de duração ISO 8601. O valor da propriedade deve ser `PT1M` , que especifica um minuto. |
| **habilitado**| Boolean | Especifica se a coleta da categoria de métrica ou de log está habilitada para este recurso. |
| **retentionPolicy**| Tipo complexo| Descreve a política de retenção para uma categoria de métrica ou de log. Esta propriedade é usada somente para contas de armazenamento. |
|**dias**| int| O número de dias para manter as métricas ou os logs. Se o valor da propriedade for 0, os logs serão mantidos para sempre. Esta propriedade é usada somente para contas de armazenamento. |

##### <a name="response"></a>Resposta

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Obter informações sobre as configurações de diagnóstico na API REST do monitor

##### <a name="request"></a>Solicitação

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Cabeçalhos

* Substitua `{api-version}` por `2016-09-01`.
* Substitua `{resource-id}` pela ID do recurso para o qual você deseja editar as configurações de diagnóstico. Para obter mais informações, consulte [usando grupos de recursos para gerenciar seus recursos do Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Defina o cabeçalho `Content-Type` como `application/json`.
* Defina o cabeçalho de autorização para um token Web JSON que você obteve do Azure AD. Para obter mais informações, consulte [Autenticando solicitações](../active-directory/develop/authentication-vs-authorization.md).

##### <a name="response"></a>Resposta

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Para obter mais informações, consulte [configurações de diagnóstico](/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Esquema de logs e eventos

### <a name="monitor-schema"></a>Monitorar esquema

#### <a name="activity-run-log-attributes"></a>Atributos de log de execução de atividade

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Propriedade | Type | Descrição | Exemplo |
| --- | --- | --- | --- |
| **Level** |String | O nível dos logs de diagnóstico. Para logs de execução de atividade, defina o valor da propriedade como 4. | `4` |
| **correlationId** |String | A ID exclusiva para acompanhar uma solicitação específica. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | A hora do evento no formato UTC de TimeSpan `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| A ID da execução da atividade. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| A ID da execução do pipeline. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | A ID associada ao recurso de data Factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | A categoria dos logs de diagnóstico. Defina o valor da propriedade `ActivityRuns`. | `ActivityRuns` |
|**level**| String | O nível dos logs de diagnóstico. Defina o valor da propriedade `Informational`. | `Informational` |
|**operationName**| String | O nome da atividade com seu status. Se a atividade for a pulsação inicial, o valor da propriedade será `MyActivity -` . Se a atividade for a pulsação final, o valor da propriedade será `MyActivity - Succeeded` . | `MyActivity - Succeeded` |
|**pipelineName**| String | O nome do pipeline. | `MyPipeline` |
|**activityName**| String | O nome da atividade. | `MyActivity` |
|**start**| String | A hora de início da atividade é executada no formato UTC de TimeSpan. | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | A hora de término da atividade é executada no formato UTC de TimeSpan. Se o log de diagnóstico mostrar que uma atividade foi iniciada, mas ainda não terminou, o valor da propriedade será `1601-01-01T00:00:00Z` . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Atributos de log de execução de pipeline

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Propriedade | Type | Descrição | Exemplo |
| --- | --- | --- | --- |
| **Level** |String | O nível dos logs de diagnóstico. Para logs de execução de atividade, defina o valor da propriedade como 4. | `4` |
| **correlationId** |String | A ID exclusiva para acompanhar uma solicitação específica. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | A hora do evento no formato UTC de TimeSpan `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| A ID da execução do pipeline. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | A ID associada ao recurso de data Factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | A categoria dos logs de diagnóstico. Defina o valor da propriedade `PipelineRuns`. | `PipelineRuns` |
|**level**| String | O nível dos logs de diagnóstico. Defina o valor da propriedade `Informational`. | `Informational` |
|**operationName**| String | O nome do pipeline junto com seu status. Depois que a execução do pipeline for concluída, o valor da propriedade será `Pipeline - Succeeded` . | `MyPipeline - Succeeded`. |
|**pipelineName**| String | O nome do pipeline. | `MyPipeline` |
|**start**| String | A hora de início da atividade é executada no formato UTC de TimeSpan. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | A hora de término da atividade é executada no formato UTC de TimeSpan. Se o log de diagnóstico mostrar uma atividade iniciada mas ainda não tiver sido encerrada, o valor da propriedade será `1601-01-01T00:00:00Z` .  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | O status final da execução do pipeline. Os valores de propriedade possíveis são `Succeeded` e `Failed` . | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Gatilho-executar atributos de log

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| Propriedade | Type | Descrição | Exemplo |
| --- | --- | --- | --- |
| **Level** |String | O nível dos logs de diagnóstico. Para logs de execução de atividade, defina o valor da propriedade como 4. | `4` |
| **correlationId** |String | A ID exclusiva para acompanhar uma solicitação específica. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | A hora do evento no formato UTC de TimeSpan `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| A ID da execução do gatilho. | `08587023010602533858661257311` |
|**resourceId**| String | A ID associada ao recurso de data Factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | A categoria dos logs de diagnóstico. Defina o valor da propriedade `PipelineRuns`. | `PipelineRuns` |
|**level**| String | O nível dos logs de diagnóstico. Defina o valor da propriedade `Informational`. | `Informational` |
|**operationName**| String | O nome do gatilho com seu status final, que indica se o gatilho foi acionado com êxito. Se a pulsação tiver sido bem-sucedida, o valor da propriedade será `MyTrigger - Succeeded` . | `MyTrigger - Succeeded` |
|**triggerName**| String | O nome do gatilho. | `MyTrigger` |
|**triggerType**| String | O tipo do gatilho. Os valores de propriedade possíveis são `Manual Trigger` e `Schedule Trigger` . | `ScheduleTrigger` |
|**triggerEvent**| String | O evento do gatilho. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | A hora de início do acionamento do gatilho no formato UTC de TimeSpan. | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | O status final que mostra se o gatilho foi acionado com êxito. Os valores de propriedade possíveis são `Succeeded` e `Failed` . | `Succeeded`|

#### <a name="ssis-integration-runtime-log-attributes"></a>Atributos do log do Integration Runtime do SSIS

Aqui estão os atributos de log das operações de início/parada/manutenção do SSIS IR.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| Propriedade                   | Type   | Descrição                                                   | Exemplo                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | A hora do evento no formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | O nome da operação de IR do SSIS                            | `Start/Stop/Maintenance` |
| **category**               | String | A categoria de logs de diagnóstico                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | String | A ID exclusiva para acompanhar uma operação específica             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | String | O nome do ADF                                          | `MyADFv2` |
| **integrationRuntimeName** | String | O nome do seu IR do SSIS                                      | `MySSISIR` |
| **level**                  | String | O nível dos logs de diagnóstico                                  | `Informational` |
| **resultType**             | String | O resultado da operação de IR do SSIS                          | `Started/InProgress/Succeeded/Failed` |
| **message**                | String | A mensagem de saída da operação de IR do SSIS                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | String | A ID exclusiva do recurso do ADF                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>Atributos do log de contexto de mensagem de evento SSIS

Aqui estão os atributos de log das condições relacionadas às mensagens de evento que são geradas pelas execuções de pacote do SSIS em seu IR do SSIS. Eles transmitem informações semelhantes como [tabela de contexto de mensagem de evento de catálogo do SSIS (SSISDB) ou exibição](/sql/integration-services/system-views/catalog-event-message-context) que mostra os valores de tempo de execução de muitas propriedades de pacote SSIS. Eles são gerados quando você seleciona o `Basic/Verbose` nível de log e é útil para verificação de conformidade/depuração.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| Propriedade                   | Type   | Descrição                                                          | Exemplo                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | A hora do evento no formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Isso é definido como `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | String | A categoria de logs de diagnóstico                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | String | A ID exclusiva para acompanhar uma operação específica                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | O nome do ADF                                                 | `MyADFv2` |
| **integrationRuntimeName** | String | O nome do seu IR do SSIS                                             | `MySSISIR` |
| **level**                  | String | O nível dos logs de diagnóstico                                         | `Informational` |
| **operationId**            | String | A ID exclusiva para acompanhar uma operação específica no SSISDB          | `1` (1 significa operações relacionadas a pacotes **não** armazenados no SSISDB/invocado via T-SQL) |
| **contextDepth**           | String | A profundidade do seu contexto de mensagem de evento                              | `0` (0 significa o contexto antes do início da execução do pacote, 1 significa o contexto quando ocorre um erro e aumenta à medida que o contexto está além do erro) |
| **packagePath**            | String | O caminho do objeto de pacote como sua fonte de contexto de mensagem de evento      | `\Package` |
| **contextType**            | String | O tipo de objeto de pacote como sua fonte de contexto de mensagem de evento      | `60`(veja [mais tipos de contexto](/sql/integration-services/system-views/catalog-event-message-context#remarks)) |
| **contextSourceName**      | String | O nome do objeto de pacote como sua fonte de contexto de mensagem de evento      | `MyPackage` |
| **contextSourceId**        | String | A ID exclusiva do objeto de pacote como sua fonte de contexto de mensagem de evento | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | String | O nome da Propriedade do pacote para sua fonte de contexto de mensagem de evento   | `DelayValidation` |
| **propertyValue**          | String | O valor da Propriedade do pacote para sua fonte de contexto de mensagem de evento  | `False` |
| **resourceId**             | String | A ID exclusiva do recurso do ADF                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>Atributos de log de mensagens de evento do SSIS

Aqui estão os atributos de log das mensagens de evento que são geradas pelas execuções de pacote SSIS em seu IR do SSIS. Eles transmitem informações semelhantes à [tabela ou exibição de mensagens de evento SSISDB](/sql/integration-services/system-views/catalog-event-messages) que mostra o texto/metadados detalhados das mensagens de evento. Eles são gerados em qualquer nível de log, exceto `None` .

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| Propriedade                   | Type   | Descrição                                                        | Exemplo                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | String | A hora do evento no formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Isso é definido como `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | String | A categoria de logs de diagnóstico                                    | `SSISPackageEventMessages` |
| **correlationId**          | String | A ID exclusiva para acompanhar uma operação específica                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | O nome do ADF                                               | `MyADFv2` |
| **integrationRuntimeName** | String | O nome do seu IR do SSIS                                           | `MySSISIR` |
| **level**                  | String | O nível dos logs de diagnóstico                                       | `Informational` |
| **operationId**            | String | A ID exclusiva para acompanhar uma operação específica no SSISDB        | `1` (1 significa operações relacionadas a pacotes **não** armazenados no SSISDB/invocado via T-SQL) |
| **mensagemtime**            | String | A hora em que a mensagem de evento é criada no formato UTC          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | String | O tipo de sua mensagem de evento                                     | `70`(veja [mais tipos de mensagem](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)) |
| **messageSourceType**      | String | O tipo de origem da mensagem de evento                              | `20`(veja [mais tipos de origem de mensagem](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)) |
| **message**                | String | O texto da mensagem de evento                                     | `MyPackage:Validation has started.` |
| **packageName**            | String | O nome do arquivo de pacote executado                             | `MyPackage.dtsx` |
| **eventName**              | String | O nome do evento de tempo de execução relacionado                                 | `OnPreValidate` |
| **messageSourceName**      | String | O nome do componente do pacote como sua fonte de mensagem de evento         | `Data Flow Task` |
| **messageSourceId**        | String | A ID exclusiva do componente de pacote como sua fonte de mensagem de evento    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **SubComponentName**       | String | O nome do componente de fluxo de dados como sua fonte de mensagem de evento       | `SSIS.Pipeline` |
| **packagePath**            | String | O caminho do objeto de pacote como sua fonte de mensagem de evento            | `\Package\Data Flow Task` |
| **executionPath**          | String | O caminho completo do pacote pai para o componente executado            | `\Transformation\Data Flow Task` (Esse caminho também captura iterações de componente) |
| **threadId**               | String | A ID exclusiva do thread executado quando sua mensagem de evento é registrada | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>Atributos do log de estatísticas de executável do SSIS

Aqui estão os atributos de log das estatísticas de executável que são gerados pelas execuções de pacote SSIS em seu IR do SSIS, em que os executáveis são contêineres ou tarefas no fluxo de controle de pacotes. Eles transmitem informações semelhantes como [tabela ou exibição de estatísticas de executável do SSISDB](/sql/integration-services/system-views/catalog-executable-statistics) que mostra uma linha para cada executável em execução, incluindo suas iterações. Elas são geradas em qualquer nível de log, exceto `None` e úteis para identificar afunilamentos/falhas no nível de tarefa.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| Propriedade                   | Type   | Descrição                                                      | Exemplo                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | A hora do evento no formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Isso é definido como `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | String | A categoria de logs de diagnóstico                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | String | A ID exclusiva para acompanhar uma operação específica                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | O nome do ADF                                             | `MyADFv2` |
| **integrationRuntimeName** | String | O nome do seu IR do SSIS                                         | `MySSISIR` |
| **level**                  | String | O nível dos logs de diagnóstico                                     | `Informational` |
| **executionId**            | String | A ID exclusiva para acompanhar uma execução específica no SSISDB      | `1` (1 significa execuções relacionadas a pacotes **não** armazenados no SSISDB/invocado via T-SQL) |
| **executionPath**          | String | O caminho completo do pacote pai para o componente executado          | `\Transformation\Data Flow Task` (Esse caminho também captura iterações de componente) |
| **startTime**              | String | A hora em que o executável entra na fase de pré-execução no formato UTC  | `2017-06-28T21:00:27.3534352Z` |
| **Final**                | String | A hora em que o executável entra na fase de pós-execução no formato UTC | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | String | O tempo de execução do executável em milissegundos                   | `1,125` |
| **executionResult**        | String | O resultado da execução do executável                                 | `0` (0 significa êxito, 1 significa falha, 2 significa conclusão e 3 significa cancelamento) |
| **executionValue**         | String | O valor definido pelo usuário retornado pela execução do executável            | `1` |
| **resourceId**             | String | A ID exclusiva do recurso do ADF                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>Atributos de log de fases do componente de execução do SSIS

Aqui estão os atributos de log das estatísticas de tempo de execução para componentes de fluxo de dados que são gerados pelas execuções de pacote SSIS em seu IR do SSIS. Eles transmitem informações semelhantes à [tabela ou exibição de fases do componente de execução do SSISDB](/sql/integration-services/system-views/catalog-execution-component-phases) que mostra o tempo gasto pelos componentes de fluxo de dados em todas as fases de execução. Eles são gerados quando você seleciona o `Performance/Verbose` nível de log e é útil para capturar estatísticas de execução de fluxo de dados.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Propriedade                   | Type   | Descrição                                                         | Exemplo                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | A hora do evento no formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Isso é definido como `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | String | A categoria de logs de diagnóstico                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | String | A ID exclusiva para acompanhar uma operação específica                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | O nome do ADF                                                | `MyADFv2` |
| **integrationRuntimeName** | String | O nome do seu IR do SSIS                                            | `MySSISIR` |
| **level**                  | String | O nível dos logs de diagnóstico                                        | `Informational` |
| **executionId**            | String | A ID exclusiva para acompanhar uma execução específica no SSISDB         | `1` (1 significa execuções relacionadas a pacotes **não** armazenados no SSISDB/invocado via T-SQL) |
| **packageName**            | String | O nome do arquivo de pacote executado                              | `MyPackage.dtsx` |
| **Tarefa**               | String | O nome da tarefa de fluxo de dados executada                                 | `Data Flow Task` |
| **SubComponentName**       | String | O nome do componente de fluxo de dados                                     | `Derived Column` |
| **etapas**                  | String | O nome da fase de execução                                         | `AcquireConnections` |
| **startTime**              | String | A hora em que a fase de execução começa no formato UTC                  | `2017-06-28T21:00:27.3534352Z` |
| **Final**                | String | A hora em que a fase de execução termina no formato UTC                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | String | O caminho de execução para a tarefa de fluxo de dados                            | `\Transformation\Data Flow Task` |
| **resourceId**             | String | A ID exclusiva do recurso do ADF                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>Atributos de log de estatísticas de dados de execução do SSIS

Aqui estão os atributos de log de movimentação de dados em cada segmento de pipelines de fluxo de dados, de upstream para componentes downstream, que são gerados por execuções de pacote SSIS em seu IR do SSIS. Eles transmitem informações semelhantes como [tabela de estatísticas de dados de execução do SSISDB ou exibição](/sql/integration-services/system-views/catalog-execution-data-statistics) que mostra as contagens de linha de dados movidos por meio de tarefas de fluxo de dados. Eles são gerados quando você seleciona o `Verbose` nível de log e é útil para calcular a taxa de transferência do fluxo de dados.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Propriedade                     | Type   | Descrição                                                        | Exemplo                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | String | A hora do evento no formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | String | Isso é definido como `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | String | A categoria de logs de diagnóstico                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | String | A ID exclusiva para acompanhar uma operação específica                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | String | O nome do ADF                                               | `MyADFv2` |
| **integrationRuntimeName**   | String | O nome do seu IR do SSIS                                           | `MySSISIR` |
| **level**                    | String | O nível dos logs de diagnóstico                                       | `Informational` |
| **executionId**              | String | A ID exclusiva para acompanhar uma execução específica no SSISDB        | `1` (1 significa execuções relacionadas a pacotes **não** armazenados no SSISDB/invocado via T-SQL) |
| **packageName**              | String | O nome do arquivo de pacote executado                             | `MyPackage.dtsx` |
| **Tarefa**                 | String | O nome da tarefa de fluxo de dados executada                                | `Data Flow Task` |
| **dataflowPathIdString**     | String | A ID exclusiva para acompanhar o caminho de fluxo de dados                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | String | O nome do caminho de fluxo de dados                                         | `ADO NET Source Output` |
| **sourceComponentName**      | String | O nome do componente de fluxo de dados que envia dados                    | `SQLDB Table3` |
| **destinationComponentName** | String | O nome do componente de fluxo de dados que recebe dados                 | `Derived Column` |
| **rowsSent**                 | String | O número de linhas enviadas pelo componente de origem                        | `500` |
| **createdTime**              | String | A hora em que os valores de linha são obtidos no formato UTC                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | String | O caminho de execução para a tarefa de fluxo de dados                           | `\Transformation\Data Flow Task` |
| **resourceId**               | String | A ID exclusiva do recurso do ADF                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="log-analytics-schema"></a>Esquema de Log Analytics

Log Analytics herda o esquema do monitor com as seguintes exceções:

* A primeira letra de cada nome de coluna é capitalizada. Por exemplo, o nome da coluna "CorrelationId" no monitor é "CorrelationId" em Log Analytics.
* Não há nenhuma coluna de "nível".
* A coluna dinâmica "Propriedades" é preservada como o tipo de blob JSON dinâmico a seguir.

    | Azure Monitor coluna | Log Analytics coluna | Type |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dinâmico |
    | $. Properties. Anotações | Anotações | Dinâmico |
    | $. Properties. Entrada | Entrada | Dinâmico |
    | $. Properties. Der | Saída | Dinâmico |
    | $. Properties. Erro. errorCode | ErrorCode | INT |
    | $. Properties. Erro. mensagem | ErrorMessage | string |
    | $. Properties. Ao | Erro | Dinâmico |
    | $. Properties. Predecessores | Predecessores | Dinâmico |
    | $. Properties. Parâmetro | Parâmetros | Dinâmico |
    | US $.properties.SystemParameters | SystemParameters | Dinâmico |
    | $. Properties. Sinalizadores | Marcas | Dinâmico |

## <a name="monitor-ssis-operations-with-azure-monitor"></a>Monitorar operações do SSIS com o Azure Monitor

Para obter & mudar suas cargas de trabalho do SSIS, você pode [provisionar o SSIS ir no ADF](./tutorial-deploy-ssis-packages-azure.md) que dá suporte a:

- Execução de pacotes implantados no catálogo do SSIS (SSISDB) hospedado por uma Instância Gerenciada/servidor do Banco de Dados SQL do Azure (modelo de implantação de projeto)
- Execução de pacotes implantados no sistema de arquivos, nos Arquivos do Azure ou no banco de dados do SQL Server (MSDB) hospedado pela Instância Gerenciada de SQL do Azure (modelo de implantação de pacote)

Depois de provisionado, você pode [verificar o status operacional do SSIS ir usando Azure PowerShell ou no Hub de **monitoramento** do portal do ADF](./monitor-integration-runtime.md#azure-ssis-integration-runtime). Com o modelo de implantação de projeto, os logs de execução de pacote SSIS são armazenados em tabelas ou exibições internas do SSISDB, para que você possa consultar, analisar e apresentá-los visualmente usando ferramentas designadas como o SSMS. Com o modelo de implantação de pacote, os logs de execução de pacote SSIS podem ser armazenados em arquivos do sistema de arquivos ou do Azure como arquivos CSV que você ainda precisa analisar e processar usando outras ferramentas designadas antes de poder consultá-las, analisá-las e apresentá-las visualmente.

Agora, com a integração do [Azure monitor](../azure-monitor/data-platform.md) , você pode consultar, analisar e apresentar visualmente todas as métricas e logs gerados das operações de ir do SSIS e das execuções de pacote do ssis em portal do Azure. Além disso, você também pode gerar alertas sobre eles.

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>Definir configurações de diagnóstico e espaço de trabalho para operações do SSIS

Para enviar todas as métricas e logs gerados das operações de IR do SSIS e das execuções do pacote SSIS para Azure Monitor, você precisa [definir as configurações de diagnóstico e o espaço de trabalho para o ADF](#configure-diagnostic-settings-and-workspace).

### <a name="ssis-operational-metrics"></a>Métricas operacionais do SSIS

As [métricas](../azure-monitor/essentials/data-platform-metrics.md) operacionais do SSIS são contadores de desempenho ou valores numéricos que descrevem o status das operações de início e parada do SSIS ir, bem como as execuções do pacote SSIS em um determinado momento. Eles fazem parte das [métricas do ADF no Azure monitor](#data-factory-metrics).

Quando você definir as configurações de diagnóstico e o espaço de trabalho para o ADF em Azure Monitor, a seleção da caixa de seleção _biométricas_ disponibilizará as métricas operacionais do SSIS para [análise interativa usando o Metrics Explorer do Azure](../azure-monitor/essentials/metrics-getting-started.md), [apresentação no painel do Azure](../azure-monitor/app/tutorial-app-dashboards.md)e [alertas quase em tempo real](../azure-monitor/alerts/alerts-metric.md).

![Nomeie suas configurações e selecione um espaço de trabalho do log Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>Alertas operacionais do SSIS

Para gerar alertas em métricas operacionais do SSIS no portal do ADF, [Selecione a página **alertas & métricas** do Hub do **Monitor** do ADF e siga as instruções passo a passo fornecidas](./monitor-visually.md#alerts).

![Gerando alertas operacionais do SSIS no portal do ADF](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

Para gerar alertas em métricas operacionais do SSIS de portal do Azure, [Selecione a página **alertas** do Hub do Azure **Monitor** e siga as instruções passo a passo fornecidas](#data-factory-alerts).

![Gerando alertas operacionais do SSIS de portal do Azure](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>Logs operacionais do SSIS

[Os logs](../azure-monitor/logs/data-platform-logs.md) operacionais do SSIS são eventos gerados pelas operações de ir do SSIS e pelas execuções do pacote SSIS que fornecem contexto suficiente sobre quaisquer problemas identificados e são úteis para a análise da causa raiz. 

Ao definir as configurações de diagnóstico e o espaço de trabalho para o ADF em Azure Monitor, você pode selecionar os logs operacionais do SSIS relevantes e enviá-los para Log Analytics com base no Azure Data Explorer. Ali, eles serão disponibilizados para [análise usando linguagem de consulta avançada](../azure-monitor/logs/log-query-overview.md), [apresentação no painel do Azure](../azure-monitor/app/tutorial-app-dashboards.md)e [alertas quase em tempo real](../azure-monitor/alerts/alerts-log.md).

![Nomeie suas configurações e selecione um espaço de trabalho do log Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

Os esquemas e o conteúdo dos logs de execução do pacote do SSIS em Azure Monitor e Log Analytics são semelhantes aos esquemas de tabelas ou exibições internas do SSISDB.

| Azure Monitor categorias de log          | Log Analytics tabelas                     | Tabelas/exibições internas do SSISDB              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

Para obter mais informações sobre atributos/propriedades de log operacional do SSIS, consulte [Azure monitor e log Analytics esquemas para ADF](#schema-of-logs-and-events).

Os logs de execução do pacote SSIS selecionados são sempre enviados para Log Analytics independentemente de seus métodos de invocação. Por exemplo, você pode invocar execuções de pacote no SSDT habilitado para Azure, via T-SQL no SSMS, SQL Server Agent ou outras ferramentas designadas, e como execuções disparadas ou de depuração de executar atividades de pacote SSIS em pipelines do ADF.

Ao consultar logs de operação de IR do SSIS na análise de logs, você pode usar propriedades **OperationName** e **ResultType** que são definidas como `Start/Stop/Maintenance` e `Started/InProgress/Succeeded/Failed` , respectivamente. 

![Consultando logs de operação de IR do SSIS em Log Analytics](media/data-factory-monitor-oms/log-analytics-query.png)

Ao consultar logs de execução de pacote do SSIS na análise de logs, você pode associá-los usando / as propriedades CorrelationId de operationId **ExecutionID** /  . **OperationId** / **ExecutionID** sempre é definido como `1` para todas as operações/execuções relacionadas a pacotes **não** armazenados no SSISDB/invocado via T-SQL.

![Consultando logs de execução de pacote SSIS no Log Analytics](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>Próximas etapas
[Monitorar e gerenciar pipelines programaticamente](monitor-programmatically.md)
