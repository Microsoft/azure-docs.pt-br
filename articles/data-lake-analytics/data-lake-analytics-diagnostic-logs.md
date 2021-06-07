---
title: Habilitar e exibir logs de diagnóstico para o Azure Data Lake Analytics
description: Entenda como configurar e acessar os logs de diagnóstico do Azure Data Lake Analytics
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 02/12/2018
ms.openlocfilehash: cd339729f2300ff7e13e7422bf73373b4ce4658e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221002"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Acessando os logs de diagnóstico do Azure Data Lake Analytics

O registro em log de diagnóstico permite que você colete as trilhas de auditoria de acesso a dados. Esses logs fornecem informações como:

* Uma lista de usuários que acessaram os dados.
* Com que frequência os dados são acessados.
* Quantos dados são armazenados na conta.

## <a name="enable-logging"></a>Habilitar o registro em log

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Abra sua conta do Data Lake Analytics e selecione **Logs de diagnóstico** na seção __Monitorar__. Em seguida, selecione __Ativar o diagnóstico__.

    ![Captura de tela que mostra a ação "logs de diagnóstico" selecionada e "ativar diagnóstico para coletar os seguintes logs" realçado.](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. De __configurações de Diagnóstico__, insira um __Nome__ para essa configuração de registro em log e opções de log, em seguida, selecione.

    ![Ativar o diagnóstico para coletar logs de auditoria e de solicitações](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Habilitar logs de diagnóstico")

   * Você pode optar por armazenar/processar os dados de três maneiras diferentes.

     * Selecione __Arquivar em uma conta de armazenamento__ para armazenar os logs em uma conta de armazenamento do Azure. Use esta opção se quiser arquivar os dados. Se escolher esta opção, você deverá fornecer uma conta de armazenamento do Azure para salvar os logs.

     * Selecione **Transmitir para um Hub de Eventos** para transmitir os dados de log para um Hub de Eventos do Azure. Use essa opção se tiver um pipeline de processamento downstream que esteja analisando logs de entrada em tempo real. Se escolher esta opção, você deverá fornecer os detalhes no Hub de Eventos do Azure que deseja usar.

     * Selecione __Enviar para log Analytics__ para enviar os dados para o serviço de Azure monitor. Use esta opção se você quiser usar logs de Azure Monitor para coletar e analisar logs.
   * Especifique se deseja obter os logs de auditoria, os logs de solicitação ou ambos.  Um log de solicitação captura todas as solicitações da API. Um log de auditoria registra todas as operações disparadas pela solicitação de API.

   * Para __Arquivar para uma conta de armazenamento__, especifique o número de dias a reter os dados.

   * Clique em __Salvar__.

        > [!NOTE]
        > Selecione __Arquivar em uma conta de armazenamento__, __Transmitir para um Hub de Eventos__ ou __Enviar para o Log Analytics__ antes de clicar no botão __Salvar__.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Use a conta de Armazenamento do Azure que contém dados de log

1. Para exibir os contêineres de blob que contêm dados de log, abra a conta de Armazenamento do Azure usada para análise Data Lake para registro em log e, em seguida, clique em __Blobs__.

   * O contêiner **insights-logs-audit** contém os logs de auditoria.
   * O contêiner **insights-logs-requests** contém os logs de solicitação.

2. Nesses contêineres, os logs são armazenados na estrutura a seguir:

   ```text
   resourceId=/
     SUBSCRIPTIONS/
       <<SUBSCRIPTION_ID>>/
         RESOURCEGROUPS/
           <<RESOURCE_GRP_NAME>>/
             PROVIDERS/
               MICROSOFT.DATALAKEANALYTICS/
                 ACCOUNTS/
                   <DATA_LAKE_ANALYTICS_NAME>>/
                     y=####/
                       m=##/
                         d=##/
                           h=##/
                             m=00/
                               PT1H.json
   ```

   > [!NOTE]
   > A folha `##` no caminho contêm o ano, o mês, o dia e a hora em que o log foi criado. O Data Lake Analytics cria um arquivo a cada hora e, portanto, o `m=` sempre conterá um valor `00`.

    Como um exemplo, o caminho completo para um log de auditoria poderia ser:

    `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    De modo semelhante, o caminho completo para um log de solicitação poderia ser:

    `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="log-structure"></a>Estrutura de log

Os logs de auditoria e solicitação estão em um formato JSON estruturado.

### <a name="request-logs"></a>Logs de Solicitação

Aqui está um exemplo de entrada no log de solicitação formatado em JSON. Cada blob tem um objeto-raiz chamado **registros** que contém uma matriz de objetos do log.

```json
{
"records":
  [
    . . . .
    ,
    {
         "time": "2016-07-07T21:02:53.456Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
         "category": "Requests",
         "operationName": "GetAggregatedJobHistory",
         "resultType": "200",
         "callerIpAddress": "::ffff:1.1.1.1",
         "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
         "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
         "properties": {
             "HttpMethod":"POST",
             "Path":"/JobAggregatedHistory",
             "RequestContentLength":122,
             "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
             "StartTime":"2016-07-07T21:02:52.472Z",
             "EndTime":"2016-07-07T21:02:53.456Z"
             }
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>Esquema do log de solicitação

| Nome | Tipo | Descrição |
| --- | --- | --- |
| time |String |O carimbo de data/hora (em UTC) do log |
| resourceId |String |O identificador do recurso em que a operação ocorreu |
| category |String |A categoria do log. Por exemplo, **Solicitações**. |
| operationName |String |Nome da operação que está registrada. Por exemplo, GetAggregatedJobHistory. |
| resultType |String |O status da operação, por exemplo, 200. |
| callerIpAddress |String |O endereço IP do cliente que está fazendo a solicitação |
| correlationId |String |O identificador do log. Esse valor pode ser usado para agrupar um conjunto de entradas de log relacionadas. |
| identidade |Objeto |A identidade que gerou o log |
| properties |JSON |Veja a próxima seção (Esquema de propriedades do log de solicitação) para obter detalhes |

#### <a name="request-log-properties-schema"></a>Esquema de propriedades do log de solicitação

| Nome | Tipo | Descrição |
| --- | --- | --- |
| HttpMethod |String |O método HTTP usado para a operação. Por exemplo, GET. |
| Caminho |String |O caminho em que a operação foi executada |
| RequestContentLength |INT |O comprimento do conteúdo da solicitação HTTP |
| ClientRequestId |String |O identificador que identifica essa solicitação com exclusividade |
| StartTime |String |A hora em que o servidor recebeu a solicitação |
| EndTime |String |A hora em que o servidor enviou uma resposta |

### <a name="audit-logs"></a>Logs de auditoria

Aqui está um exemplo de entrada no log de auditoria formatado em JSON. Cada blob tem um objeto-raiz chamado **registros** que contém uma matriz de objetos do log.

```json
{
"records":
  [
    {
         "time": "2016-07-28T19:15:16.245Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
         "category": "Audit",
         "operationName": "JobSubmitted",
         "identity": "user@somewhere.com",
         "properties": {
             "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
             "JobName": "New Job",
             "JobRuntimeName": "default",
             "SubmitTime": "7/28/2016 7:14:57 PM"
             }
    }
  ]
}
```

#### <a name="audit-log-schema"></a>Esquema do log de auditoria

| Nome | Tipo | Descrição |
| --- | --- | --- |
| time |String |O carimbo de data/hora (em UTC) do log |
| resourceId |String |O identificador do recurso em que a operação ocorreu |
| category |String |A categoria do log. Por exemplo, **Auditoria**. |
| operationName |String |Nome da operação que está registrada. Por exemplo, JobSubmitted. |
| resultType |String |Um substatus para o status do trabalho (operationName). |
| resultSignature |String |Detalhes adicionais sobre o status do trabalho (operationName). |
| identidade |String |O usuário que solicitou a operação. Por exemplo, susan@contoso.com. |
| properties |JSON |Veja a próxima seção (Esquema de propriedades do log de auditoria) para obter detalhes |

> [!NOTE]
> **resultType** e **resultSignature** fornecem informações sobre o resultado de uma operação e conter apenas um valor caso uma operação tenha sido concluída. Por exemplo, eles contêm um valor somente quando **operationName** contém um valor **JobStarted** ou **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades do log de auditoria

| Nome | Tipo | Descrição |
| --- | --- | --- |
| JobId |String |A ID atribuída ao trabalho |
| JobName |String |O nome fornecido para o trabalho |
| JobRunTime |String |O runtime usado para processar o trabalho |
| SubmitTime |String |A hora (em UTC) em que o trabalho foi enviado |
| StartTime |String |A hora em que o trabalho começou a ser executado após o envio (em UTC) |
| EndTime |String |A hora em que o trabalho foi concluído |
| Paralelismo |String |O número de unidades do Data Lake Analytics solicitadas para esse trabalho durante o envio |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime** e **Parallelism** fornecem informações sobre uma operação. Essas entradas contêm um valor apenas se operação tiver sido iniciada ou concluída. Por exemplo, **SubmitTime** somente contém um valor após **operationName** ter o valor **JobSubmitted**.

## <a name="process-the-log-data"></a>Processar os dados de log

O Azure Data Lake Analytics fornece um exemplo sobre como processar e analisar os dados do log. Você pode encontrar o exemplo em [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) .

## <a name="next-steps"></a>Próximas etapas

[Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
