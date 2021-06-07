---
title: Arquivar dados de Log Analytics espaço de trabalho para o armazenamento do Azure usando o aplicativo lógico
description: Descreve um método para usar o aplicativo lógico do Azure para consultar dados de um espaço de trabalho Log Analytics e enviar para o armazenamento do Azure.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: fd66aa1f10a32d94d515a1f0aa25c25331796a8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035680"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>Arquivar dados de Log Analytics espaço de trabalho para o armazenamento do Azure usando o aplicativo lógico
Este artigo descreve um método para usar os [aplicativos lógicos do Azure](../../logic-apps/index.yml) para consultar dados de um espaço de trabalho Log Analytics no Azure monitor e enviar para o armazenamento do Azure. Use esse processo quando precisar exportar seus dados de log do Azure Monitor para cenários de auditoria e conformidade ou para permitir que outro serviço recupere esses dados.  

## <a name="other-export-methods"></a>Outros métodos de exportação
O método descrito neste artigo descreve uma exportação agendada de uma consulta de log usando um aplicativo lógico. Outras opções para exportar dados para cenários específicos incluem o seguinte:

- Para exportar todos os dados do seu espaço de trabalho do Log Analytics para uma conta de armazenamento do Azure ou Hub de eventos, use o recurso de exportação de dados do espaço de trabalho Log Analytics dos logs do Azure Monitor. Confira [log Analytics exportação de dados de espaço de trabalho no Azure monitor (versão prévia)](logs-data-export.md)
- Exportar uma vez usando um aplicativo lógico. Consulte [conector de logs de Azure monitor para aplicativos lógicos e automação de energia](logicapp-flow-connector.md).
- Exportar uma vez para o computador local usando o script do PowerShell. Consulte [Invoke-AzOperationalInsightsQueryExport]] ( https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport) .

## <a name="overview"></a>Visão geral
Este procedimento usa o [conector de logs de Azure monitor](/connectors/azuremonitorlogs/) que permite executar uma consulta de log de um aplicativo lógico e usar sua saída em outras ações no fluxo de trabalho. O [conector do armazenamento de BLOBs do Azure](/connectors/azureblob/) é usado neste procedimento para enviar a saída da consulta para o armazenamento do Azure. As outras ações são descritas nas seções abaixo.

![Visão geral do aplicativo lógico](media/logs-export-logic-app/logic-app-overview.png)

Ao exportar dados de um espaço de trabalho Log Analytics, você deve filtrar e agregar seus dados de log e otimizar sua consulta para limitar a quantidade de dados processados pelo fluxo de trabalho do aplicativo lógico aos dados necessários. Por exemplo, se você precisar arquivar eventos de entrada, poderá filtrar os eventos necessários e projetar apenas os campos obrigatórios com a seguinte consulta: 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

Ao exportar os dados em um agendamento, use a função ingestion_time () em sua consulta para garantir que você não perca o atraso nos dados de chegada. Se os dados forem atrasados devido a problemas de rede ou de plataforma, o uso do tempo de ingestão garante que ele será incluído na próxima execução do aplicativo lógico. Consulte a [ação adicionar logs de Azure monitor](#add-azure-monitor-logs-action) para obter um exemplo.

## <a name="prerequisites"></a>Pré-requisitos
Veja a seguir os pré-requisitos que devem ser concluídos antes de concluir este procedimento.

- Workspace do Log Analytics. O usuário que cria o aplicativo lógico deve ter pelo menos permissão de leitura para o espaço de trabalho. 
- Conta de armazenamento do Azure. A conta de armazenamento não precisa estar na mesma assinatura que seu espaço de trabalho do Log Analytics. O usuário que cria o aplicativo lógico deve ter permissão de gravação para a conta de armazenamento. 


## <a name="connector-limits"></a>Limites de conector
Log Analytics consultas de log e de espaço de trabalho no Azure Monitor são serviços multilocação que incluem limites que protegem e isolam clientes e mantêm a qualidade do serviço. Ao consultar uma grande quantidade de dados, você deve considerar os limites a seguir, o que pode afetar a maneira como você configura a recorrência do aplicativo lógico e sua consulta de log:

- As consultas de log não podem retornar mais de 500.000 linhas.
- As consultas de log não podem retornar mais de 64 milhões bytes.
- As consultas de log não podem ser executadas por mais de 10 minutos por padrão. 
- O conector de Log Analytics é limitado à chamada de 100 por minuto.


## <a name="create-container-in-the-storage-account"></a>Criar contêiner na conta de armazenamento
Use o procedimento em [criar um contêiner](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) para adicionar um contêiner à sua conta de armazenamento para manter os dados exportados. O nome usado para o contêiner neste artigo é **loganalytics-data**, mas você pode usar qualquer nome.


## <a name="create-logic-app"></a>Criar aplicativo lógico

Vá para **aplicativos lógicos** no portal do Azure e clique em **Adicionar**. Selecione uma **assinatura**, um **grupo de recursos** e uma **região** para armazenar o novo aplicativo lógico e dê a ele um nome exclusivo. Você pode ativar **log Analytics** configuração para coletar informações sobre dados de tempo de execução e eventos, conforme descrito em [configurar logs de Azure monitor e coletar dados de diagnóstico para aplicativos lógicos do Azure](../../logic-apps/monitor-logic-apps-log-analytics.md). Essa configuração não é necessária para usar o conector de logs de Azure Monitor.

![Criar aplicativo lógico](media/logs-export-logic-app/create-logic-app.png)


Clique em **examinar + criar** e em **criar**. Quando a implantação for concluída, clique em **ir para o recurso** para abrir o **Designer de aplicativos lógicos**.

## <a name="create-a-trigger-for-the-logic-app"></a>Criar um gatilho para o aplicativo lógico
Em **Iniciar com um gatilho comum**, selecione **recorrência**. Isso cria um aplicativo lógico que é executado automaticamente em intervalos regulares. Na caixa **frequência** da ação, selecione **dia** e, na caixa **intervalo** , digite **1** para executar o fluxo de trabalho uma vez por dia.

![Ação de recorrência](media/logs-export-logic-app/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>Ação de adicionar logs de Azure Monitor
Clique em **+ nova etapa** para adicionar uma ação que é executada após a ação de recorrência. Em **escolher uma ação**, digite **Azure monitor** e, em seguida, selecione **logs de Azure monitor**.

![Ação de logs de Azure Monitor](media/logs-export-logic-app/select-azure-monitor-connector.png)

Clique em **log Analytics do Azure – executar consulta e listar resultados**.

![Captura de tela de uma nova ação que está sendo adicionada a uma etapa no designer do aplicativo lógico. Azure Monitor logs é realçado em escolher uma ação.](media/logs-export-logic-app/select-query-action-list.png)

Será solicitado que você selecione um locatário e conceda acesso ao espaço de trabalho do Log Analytics com a conta que o workflow usará para executar a consulta.


## <a name="add-azure-monitor-logs-action"></a>Ação de adicionar logs de Azure Monitor
A ação logs de Azure Monitor permite que você especifique a consulta a ser executada. A consulta de log usada neste exemplo é otimizada para recorrência por hora e coleta os dados ingeridos para o tempo de execução específico. Por exemplo, se o fluxo de trabalho for executado às 4:35, o intervalo de tempo será de 4:00 a 5:00. Se você alterar o aplicativo lógico para execução em uma frequência diferente, também será necessário alterar a consulta. Por exemplo, se você definir a recorrência para executar diariamente, definirá StartTime na consulta para startofday (make_datetime (year, month, Day, 0, 0)). 

Selecione a **assinatura** e o **grupo de recursos** para seu espaço de trabalho log Analytics. Selecione *log Analytics espaço de trabalho* para o **tipo de recurso** e, em seguida, selecione o nome do espaço de trabalho em **nome do recurso**.

Adicione a consulta de logs a seguir à janela **Consultar**.  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

O **intervalo de tempo** especifica os registros que serão incluídos na consulta com base na coluna **TimeGenerated** . Isso deve ser definido como um valor igual ou maior que o intervalo de tempo selecionado na consulta. Como essa consulta não está usando a coluna **TimeGenerated** , a opção **set in Query** não está disponível. Consulte [escopo da consulta](./scope.md) para obter mais detalhes sobre o intervalo de tempo. 

Selecione as **últimas 4 horas** para o **intervalo de tempo**. Isso garantirá que todos os registros com um tempo de ingestão maior do que **TimeGenerated** serão incluídos nos resultados.
   
![Captura de tela das configurações da nova ação logs de Azure Monitor chamada executar consulta e Visualizar resultados.](media/logs-export-logic-app/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>Adicionar atividade de análise JSON (opcional)
A saída da ação **Executar consulta e resultados da lista** é formatada em JSON. Você pode analisar esses dados e manipulá-los como parte da preparação para a ação de **compor** . 

Você pode fornecer um esquema JSON que descreve a carga que você espera receber. O designer analisa o conteúdo JSON usando esse esquema e gera tokens amigáveis que representam as propriedades no conteúdo JSON. É possível então facilmente referenciar e usar essas propriedades em todo o fluxo de trabalho do aplicativo lógico. 


Clique em **+ nova etapa** e, em seguida, clique em **+ Adicionar uma ação**. Em **escolher uma ação**, digite **JSON** e, em seguida, selecione **analisar JSON**.

![Selecionar atividade de análise JSON](media/logs-export-logic-app/select-parse-json.png)

Clique na caixa **conteúdo** para exibir uma lista de valores de atividades anteriores. Selecione **corpo** na ação **Executar consulta e listar resultados** . Essa é a saída da consulta de log.

[![Selecionar corpo](media/logs-export-logic-app/select-body.png)](media/logs-export-logic-app/select-body.png#lightbox)

5.  Clique em **usar conteúdo de exemplo para gerar o esquema**. Execute a consulta de log e copie a saída a ser usada para o conteúdo de exemplo.  Para a consulta de exemplo aqui, você pode usar a seguinte saída:


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![Analisar carga JSON](media/logs-export-logic-app/parse-json-payload.png)

## <a name="add-the-compose-action"></a>Adicionar a ação de composição
A ação **compor** usa a saída JSON analisada e cria o objeto que você precisa armazenar no BLOB.

Clique em **+ nova etapa** e, em seguida, clique em **+ Adicionar uma ação**. Em **escolher uma ação**, digite **compor** e, em seguida, selecione a ação **compor** .

![Selecionar ação de composição](media/logs-export-logic-app/select-compose.png)


Clique na caixa **entradas** para exibir uma lista de valores de atividades anteriores. Selecione **corpo** na ação **analisar JSON** . Esta é a saída analisada da consulta de log.

[![Selecione o corpo da ação de composição](media/logs-export-logic-app/select-body-compose.png)](media/logs-export-logic-app/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>Adicionar a ação criar blob
A ação criar blob grava o JSON composto no armazenamento.

Clique em **+ nova etapa** e, em seguida, clique em **+ Adicionar uma ação**. Em **escolher uma ação**, digite **blob** e, em seguida, selecione a ação **criar blob** .

![Selecione criar blob](media/logs-export-logic-app/select-create-blob.png)

Digite um nome para a conexão com sua conta de armazenamento em **nome da conexão** e, em seguida, clique no ícone de pasta na caixa caminho da **pasta** para selecionar o contêiner em sua conta de armazenamento. Clique no **nome do blob** para ver uma lista de valores de atividades anteriores. Clique em **expressão** e insira uma expressão que corresponda ao intervalo de tempo. Para essa consulta que é executada por hora, a expressão a seguir define o nome do blob por hora anterior: 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![Expressão de BLOB](media/logs-export-logic-app/blob-expression.png)](media/logs-export-logic-app/blob-expression.png#lightbox)

Clique na caixa **conteúdo do blob** para exibir uma lista de valores de atividades anteriores e, em seguida, selecione **saídas** na seção **compor** .


![Criar expressão de BLOB](media/logs-export-logic-app/create-blob.png)


## <a name="test-the-logic-app"></a>Testar o aplicativo lógico
Teste o fluxo de trabalho clicando em **executar**. Se o fluxo de trabalho tiver erros, ele será indicado na etapa com o problema. Você pode exibir as execuções e analisar em cada etapa para exibir a entrada e a saída para investigar falhas. Consulte [solucionar problemas e diagnosticar falhas de fluxo de trabalho no aplicativo lógico do Azure](../../logic-apps/logic-apps-diagnosing-failures.md) , se necessário.

[![Histórico de execuções](media/logs-export-logic-app/runs-history.png)](media/logs-export-logic-app/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>Exibir logs no armazenamento
Vá para o menu **contas de armazenamento** na portal do Azure e selecione sua conta de armazenamento. Clique no bloco **BLOBs** e selecione o contêiner que você especificou na ação criar BLOB. Selecione um dos BLOBs e **edite o blob**.

[![Dados de BLOB](media/logs-export-logic-app/blob-data.png)](media/logs-export-logic-app/blob-data.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [consultas de log no Azure Monitor](./log-query-overview.md).
- Saiba mais sobre os [aplicativos lógicos](../../logic-apps/index.yml)
- Saiba mais sobre a [automatização de energia](https://flow.microsoft.com).