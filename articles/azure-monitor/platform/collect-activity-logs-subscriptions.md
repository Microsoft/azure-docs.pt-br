---
title: Coletar Logs de atividades do Azure em um espaço de trabalho do Log Analytics em assinaturas | Microsoft Docs
description: Use os Hubs de eventos e os aplicativos lógicos para coletar dados de Log de atividades do Azure e enviá-lo para um espaço de trabalho do Log Analytics no Azure Monitor em um locatário diferente.
services: log-analytics, logic-apps, event-hubs
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: magoedte
ms.openlocfilehash: 76a789bd4f9167344fcc240e3f3fdbf3a00aa5a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742322"
---
# <a name="collect-azure-activity-logs-into-a-log-analytics-workspace-across-subscriptions-in-different-azure-active-directory-tenants"></a>Coletar Logs de atividades do Azure em um espaço de trabalho do Log Analytics em assinaturas em diferentes locatários do Azure Active Directory

Este artigo aborda um método para coletar Logs de atividades do Azure em um espaço de trabalho do Log Analytics no Azure Monitor usando o conector do coletor de dados do Azure Log Analytics para aplicativos lógicos. Use o processo neste artigo quando precisar enviar logs para um workspace em um locatário diferente do Azure Active Directory. Por exemplo, se você for um provedor de serviço gerenciado, convém coletar logs de atividade de assinatura do cliente e armazená-los em um espaço de trabalho do Log Analytics em sua própria assinatura.

Se o espaço de trabalho do Log Analytics estiver na mesma assinatura do Azure, ou em uma assinatura diferente, mas no mesmo Active Directory do Azure, use as etapas na [solução de log de atividades do Azure](collect-activity-logs.md) para coletar logs de atividades do Azure.

## <a name="overview"></a>Visão geral

A estratégia usada neste cenário é fazer com que o log de atividades do Azure envie eventos para um [Hub de eventos](../../event-hubs/event-hubs-about.md) onde um [aplicativo lógico](../../logic-apps/logic-apps-overview.md) envia para seu espaço de trabalho do Log Analytics. 

![imagem do fluxo de dados do log de atividades para o espaço de trabalho do Log Analytics](media/collect-activity-logs-subscriptions/data-flow-overview.png)

As vantagens dessa abordagem incluem:
- Baixa latência, uma vez que o log de atividades do Azure é transmitido para o Hub de Eventos.  O aplicativo lógico, em seguida, é disparado e envia os dados ao espaço de trabalho. 
- É necessário pouco código e não há nenhuma infraestrutura de servidor para implantar.

Este artigo orienta você sobre como:
1. Criar um Hub de Eventos. 
2. Exportar logs de atividades para um Hub de Eventos usando o perfil de exportação do log de atividades do Azure.
3. Crie um aplicativo lógico para ler do Hub de eventos e enviar eventos para o espaço de trabalho do Log Analytics.

## <a name="requirements"></a>Requisitos
A seguir estão os requisitos para os recursos do Azure usados neste cenário.

- O namespace de Hub de Eventos não precisa estar na mesma assinatura que emite os logs. O usuário que define a configuração deve ter as devidas permissões de acesso para ambas as assinaturas. Se você tiver várias assinaturas no mesmo Azure Active directory, você pode enviar os logs de atividade para todas as assinaturas para um hub de eventos único.
- O aplicativo lógico pode ser em uma assinatura diferente do hub de eventos e não precisa estar no mesmo Active Directory do Azure. O aplicativo lógico lê do Hub de Eventos usando a chave de acesso compartilhado do Hub de Eventos.
- O espaço de trabalho do Log Analytics pode estar em uma assinatura e Active Directory do Azure diferentes do aplicativo lógico, mas para simplificar, é recomendável que estejam na mesma assinatura. O aplicativo lógico envia para o espaço de trabalho usando a chave e a ID do espaço de trabalho do Log Analytics.



## <a name="step-1---create-an-event-hub"></a>Etapa 1 - Criar um Hub de Eventos

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../../event-hubs/event-hubs-create.md) to create your event hub. -->

1. No portal do Azure, selecione **Criar um recurso** > **Internet das Coisas** > **Hubs de Eventos**.

   ![Novo hub de eventos do Marketplace](media/collect-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. Em **Criar namespace**, digite um novo namespace ou selecione um existente. O sistema imediatamente verifica para ver se o nome está disponível.

   ![imagem da caixa de diálogo criar hub de evento](media/collect-activity-logs-subscriptions/create-event-hub1.png)

4. Escolha o tipo de preços (básico ou padrão), uma assinatura do Azure, o grupo de recursos e o local para o novo recurso.  Clique em **Criar** para criar o namespace. Talvez você precise aguardar alguns minutos para o sistema provisionar totalmente os recursos.
6. Clique no namespace que você acabou de criar na lista.
7. Selecione **Políticas de acesso compartilhado** e, em seguida, clique em **RootManageSharedAccessKey**.

   ![imagem das políticas de acesso compartilhado do hub de eventos](media/collect-activity-logs-subscriptions/create-event-hub7.png)
   
8. Clique no botão de cópia para copiar a cadeia de conexão **RootManageSharedAccessKey** na área de transferência. 

   ![imagem da chave de acesso compartilhado do hub de eventos](media/collect-activity-logs-subscriptions/create-event-hub8.png)

9. Em um local temporário, como o bloco de notas, mantenha uma cópia do nome do Hub de Eventos e da cadeia de conexão primária ou secundária do Hub de Eventos. O aplicativo lógico requer esses valores.  Para a cadeia de conexão do Hub de Eventos, você pode usar a cadeia de conexão **RootManageSharedAccessKey** ou criar uma separada.  A cadeia de conexão que você usa deve começar com `Endpoint=sb://` e ser de uma política que tenha a política de acesso **Gerenciar**.


## <a name="step-2---export-activity-logs-to-event-hub"></a>Etapa 2 - Exportar os Logs de Atividade para o Hub de Eventos

Para habilitar a transmissão do Log de Atividades, você pode escolher um Namespace do Hub de Eventos e uma política de acesso compartilhado para esse namespace. Um Hub de Eventos é criado no namespace quando ocorre o primeiro evento de Log de Atividades de novo. 

Você pode usar um namespace de hub de eventos que não esteja na mesma assinatura que o emissor de logs, no entanto, as assinaturas devem estar no mesmo Active Directory do Azure. O usuário que define a configuração deve ter o devido RBAC para acessar ambas as assinaturas. 

1. No portal do Azure, selecione **Monitorar** > **Log de atividades**.
3. Clique no botão **Exportar** na parte superior da página.

   ![imagem do monitor do azure na barra de navegação](media/collect-activity-logs-subscriptions/activity-log-blade.png)

4. Selecione a **Assinatura** da qual exportar, em seguida, clique em **Selecionar tudo** na lista suspensa **Regiões** para selecionar os eventos para os recursos em todas as regiões. Clique na caixa de seleção **Exportar para um hub de eventos**.
7. Clique em **namespace de barramento de serviço** e, em seguida, selecione a **Assinatura** com o hub de eventos, o **namespace de hub de eventos**e um **nome da política de hub de eventos**.

    ![imagem de exportação para a página do hub de eventos](media/collect-activity-logs-subscriptions/export-activity-log2.png)

11. Clique em **OK** e, em seguida, **Salvar** para salvar essas configurações. As configurações serão aplicadas imediatamente à sua assinatura.

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>Etapa 3 - Criar Aplicativo Lógico

Depois que os logs de atividade estiver escrevendo para o hub de eventos, você pode criar um aplicativo lógico para coletar os logs do hub de eventos e gravá-los no espaço de trabalho do Log Analytics.

O aplicativo lógico inclui o seguinte:
- Um gatilho do [conector do Hub de Eventos](https://docs.microsoft.com/connectors/eventhubs/) para ler do Hub de Eventos.
- Um [ação Parse JSON](../../logic-apps/logic-apps-content-type.md) para extrair os eventos JSON.
- Uma [ação compor](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) para converter o JSON em um objeto.
- Um [conector de dados de envio de Log Analytics](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) para enviar os dados no espaço de trabalho do Log Analytics.

   ![imagem de adição de gatilho do hub de eventos em aplicativos lógicos](media/collect-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>Requisitos do aplicativo lógico
Antes de criar seu aplicativo lógico, verifique se que você tem as seguintes informações de etapas anteriores:
- Nome do Hub de Eventos
- Cadeia de conexão (primária ou secundária) do Hub de Eventos para o namespace do Hub de Eventos.
- ID do espaço de trabalho do Log Analytics
- Chave compartilhada do Log Analytics

Para obter o cadeia de conexão e o nome do Hub de Eventos, siga as etapas em [Verificar permissões do namespace dos Hubs de Eventos e localizar a cadeia de conexão](../../connectors/connectors-create-api-azure-event-hubs.md#permissions-connection-string).


### <a name="create-a-new-blank-logic-app"></a>Criar um aplicativo lógico em branco

1. No portal do Azure, escolha **Criar um recurso** > **Integração corporativa** > **Aplicativo Lógico**.

    ![Novo aplicativo lógico do Marketplace](media/collect-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. Insira as configurações na tabela a seguir.

    ![Criar aplicativo lógico](media/collect-activity-logs-subscriptions/create-logic-app.png)

   |Configuração | DESCRIÇÃO  |
   |:---|:---|
   | NOME           | Nome exclusivo para o aplicativo lógico. |
   | Assinatura   | Selecione a assinatura do Azure que contém o aplicativo lógico. |
   | Grupo de recursos | Selecione um grupo de recursos do Azure existente ou crie um novo para o aplicativo lógico. |
   | Local padrão       | Selecione a região do datacenter para implantar seu aplicativo lógico. |
   | Log Analytics  | Selecione se você quiser registrar o status de cada execução do seu aplicativo lógico em um espaço de trabalho do Log Analytics.  |

    
3. Selecione **Criar**. Quando aparecer uma notificação de **Implantação bem-sucedida**, clique em **Acessar recursos** para abrir seu aplicativo lógico.

4. Em **Modelos**, escolha **Aplicativo lógico em branco**. 

O Designer de Aplicativos Lógicos agora mostra os conectores disponíveis e seus gatilhos, que você usa para iniciar o fluxo de trabalho do aplicativo lógico.

<!-- Learn [how to create a logic app](../../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>Adicionar gatilho do Hub de Eventos

1. Na caixa de pesquisa do Designer de Aplicativo Lógico, insira *hubs de eventos* para seu filtro. Selecione o gatilho **Hubs de Eventos - Quando os eventos estiverem disponíveis no Hub de Eventos**.

   ![imagem de adição de gatilho do hub de eventos em aplicativos lógicos](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. Quando você for solicitado a fornecer credenciais, conecte-se ao namespace do Hubs de Eventos. Insira um nome à sua conexão e, em seguida, a cadeia de conexão que você copiou.  Selecione **Criar**.

   ![imagem de adição da conexão do hub de eventos em aplicativos lógicos](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. Depois de criar a conexão, edite as configurações para o gatilho. Iniciar selecionando **insights-operacional-logs** da lista suspensa **nome do Hub de Eventos**.

   ![Quando os eventos estiverem disponíveis na caixa de diálogo](media/collect-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. Expanda **Mostrar opções avançadas** e altere **tipo de conteúdo** para *application/json*

   ![Caixa de diálogo de configuração do Hub de Eventos](media/collect-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>Adicionar a ação Parse JSON

A saída do Hub de Eventos contém uma carga JSON com uma matriz de registros. O [Parse JSON](../../logic-apps/logic-apps-content-type.md) ação é usada para extrair apenas o conjunto de registros para enviar ao espaço de trabalho do Log Analytics.

1. Clique em **Nova etapa** > **Adicionar uma ação**
2. Na caixa de pesquisa, digite *Parse JSON* como filtro. Selecione a ação **Operações de dados - Parse JSON**.

   ![Adicionar ação parse json em aplicativos lógicos](media/collect-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. Clique no campo **Conteúdo** e, em seguida, selecione *Corpo*.

4. Copie e cole o seguinte esquema no campo **Esquema**.  Esse esquema corresponde à saída de uma ação de Hub de Eventos.  

   ![Caixa de diálogo Parse JSON](media/collect-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> Você pode obter uma carga de exemplo clicando em **Executar** e examinando a **Saída bruta** do Hub de Eventos.  Você pode usar essa saída com **Usar carga de exemplo para gerar o esquema** na atividade **Parse JSON** para gerar o esquema.

### <a name="add-compose-action"></a>Adicionar ação compor
A ação [Compor](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) leva a saída JSON e cria um objeto que pode ser usado pela ação do Log Analytics.

1. Clique em **Nova etapa** > **Adicionar uma ação**
2. Digite *Compor* para seu filtro e, em seguida, selecione a ação **Operações de dados - Compor**.

    ![Adicionar ação compor](media/collect-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. Clique no campo **Entradas** e selecione **Corpo** na atividade **Parse JSON**.


### <a name="add-log-analytics-send-data-action"></a>Adicionar ação de enviar dados do Log Analytics
O [coletor de dados do Azure Log Analytics](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) ação usa o objeto da ação de composição e os envia para um espaço de trabalho do Log Analytics.

1. Clique em **Nova etapa** > **Adicionar uma ação**
2. Digite *log analytics* para seu filtro e, em seguida, selecione a ação **Coletor de Dados do Log Analytics do Azure - Enviar dados**.

   ![Adicionando ação de envio de dados da análise de logs nos aplicativos lógicos](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. Insira um nome para sua conexão e cole a **ID do espaço de trabalho** e a **chave do espaço de trabalho** para seu espaço de trabalho do Log Analytics.  Clique em **Criar**.

   ![Adicionando conexão da análise de logs nos aplicativos lógicos](media/collect-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. Depois de criar a conexão, edite as configurações na tabela abaixo. 

    ![Configure a ação de envio de dados](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |Configuração        | Valor           | DESCRIÇÃO  |
   |---------------|---------------------------|--------------|
   |Corpo da solicitação JSON  | **Saída** da ação **Compor** | Recupera os registros do corpo da ação Compor. |
   | Campo de Log Personalizado | AzureActivity | Nome da tabela de log personalizado para criar espaço de trabalho do Log Analytics para manter os dados importados. |
   | Time-generated-field | tempo real | Não selecione o campo JSON para **tempo** - apenas digite a palavra tempo. Se você selecionar o campo JSON o designer coloca a ação **Enviar dados** em um loop *Para cada*, que é não o que você deseja. |




10. Clique em **Salvar** para salvar as alterações feitas no seu aplicativo lógico.

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>Etapa 4 - Teste e solução de problemas do aplicativo lógico
Com o fluxo de trabalho concluído, você pode testar no designer para verificar se ele está funcionando sem erros.

No Designer de Aplicativos Lógicos, clique em **Executar** para testar o aplicativo lógico. Cada etapa no aplicativo lógico mostra um ícone de status, com uma marca de seleção branca em um círculo verde, indicando êxito.

   ![Testar aplicativo lógico](media/collect-activity-logs-subscriptions/test-logic-app.png)

Para ver informações detalhadas sobre cada etapa, clique no nome da etapa para expandi-lo. Clique em **Mostrar entradas brutas** e **Mostrar saídas brutas** para obter mais informações sobre os dados recebidos e enviados em cada etapa.

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>Etapa 5 - Exibir o log de atividades do Azure no Log Analytics
A etapa final é verificar o espaço de trabalho do Log Analytics para certificar-se de que os dados estão sendo coletados conforme o esperado.

1. No Portal do Azure, clique em **Todos os serviços**, localizado no canto superior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
2. Na lista de workspaces do Log Analytics, selecione um workspace.
3.  Clique no bloco **Pesquisa de Logs** e, no painel da Pesquisa de Logs, no campo de consulta, digite `AzureActivity_CL` e, em seguida, pressione Enter ou clique no botão de pesquisa à direita do campo de consulta. Se você não nomeou seu log personalizado *AzureActivity*, digite o nome que você escolheu e acrescente `_CL`.

>[!NOTE]
> Na primeira vez que um novo log personalizado é enviado para o espaço de trabalho do Log Analytics-lo pode levar até uma hora para o log personalizado a ser pesquisada.

>[!NOTE]
> Os logs de atividade são gravados em uma tabela personalizada e não aparecem na [solução do log de atividades](./collect-activity-logs.md).


![Testar aplicativo lógico](media/collect-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um aplicativo lógico para ler os Logs de atividade do Azure de um Hub de eventos e enviá-los para o espaço de trabalho do Log Analytics para análise. Para saber mais sobre visualização de dados em um espaço de trabalho, incluindo a criação de painéis, revise o tutorial para visualizar dados.

> [!div class="nextstepaction"]
> [Tutorial Visualizar dados de pesquisa de logs](./../../azure-monitor/learn/tutorial-logs-dashboards.md)
