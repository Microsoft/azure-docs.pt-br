---
title: Exibindo logs de diagnóstico do Azure Data Lake Storage Gen1 | Microsoft Docs
description: 'Entender como configurar e acessar os logs de diagnóstico do Azure Data Lake Storage Gen1 '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 0b84e26962b00ee5b9d4c34cab7efbcc9aa0bf01
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582800"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Acessando os logs de diagnóstico do Azure Data Lake Storage Gen1
Saiba como habilitar o log de diagnósticos em sua conta do Azure Data Lake Storage Gen1 e como exibir os logs coletados em sua conta.

As organizações podem habilitar o log de diagnóstico para sua conta de Azure Data Lake Storage Gen1 para coletar trilhas de auditoria de acesso a dados que fornecem informações como a lista de usuários que acessam os dados, com que frequência os dados são acessados, a quantidade de dados armazenados na conta, etc. Quando habilitado, o diagnóstico e/ou as solicitações são registrados em uma base de melhor esforço. As entradas de log de Solicitações e Diagnóstico são criadas somente em caso de solicitações feitas no ponto de extremidade de serviço.

## <a name="prerequisites"></a>Pré-requisitos
* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure data Lake Storage Gen1 conta**. Siga as instruções em [Introdução ao Azure Data Lake Storage Gen1 usando o portal do Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Habilitar o log de diagnósticos em sua conta do Data Lake Storage Gen1
1. Faça logon no novo [portal do Azure](https://portal.azure.com).
2. Abra sua conta Data Lake Storage Gen1 e, na folha da conta Data Lake Storage Gen1, clique em **Configurações de Diagnóstico**.
3. Na folha **Configurações de diagnóstico**, clique em **Ativar diagnóstico**.

    ![Captura de tela da conta Data Lake Storage Gen 1 com a opção de configuração de diagnóstico e a opção Ativar diagnóstico chamada out.](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Habilitar logs de diagnóstico")

3. Na folha **Configurações de diagnóstico**, faça as seguintes alterações para configurar o log de diagnósticos.
   
    ![Captura de tela da seção de configuração de diagnóstico com a caixa de texto nome e a opção salvar chamada out.](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Habilitar logs de diagnóstico")
   
   * Para **Nome**, insira um valor para a configuração de log de diagnóstico.
   * Você pode optar por armazenar/processar os dados de maneiras diferentes.
     
        * Selecione a opção **Arquivar em uma conta de armazenamento** para armazenar os logs em uma conta de armazenamento do Azure. Use esta opção se quiser arquivar os dados que serão processados em lote em uma data posterior. Se escolher esta opção, você deverá fornecer uma conta de armazenamento do Azure para salvar os logs.
        
        * Selecione a opção **Transmitir pra um hub de eventos** para transmitir os dados de log para um Hub de Eventos do Azure. Provavelmente, você usará esta opção se tiver um pipeline de processamento de downstream para analisar os logs de entrada em tempo real. Se escolher esta opção, você deverá fornecer os detalhes no Hub de Eventos do Azure que deseja usar.

        * Selecione a opção para **Enviar para log Analytics** usar o serviço Azure monitor para analisar os dados de log gerados. Se você selecionar essa opção, deverá fornecer os detalhes para o espaço de trabalho do Log Analytics que seria utilizado para executar a análise de log. Consulte [Exibir ou analisar dados coletados com Azure monitor logs de pesquisa](../azure-monitor/logs/log-analytics-tutorial.md) para obter detalhes sobre como usar os logs de Azure monitor.
     
   * Especifique se deseja obter os logs de auditoria, os logs de solicitação ou ambos.
   * Especifique o número de dias que os dados devem ser mantidos. Retenção só é aplicável se você estiver usando a conta de armazenamento do Azure para arquivar dados de log.
   * Clique em **Save** (Salvar).

Depois de habilitar as configurações de diagnóstico, você poderá observar os logs na guia **Logs de Diagnóstico** .

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Exibir logs de diagnóstico da sua conta do Data Lake Storage Gen1
Há duas maneiras de exibir os dados do log da sua conta do Data Lake Storage Gen1.

* Na exibição de configurações da conta do Data Lake Storage Gen1
* Na conta de Armazenamento do Azure onde os dados são armazenados

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Usando a exibição de configurações do Data Lake Storage Gen1
1. Na folha **Configurações** da conta Data Lake Storage Gen1, clique em **Logs de Diagnóstico**.
   
    ![Exibir logs de diagnóstico](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Exibir logs de diagnóstico") 
2. Na folha **Logs de Diagnóstico**, você deve ver os logs categorizados por **Logs de Auditoria** e **Logs de Solicitação**.
   
   * Os Logs de Solicitação capturam todas as solicitações de API feitas na conta do Data Lake Storage Gen1.
   * Os Logs de Auditoria são semelhantes aos Logs de Solicitação, mas fornecem uma análise mais detalhada das operações que estão sendo executadas na conta do Data Lake Storage Gen1. Por exemplo, uma única chamada à API de upload nos logs de solicitação poderá resultar em várias operações do tipo "Anexar" nos logs de auditoria.
3. Para baixar os logs, clique no link **Baixar** de cada entrada de log.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Na conta de Armazenamento do Azure que contém dados de log
1. Abra a folha Conta de Armazenamento do Azure associada ao Data Lake Storage Gen1 para registro em log e clique em Blobs. A folha **serviço Blob** lista dois contêineres.
   
    ![Captura de tela da folha Data Lake Storage Gen 1 a opção BLOBs selecionada e a folha serviço de blog com os nomes dos dois serviços blob chamados.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Exibir logs de diagnóstico")
   
   * O contêiner **insights-logs-audit** contém os logs de auditoria.
   * O contêiner **insights-logs-requests** contém os logs de solicitação.
2. Dentro desses contêineres, os logs são armazenados na estrutura a seguir.
   
    ![Captura de tela da estrutura de log conforme armazenada no contêiner.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Exibir logs de diagnóstico")
   
    Por exemplo, o caminho completo para um log de auditoria poderia ser `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    De modo semelhante, o caminho completo para um log de solicitação poderia ser `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Compreenda a estrutura dos dados de log
Os logs de auditoria e solicitação estão em formato JSON. Nesta seção, examinaremos a estrutura do JSON nos logs de solicitação e auditoria.

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
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Requests",
        "operationName": "GETCustomerIngressEgress",
        "resultType": "200",
        "callerIpAddress": "::ffff:1.1.1.1",
        "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
        "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
        "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"StoreIngressSize":0 ,"StoreEgressSize":4096,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z","QueryParameters":"api-version=<version>&op=<operationName>"}
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
| resourceId |String |A ID do recurso em que a operação ocorreu |
| category |String |A categoria do log. Por exemplo, **Solicitações**. |
| operationName |String |Nome da operação que está registrada. Por exemplo, getfilestatus. |
| resultType |String |O status da operação, por exemplo, 200. |
| callerIpAddress |String |O endereço IP do cliente que está fazendo a solicitação |
| correlationId |String |A ID do log que pode ser usada para agrupar um conjunto de entradas de log relacionada |
| identidade |Objeto |A identidade que gerou o log |
| properties |JSON |Confira abaixo para obter os detalhes |

#### <a name="request-log-properties-schema"></a>Esquema de propriedades do log de solicitação
| Nome | Tipo | Descrição |
| --- | --- | --- |
| HttpMethod |String |O método HTTP usado para a operação. Por exemplo, GET. |
| Caminho |String |O caminho em que a operação foi executada |
| RequestContentLength |INT |O comprimento do conteúdo da solicitação HTTP |
| ClientRequestId |String |A ID que identifica esta solicitação exclusivamente |
| StartTime |String |A hora em que o servidor recebeu a solicitação |
| EndTime |String |A hora em que o servidor enviou uma resposta |
| StoreIngressSize |long |Tamanho em bytes de entrada para Data Lake Store |
| StoreEgressSize |long |Tamanho em bytes de saída de Data Lake Store |
| QueryParameters |String |Descrição: esses são os parâmetros de consulta http. Exemplo 1: API-Version = 2014-01-01&op = getfilestatus exemplo 2: op = APPEND&Append = true&syncFlag = DATA&filesessionid = bee3355a-4925-4435-bb4d-ceea52811aeb&leaseid = bee3355a-4925-4435-bb4d-ceea52811aeb&deslocamento = 28313319&API-Version = 2017-08-01 |

### <a name="audit-logs"></a>Logs de auditoria
Aqui está um exemplo de entrada no log de auditoria formatado em JSON. Cada blob tem um objeto raiz chamado **registros** que contém uma matriz de objetos de log

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-08T19:08:59.359Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Audit",
        "operationName": "SeOpenStream",
        "resultType": "0",
        "resultSignature": "0",
        "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
        "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
        "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="audit-log-schema"></a>Esquema do log de auditoria
| Nome | Tipo | Descrição |
| --- | --- | --- |
| time |String |O carimbo de data/hora (em UTC) do log |
| resourceId |String |A ID do recurso em que a operação ocorreu |
| category |String |A categoria do log. Por exemplo, **Auditoria**. |
| operationName |String |Nome da operação que está registrada. Por exemplo, getfilestatus. |
| resultType |String |O status da operação, por exemplo, 200. |
| resultSignature |String |Detalhes adicionais sobre a operação. |
| correlationId |String |A ID do log que pode ser usada para agrupar um conjunto de entradas de log relacionada |
| identidade |Objeto |A identidade que gerou o log |
| properties |JSON |Confira abaixo para obter os detalhes |

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades do log de auditoria
| Nome | Tipo | Descrição |
| --- | --- | --- |
| StreamName |String |O caminho em que a operação foi executada |

## <a name="samples-to-process-the-log-data"></a>Exemplos para processar os dados do log
Ao enviar logs de Azure Data Lake Storage Gen1 para Azure Monitor logs (consulte [Exibir ou analisar dados coletados com Azure monitor logs de pesquisa](../azure-monitor/logs/log-analytics-tutorial.md) para obter detalhes sobre como usar logs do Azure monitor), a consulta a seguir retornará uma tabela que contém uma lista de nomes de exibição do usuário, a hora dos eventos e a contagem de eventos para a hora do evento junto com um gráfico Visual. Podem ser facilmente modificados para mostrar o GUID de usuário ou outros atributos:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


O Azure Data Lake Storage Gen1 fornece um exemplo sobre como processar e analisar os dados de log. Você pode encontrar o exemplo em [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) . 

## <a name="see-also"></a>Veja também
* [Visão Geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
