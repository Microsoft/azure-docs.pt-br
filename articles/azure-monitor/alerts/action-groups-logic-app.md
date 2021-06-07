---
title: Como disparar ações complexas com alertas do Azure Monitor
description: Saiba como criar uma ação do aplicativo lógico para processar alertas do Azure Monitor.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: a1371e00a6d4c5db609466e25c9d94aad5e73398
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045710"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Como disparar ações complexas com alertas do Azure Monitor

Este artigo mostra como configurar e disparar um aplicativo lógico para criar uma conversa no Microsoft Teams quando um alerta é acionado.

## <a name="overview"></a>Visão geral

Quando um alerta do Azure Monitor dispara, ele chama um [grupo de ações](./action-groups.md). Os grupos de ações permitem que você dispare uma ou mais ações para notificar os outros sobre o alerta e até mesmo corrigi-lo.

O processo geral é:

-   Criar o Aplicativo Lógico para o respectivo tipo de alerta

-   Importe um conteúdo de exemplo para o respectivo tipo de alerta para o aplicativo lógico.

-   Definir o comportamento do Aplicativo Lógico

-   Copiar o ponto de extremidade HTTP do aplicativo lógico em um grupo de ação do Microsoft Azure.

O processo será semelhante se você quiser que o aplicativo lógico execute uma ação diferente.

## <a name="create-an-activity-log-alert-administrative"></a>Criar um alerta de log de Atividades – Administrativo

1. [Criar um aplicativo lógico](~/articles/logic-apps/quickstart-create-first-logic-app-workflow.md)

2.  Selecione o gatilho **Quando uma solicitação HTTP for recebida**.

1. Na caixa de diálogo **Quando uma solicitação HTTP é recebida**, selecione **Use o conteúdo de amostra para gerar o esquema**.

    ![Captura de tela que mostra a caixa de diálogo Quando uma solicitação H T T P e a opção Usar o conteúdo de amostra para gerar o esquema selecionada. ](~/articles/app-service/media/tutorial-send-email/generate-schema-with-payload.png)

3.  Copie e cole o seguinte conteúdo de exemplo na caixa de diálogo:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. O **Designer do Aplicativo Lógico** exibirá uma janela pop-up lembrando que a solicitação enviada ao Aplicativo Lógico deve definir o cabeçalho **Content-Type** para **aplicativo/json**. Fechar a janela pop-up. O alerta do Azure Monitor define o cabeçalho.

    ![Definir o cabeçalho Content-Type](media/action-groups-logic-app/content-type-header.png "Definir o cabeçalho Content-Type")

10. Selecione **+** **nova etapa** e, em seguida, escolha **Adicionar uma ação**.

    ![Adicionar uma ação](media/action-groups-logic-app/add-action.png "Adicionar uma ação")

11. Pesquise e selecione o conector do Microsoft Teams. Escolha a ação **Microsoft Teams-post Message** .

    ![Ações do Microsoft Teams](media/action-groups-logic-app/microsoft-teams-actions.png "Ações do Microsoft Teams")

12. Configure a ação do Microsoft Teams. O **Designer de aplicativos lógicos** solicita que você autentique sua conta corporativa ou de estudante. Escolha a **ID da Equipe** e **ID do Canal** para enviar a mensagem.

13. Configure a mensagem usando uma combinação de texto estático e referências para o \<fields\> no conteúdo dinâmico. Recorte e cole o texto a seguir no campo **Mensagem**.

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Em seguida, pesquise e substitua o \<fields\> por marcas de conteúdo dinâmico de mesmo nome.

    > [!NOTE]
    > Há dois campos dinâmicos nomeados **status**. Adicione ambos os campos à mensagem. Use um no recipiente de propriedades **activityLog** e exclua o outro campo. Passe o cursor sobre o campo **status** para ver a referência de campo completa, conforme mostrado na captura de tela a seguir:

    ![Ação do Microsoft Teams: postar uma mensagem](media/action-groups-logic-app/teams-action-post-message.png "Ação do Microsoft Teams: postar uma mensagem")

14. Na parte superior do **Designer de Aplicativos Lógicos**, selecione **Salvar** para salvar seu aplicativo lógico.

15. Abra o grupo de ações existente e adicione uma ação para referenciar o aplicativo lógico. Se você não tiver um grupo de ações existente, consulte [criar e gerenciar grupos de ações no portal do Azure](./action-groups.md) para criar um. Não se esqueça de salvar as alterações.

    ![Atualizar o grupo de ações](media/action-groups-logic-app/update-action-group.png "Atualizar o grupo de ações")

Na próxima vez que um alerta chamar o grupo de ações, o aplicativo lógico será chamado.

## <a name="create-a-service-health-alert"></a>Criar um alerta de integridade do serviço

As entradas da Integridade de Serviço do Azure são parte do registro de atividade. O processo para criar o alerta é semelhante para [criar um alerta de registro de atividade](#create-an-activity-log-alert-administrative),mas com algumas alterações:

- As etapas de 1 a 3 são as mesmas.
- Para a etapa 4, use o seguinte conteúdo de exemplo para o gatilho de solicitação HTTP:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{\"ImpactedRegions\"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  As etapas 5 e 6 são as mesmas.
-  Para as etapas 7 a 11, use o seguinte processo:

   1. Selecione **+** **nova etapa** e, em seguida, escolha **Adicionar uma condição**. Defina as condições a seguir para que o aplicativo lógico seja executado quando os dados de entrada corresponderem aos valores abaixo.  Ao inserir o valor da versão na caixa de texto, coloque aspas ("0.1.1") para certificar-se de que ele seja avaliado como uma cadeia de caracteres e não um tipo numérico.  O sistema não mostra as aspas se você retorna à página, mas o código subjacente ainda mantém o tipo de cadeia de caracteres.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Condição de carga de integridade do serviço"](media/action-groups-logic-app/service-health-payload-condition.png "Condição de carga de integridade do serviço")

   1. Na condição **se verdadeiro**, siga as instruções nas etapas de 11 a 13 em [Criar um alerta do registro de atividade](#create-an-activity-log-alert-administrative) para adicionar a ação da Microsoft Teams.

   1. Defina a mensagem usando uma combinação de HTML e conteúdo dinâmico. Recorte e cole o conteúdo a seguir no campo **Mensagem**. Substitua os campos `[incidentType]`, `[trackingID]`, `[title]`, e `[communication]` pela marcação de conteúdo dinâmica de mesmo nome:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Ação post da condição verdadeira de integridade do serviço"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Ação de postagem da condição verdadeira de integridade do serviço")

   1. Para a condição **Se falso** forneça uma mensagem útil:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Ação post da condição do Service Health false"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Ação de postagem condição do serviço de integridade falsa")

- A etapa 15 é a mesma. Siga as instruções para salvar seu aplicativo lógico e atualizar seu grupo de ações.

## <a name="create-a-metric-alert"></a>Criar um alerta de métrica

O processo para criar o alerta métrico é semelhante para [criar um alerta de registro de atividade](#create-an-activity-log-alert-administrative),mas com algumas alterações:

- As etapas de 1 a 3 são as mesmas.
- Para a etapa 4, use o seguinte conteúdo de exemplo para o gatilho de solicitação HTTP:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- As etapas 5 e 6 são as mesmas.
- Para as etapas 7 a 11, use o seguinte processo:

  1. Selecione **+** **nova etapa** e, em seguida, escolha **Adicionar uma condição**. Defina as condições a seguir para que o aplicativo lógico seja executado quando os dados de entrada corresponderem aos valores abaixo. Ao inserir o valor da versão na caixa de texto, coloque aspas ("2.0") para certificar-se de que ele seja avaliado como uma cadeia de caracteres e não um tipo numérico.  O sistema não mostra as aspas se você retorna à página, mas o código subjacente ainda mantém o tipo de cadeia de caracteres. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Condição da carga do alerta de métrica"](media/action-groups-logic-app/metric-alert-payload-condition.png "Condição de carga de alerta de métrica")

  1. Na condição **se verdadeiro**, adicione um **Para cada** loop e a ação da Microsoft Teams. Defina a mensagem usando uma combinação de HTML e conteúdo dinâmico.

      !["Ação de postagem da condição verdadeira de alerta de métrica"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Ação de postagem da condição verdadeira do alerta de métrica")

  1. Na condição **If false** , defina uma ação do Microsoft Teams para comunicar que o alerta de métrica não corresponde às expectativas do aplicativo lógico. Inclui o conteúdo JSON. Observe como referenciar o `triggerBody` conteúdo dinâmico na `json()` expressão.

      !["Ação de postagem da condição falsa de alerta de métrica"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Ação de postagem da condição falsa de alerta de métrica")

- A etapa 15 é a mesma. Siga as instruções para salvar seu aplicativo lógico e atualizar seu grupo de ações.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Chamar outros aplicativos além do Microsoft Teams
Aplicativos Lógicos têm um número de diferentes conectores que permitem disparar ações em um intervalo amplo de aplicativos e bancos de dados. Slack, SQL Server, Oracle, Salesforce, são apenas alguns exemplos. Para obter mais informações sobre conectores, consulte [Conectores do aplicativo lógico](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Próximas etapas
* Obtenha uma [visão geral dos alertas do log de atividades do Microsoft Azure](./alerts-overview.md) e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de integridade do Serviço do Azure é publicada](../../service-health/alerts-activity-log-service-notifications-portal.md).
* Saiba mais sobre [grupos de ação](./action-groups.md).