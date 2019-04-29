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
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: d200f72b3c0e5634c3dca8f60a4754a14351110a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878662"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Acessando os logs de diagnóstico do Azure Data Lake Storage Gen1
Saiba como habilitar o log de diagnósticos em sua conta do Azure Data Lake Storage Gen1 e como exibir os logs coletados em sua conta.

As organizações podem habilitar o log de diagnóstico para que suas contas do Azure Data Lake Storage Gen1 coletem trilhas de auditoria de acesso a dados que forneçam informações, como a lista de usuários que acessam os dados, a frequência que os dados são acessados, a quantidade de dados armazenados na conta, etc. Quando habilitado, o diagnóstico e/ou as solicitações são registradas em uma base de melhor esforço. As entradas de log de Solicitações e Diagnóstico são criadas somente em caso de solicitações feitas no ponto de extremidade de serviço.

## <a name="prerequisites"></a>Pré-requisitos
* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Conta do Azure Data Lake Storage Gen1**. Siga as instruções em [Introdução ao Azure Data Lake Storage Gen1 usando o Portal do Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Habilitar o log de diagnósticos em sua conta do Data Lake Storage Gen1
1. Inscreva-se no novo [portal do Azure](https://portal.azure.com).
2. Abra sua conta Data Lake Storage Gen1 e, na folha da conta Data Lake Storage Gen1, clique em **Configurações de Diagnóstico**.
3. Na folha **Configurações de diagnóstico**, clique em **Ativar diagnóstico**.

    ![Habilitar o log de diagnóstico](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "habilitar logs de diagnóstico")

3. Na folha **Configurações de diagnóstico**, faça as seguintes alterações para configurar o log de diagnósticos.
   
    ![Habilitar o log de diagnóstico](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "habilitar logs de diagnóstico")
   
   * Para **Nome**, insira um valor para a configuração de log de diagnóstico.
   * Você pode optar por armazenar/processar os dados de maneiras diferentes.
     
        * Selecione a opção **Arquivar em uma conta de armazenamento** para armazenar os logs em uma conta de armazenamento do Azure. Use esta opção se quiser arquivar os dados que serão processados em lote em uma data posterior. Se escolher esta opção, você deverá fornecer uma conta de armazenamento do Azure para salvar os logs.
        
        * Selecione a opção **Transmitir pra um hub de eventos** para transmitir os dados de log para um Hub de Eventos do Azure. Provavelmente, você usará esta opção se tiver um pipeline de processamento de downstream para analisar os logs de entrada em tempo real. Se escolher esta opção, você deverá fornecer os detalhes no Hub de Eventos do Azure que deseja usar.

        * Selecione a opção para **enviar para Log Analytics** para usar o serviço do Azure Monitor para analisar os dados de log gerado. Se você selecionar essa opção, deverá fornecer os detalhes para o espaço de trabalho do Log Analytics que seria utilizado para executar a análise de log. Ver [exibir ou analisar os dados coletados com a pesquisa de logs do Azure Monitor](../azure-monitor/learn/tutorial-viewdata.md) para obter detalhes sobre como usar o Azure Monitor registra em log.
     
   * Especifique se deseja obter os logs de auditoria, os logs de solicitação ou ambos.
   * Especifique o número de dias que os dados devem ser mantidos. Retenção só é aplicável se você estiver usando a conta de armazenamento do Azure para arquivar dados de log.
   * Clique em **Salvar**.

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
   
    ![Exibir logs de diagnóstico](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Exibir logs de diagnóstico")
   
   * O contêiner **insights-logs-audit** contém os logs de auditoria.
   * O contêiner **insights-logs-requests** contém os logs de solicitação.
2. Dentro desses contêineres, os logs são armazenados na estrutura a seguir.
   
    ![Exibir logs de diagnóstico](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Exibir logs de diagnóstico")
   
    Por exemplo, o caminho completo para um log de auditoria poderia ser `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    De modo semelhante, o caminho completo para um log de solicitação poderia ser `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Compreenda a estrutura dos dados de log
Os logs de auditoria e solicitação estão em formato JSON. Nesta seção, examinaremos a estrutura do JSON nos logs de solicitação e auditoria.

### <a name="request-logs"></a>Logs de Solicitação
Aqui está um exemplo de entrada no log de solicitação formatado em JSON. Cada blob tem um objeto-raiz chamado **registros** que contém uma matriz de objetos do log.

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
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Esquema do log de solicitação
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| tempo real |Cadeia de caracteres |O carimbo de data/hora (em UTC) do log |
| ResourceId |Cadeia de caracteres |A ID do recurso em que a operação ocorreu |
| categoria |Cadeia de caracteres |A categoria do log. Por exemplo, **Solicitações**. |
| operationName |Cadeia de caracteres |Nome da operação que está registrada. Por exemplo, getfilestatus. |
| resultType |Cadeia de caracteres |O status da operação, por exemplo, 200. |
| callerIpAddress |Cadeia de caracteres |O endereço IP do cliente que está fazendo a solicitação |
| correlationId |Cadeia de caracteres |A ID do log que pode ser usada para agrupar um conjunto de entradas de log relacionada |
| identidade |Objeto |A identidade que gerou o log |
| propriedades |JSON |Confira abaixo para obter os detalhes |

#### <a name="request-log-properties-schema"></a>Esquema de propriedades do log de solicitação
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| HttpMethod |Cadeia de caracteres |O método HTTP usado para a operação. Por exemplo, GET. |
| Caminho |Cadeia de caracteres |O caminho em que a operação foi executada |
| RequestContentLength |int |O comprimento do conteúdo da solicitação HTTP |
| ClientRequestId |Cadeia de caracteres |A ID que identifica esta solicitação exclusivamente |
| StartTime |Cadeia de caracteres |A hora em que o servidor recebeu a solicitação |
| EndTime |Cadeia de caracteres |A hora em que o servidor enviou uma resposta |

### <a name="audit-logs"></a>Logs de auditoria
Aqui está um exemplo de entrada no log de auditoria formatado em JSON. Cada blob tem um objeto-raiz chamado **registros** que contém uma matriz de objetos do log

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

#### <a name="audit-log-schema"></a>Esquema do log de auditoria
| NOME | Type | DESCRIÇÃO |
| --- | --- | --- |
| tempo real |Cadeia de caracteres |O carimbo de data/hora (em UTC) do log |
| ResourceId |Cadeia de caracteres |A ID do recurso em que a operação ocorreu |
| categoria |Cadeia de caracteres |A categoria do log. Por exemplo, **Auditoria**. |
| operationName |Cadeia de caracteres |Nome da operação que está registrada. Por exemplo, getfilestatus. |
| resultType |Cadeia de caracteres |O status da operação, por exemplo, 200. |
| resultSignature |Cadeia de caracteres |Detalhes adicionais sobre a operação. |
| correlationId |Cadeia de caracteres |A ID do log que pode ser usada para agrupar um conjunto de entradas de log relacionada |
| identidade |Objeto |A identidade que gerou o log |
| propriedades |JSON |Confira abaixo para obter os detalhes |

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades do log de auditoria
| Name | Type | DESCRIÇÃO |
| --- | --- | --- |
| StreamName |Cadeia de caracteres |O caminho em que a operação foi executada |

## <a name="samples-to-process-the-log-data"></a>Exemplos para processar os dados do log
Quando o envio de logs do Azure Data Lake armazenamento Gen1 aos logs do Azure Monitor (consulte [exibir ou analisar os dados coletados com a pesquisa de logs do Azure Monitor](../azure-monitor/learn/tutorial-viewdata.md) para obter detalhes sobre como usar o Azure Monitor registra em log), a consulta a seguir retornará uma tabela que contém um lista de usuário exibir nomes, o tempo dos eventos e a contagem de eventos para a hora do evento, juntamente com um gráfico visual. Podem ser facilmente modificados para mostrar o GUID de usuário ou outros atributos:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


O Azure Data Lake Storage Gen1 fornece um exemplo sobre como processar e analisar os dados de log. Você pode encontrar a política de exemplo em [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Consulte também
* [Visão Geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)

