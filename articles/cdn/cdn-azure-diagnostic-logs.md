---
title: Logs de diagnóstico
titleSuffix: Azure Content Delivery Network
description: Saiba como usar os logs de diagnóstico do Azure para salvar a análise principal, que permite exportar métricas de uso de seu ponto de extremidade de rede de distribuição de conteúdo do Azure.
services: cdn
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: allensu
ms.openlocfilehash: 96e80de5b8b5ab0a046913ce40ca2d7254dd0133
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573222"
---
# <a name="diagnostic-logs---azure-content-delivery-network"></a>Logs de diagnóstico-rede de distribuição de conteúdo do Azure

Com os logs de diagnóstico do Azure, é possível exibir análises de núcleo e salvá-las em um ou mais destinos, incluindo:

* Conta de Armazenamento do Azure
* Espaço de trabalho do Log Analytics
* Hubs de eventos do Azure

Este recurso está disponível em pontos de extremidade CDN para todos os tipos de preço. 

Os logs de diagnóstico permitem exportar métricas de uso básicas do ponto de extremidade da CDN para diferentes tipos de fontes para que você possa consumi-las de forma personalizada. Você pode fazer os seguintes tipos de exportação de dados:

* Exportar os dados para o armazenamento de blobs, exportar para CSV e gerar grafos no Excel.
* Exportar dados para Hubs de Eventos e correlacionar com os dados de outros serviços do Azure.
* Exportar dados para Azure Monitor logs e exibir dados em seu próprio espaço de trabalho Log Analytics

Um perfil da CDN do Azure é necessário para as etapas a seguir. Consulte [criar um perfil e um ponto de extremidade da CDN do Azure](cdn-create-new-endpoint.md) antes de continuar.

## <a name="enable-logging-with-the-azure-portal"></a>Ativar o registro em log com o portal do Azure

Siga estas etapas habilitar o registro em log para o ponto de extremidade da CDN do Azure:

1. Entre no [portal do Azure](https://portal.azure.com). 

2. Na portal do Azure, navegue até **todos os recursos**  ->  **de seu perfil de CDN**

2. Selecione o ponto de extremidade da CDN para o qual você deseja habilitar os logs de diagnóstico:

    :::image type="content" source="./media/cdn-diagnostics-log/02_browse-to-diagnostics-logs.png" alt-text="Selecione ponto de extremidade CDN." border="true":::

3. Selecione **logs de diagnóstico** na seção **monitoramento** :

    :::image type="content" source="./media/cdn-diagnostics-log/03_diagnostics-logs-options.png" alt-text="Selecione logs de diagnóstico." border="true":::

### <a name="enable-logging-with-azure-storage"></a>Habilitar registro em log com o Armazenamento do Azure

Para usar uma conta de armazenamento para armazenar os logs, siga estas etapas:

 >[!NOTE] 
 >Uma conta de armazenamento é necessária para concluir estas etapas. Consulte: **[criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)** para obter mais informações.
    
1. Para **nome da configuração de diagnóstico**, insira um nome para as configurações de log de diagnóstico.
 
2. Selecione **arquivo para uma conta de armazenamento**, em seguida, selecione **CoreAnalytics**. 

3. Para **retenção (dias)**, escolha o número de dias de retenção. Uma retenção de zero dias armazena os logs indefinidamente. 

4. Selecione a assinatura e a conta de armazenamento para os logs.

    :::image type="content" source="./media/cdn-diagnostics-log/04_diagnostics-logs-storage.png" alt-text="Logs de diagnóstico-armazenamento." border="true":::

3. Selecione **Salvar**.

### <a name="send-to-log-analytics"></a>Enviar para o Log Analytics

Para usar Log Analytics para os logs, siga estas etapas:

>[!NOTE] 
>Um espaço de trabalho do log Analytics é necessário para concluir estas etapas. Consulte: **[criar um log Analytics espaço de trabalho no portal do Azure](../azure-monitor/logs/quick-create-workspace.md)** para obter mais informações.
    
1. Para **nome da configuração de diagnóstico**, insira um nome para as configurações de log de diagnóstico.

2. Selecione **Enviar para log Analytics** e, em seguida, selecione **CoreAnalytics**. 

3. Selecione a assinatura e Log Analytics espaço de trabalho para os logs.

   :::image type="content" source="./media/cdn-diagnostics-log/05-la-workspace.png" alt-text="Logs de diagnóstico-Log Analytics." border="true":::

4. Selecione **Salvar**.

### <a name="stream-to-an-event-hub"></a>Transmitir para um hub de eventos

Para usar um hub de eventos para os logs, siga estas etapas:

>[!NOTE] 
>Um hub de eventos é necessário para concluir estas etapas. Consulte: **[início rápido: criar um hub de eventos usando portal do Azure](../event-hubs/event-hubs-create.md)** para obter mais informações.
    
1. Para **nome da configuração de diagnóstico**, insira um nome para as configurações de log de diagnóstico.

2. Selecione **fluxo para um hub de eventos** e, em seguida, selecione **CoreAnalytics**. 

3. Selecione a assinatura e o namespace do hub de eventos para os logs.

   :::image type="content" source="./media/cdn-diagnostics-log/06-eventhub-namespace.png" alt-text="Logs de diagnóstico-Hub de eventos." border="true":::

4. Selecione **Salvar**.


## <a name="enable-logging-with-powershell"></a>Habilitar o registro em log com o PowerShell

O exemplo a seguir mostra como habilitar os Logs de Diagnóstico por meio dos Cmdlets do Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Habilitar os logs de diagnóstico em uma conta de armazenamento

1. Entre no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Para habilitar os logs de diagnóstico em uma conta de armazenamento, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Categories CoreAnalytics
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Habilitar logs de diagnóstico para Log Analytics espaço de trabalho

1. Entre no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Para habilitar os logs de diagnóstico para um espaço de trabalho Log Analytics, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Categories CoreAnalytics
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Habilitar logs de diagnóstico para o namespace do hub de eventos

1. Entre no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Para habilitar os logs de diagnóstico para um espaço de trabalho Log Analytics, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $eventhubname = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhubname -Enabled $true -Categories CoreAnalytics
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Consumir logs de diagnóstico do Armazenamento do Azure
Esta seção descreve o esquema da análise de núcleo da CDN, a organização em uma conta de armazenamento do Azure e fornece um código de exemplo para baixar os logs em um arquivo CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Usar o Gerenciador de Armazenamento do Microsoft Azure
Para baixar a ferramenta, consulte [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/). Depois de baixar e instalar o software, configure-o para usar a mesma conta de armazenamento do Azure que foi configurada como um destino para os Logs de diagnóstico da CDN.

1.  Abrir **Gerenciador de armazenamento do Microsoft Azure**
2.  Localize a conta de armazenamento
3.  Expanda o nó **Blob Containers** sob esta conta de armazenamento.
4.  Selecione o contêiner denominado *insights-logs-coreanalytics*.
5.  Os resultados aparecem no painel direito, começando com o primeiro nível, como *resourceId =*. Continue selecionando cada nível até encontrar o arquivo *PT1H.json*. Para obter uma explicação do caminho, confira [Formato de caminho de blob](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Cada arquivo blob *PT1H.json* representa os logs de análise por uma hora para um ponto de extremidade CDN específico ou seu domínio personalizado.
7.  O esquema do conteúdo desse arquivo JSON é descrito na seção Esquema dos logs de análise de núcleo.


#### <a name="blob-path-format"></a>Formato de caminho de blob

Os logs de análise de núcleo são gerados a cada hora e os dados são coletados e armazenados dentro de um único blob do Azure como um payload JSON. A ferramenta Gerenciador de armazenamento interpreta '/' como um separador de diretório e mostra a hierarquia. O caminho para o blob do Azure é exibido como se houver uma estrutura hierárquica e representa o nome do blob. O nome do blob segue a convenção de nomenclatura abaixo:    

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Descrição dos campos:**

|Valor|Descrição|
|-------|---------|
|ID da assinatura    |A ID da assinatura do Azure no formato Guid.|
|Nome do Grupo de Recursos |Nome do grupo de recursos ao qual os recursos da CDN pertencem.|
|Nome do perfil |Nome do perfil CDN|
|Nome do Ponto de Extremidade |Nome do ponto de extremidade da CDN|
|Year|  Representação de quatro dígitos do ano, por exemplo, 2017|
|Mês| Representação de dois dígitos do número do mês. 01 = Janeiro... 12 = dezembro|
|Dia|   Representação de dois dígitos do dia do mês|
|PT1H.json| Arquivo JSON real em que os dados da análise são armazenados|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportando os dados de análise de núcleo para um arquivo CSV

Para acessar o Core Analytics, é fornecido um código de exemplo para uma ferramenta. Esta ferramenta permite fazer o download do arquivos JSON em um formato de arquivo simples separado por vírgula, que pode ser usado para criar gráficos ou outras agregações.

Aqui está como você pode usar a ferramenta:

1.  Visite o link do GitHub: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Baixe o código.
3.  Siga as instruções a serem compiladas e configuradas.
4.  Execute a ferramenta.
5.  O arquivo CSV resultante mostra os dados de análise em uma hierarquia simples.

## <a name="log-data-delays"></a>Atrasos em dados de log

A tabela a seguir mostra atrasos em dados de log para **CDN Standard do Azure da Microsoft**, **CDN Standard do Azure da Akamai** e da **CDN Standard/Premium do Azure da Verizon**.

Atrasos em dados de log da Microsoft | Atrasos em dados de log da Verizon | Atrasos em dados de log da Akamai
--- | --- | ---
Atrasado em 1 hora. | Atrasado em 1 hora e pode demorar até 2 horas para começar a aparecer após a conclusão da propagação do ponto de extremidade. | Atrasado em 24 horas; se foram criados há mais de 24 horas, leva até 2 horas para eles começarem a ser exibidos. Se eles tiverem sido criados recentemente, poderá demorar até 25 horas para os logs começarem a aparecer.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Tipos de log de diagnóstico para análise de núcleo de CDN

Atualmente, a Microsoft oferece somente logs analíticos principais, que contêm métricas que mostram estatísticas de respostas HTTP e estatísticas de saída, conforme vistas dos POPs / arestas CDN.

### <a name="core-analytics-metrics-details"></a>Detalhes das métricas da análise de núcleo
A tabela a seguir mostra uma lista de métricas disponíveis nos logs de análise de núcleo para:

* **CDN Standard do Azure da Microsoft**
* **CDN Standard do Azure da Akamai**
* **CDN Standard/Premium do Azure da Verizon**

Nem todas as métricas estão disponíveis de todos os provedores, embora essas diferenças sejam mínimas. A tabela também mostra se uma determinada métrica está disponível de um provedor. As métricas estão disponíveis somente para os terminais CDN que possuem tráfego neles.


|Métrica                     | Descrição | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Número total de ocorrências de solicitação durante esse período. | Sim | Sim |Sim |
| RequestCountHttpStatus2xx | Contagem de todas as solicitações que resultaram em um código HTTP 2xx (por exemplo, 200, 202). | Sim | Sim |Sim |
| RequestCountHttpStatus3xx | Contagem de todas as solicitações que resultaram em um código HTTP 3xx (por exemplo, 300, 302). | Sim | Sim |Sim |
| RequestCountHttpStatus4xx | Contagem de todas as solicitações que resultaram em um código HTTP 4xx (por exemplo, 400, 404). | Sim | Sim |Sim |
| RequestCountHttpStatus5xx | Contagem de todas as solicitações que resultaram em um código HTTP 5xx (por exemplo, 500, 504). | Sim | Sim |Sim |
| RequestCountHttpStatusOthers | Contagem de todos os outros códigos HTTP (fora de 2xx a 5xx). | Sim | Sim |Sim |
| RequestCountHttpStatus200 | Contagem de todas as solicitações que resultaram em um código de resposta HTTP 200. | Sim | Não  |Sim |
| RequestCountHttpStatus206 | Contagem de todas as solicitações que resultaram em um código de resposta HTTP 206. | Sim | Não  |Sim |
| RequestCountHttpStatus302 | Contagem de todas as solicitações que resultaram em um código de resposta HTTP 302. | Sim | Não  |Sim |
| RequestCountHttpStatus304 | Contagem de todas as solicitações que resultaram em um código de resposta HTTP 304. | Sim | Não  |Sim |
| RequestCountHttpStatus404 | Contagem de todas as solicitações que resultaram em um código de resposta HTTP 404. | Sim | Não  |Sim |
| RequestCountCacheHit | Contagem de todas as solicitações que resultaram em um hit do Cache. O ativo foi servido diretamente do POP para o cliente. | Sim | Sim | Não  |
| RequestCountCacheMiss | Contagem de todas as solicitações que resultaram em uma Perda do Cache. Um erro de cache significa que o ativo não foi encontrado no POP mais próximo ao cliente e foi recuperado da origem. | Sim | Sim | Não |
| RequestCountCacheNoCache | Contagem de todas as solicitações para um ativo que são impedidas de serem armazenadas em cache devido a uma configuração de usuário na borda. | Sim | Sim | Não |
| RequestCountCacheUncacheable | Contagem de todas as solicitações para ativos que são impedidas de serem armazenadas em cache pelos cabeçalhos Cache-Control e Expires do ativo. Essa contagem indica que ela não deve ser armazenada em cache em um POP ou pelo cliente HTTP. | Sim | Sim | Não |
| RequestCountCacheOthers | Contagem de todas as solicitações com o status de cache não cobertas pelos itens acima. | Não | Sim | Não  |
| EgressTotal | Transferência de dados de saída em GB | Sim |Sim |Sim |
| EgressHttpStatus2xx | Transferência de dados de saída* para respostas com códigos de status HTTP 2xx em GB. | Sim | Sim | Não  |
| EgressHttpStatus3xx | Transferência de dados de saída para respostas com códigos de status HTTP 3xx em GB. | Sim | Sim | Não  |
| EgressHttpStatus4xx | Transferência de dados de saída para respostas com códigos de status HTTP 4xx em GB. | Sim | Sim | Não  |
| EgressHttpStatus5xx | Transferência de dados de saída para respostas com códigos de status HTTP 5xx em GB. | Sim | Sim | Não |
| EgressHttpStatusOthers | Transferência de dados de saída para respostas com outros códigos de status HTTP em GB. | Sim | Sim | Não  |
| EgressCacheHit | Transferência de dados de saída para respostas que foram entregues diretamente do cache da CDN nos POPs/Bordas da CDN. | Sim | Sim | Não |
| EgressCacheMiss. | Transferência de dados de saída para respostas que não foram encontradas no servidor POP mais próximo e recuperadas do servidor de origem. | Sim | Sim | Não |
| EgressCacheNoCache | Transferência de dados de saída para ativos que são impedidos de serem armazenados em cache devido a uma configuração de usuário na borda. | Sim | Sim | Não |
| EgressCacheUncacheable | Transferência de dados de saída para ativos que são impedidos de serem armazenados em cache pelo Cache-Control do ativo e, ou expiram cabeçalhos. Indica que ele não deve ser armazenado em cache em um POP ou pelo cliente HTTP. | Sim | Sim | Não |
| EgressCacheOthers | Transferências de dados de saída para outros cenários de cache. | Não | Sim | Não |

* Transferência de dados de saída refere-se ao tráfego entregue de servidores POP da CDN para o cliente.


### <a name="schema-of-the-core-analytics-logs"></a>Esquema dos logs de análise de núcleo 

Todos os logs são armazenados em formato JSON e cada entrada tem campos de cadeia de caracteres de acordo com o esquema a seguir:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Em que *time* representa a hora de início do limite da hora pela qual as estatísticas são relatadas. Uma métrica sem suporte por um provedor de CDN, em vez de um valor Double ou Integer, resulta em um valor nulo. Esse valor nulo indica a ausência de uma métrica e isso é diferente de um valor de 0. Um conjunto dessas métricas por domínio é configurado no ponto de extremidade.

Propriedades de exemplo:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Recursos adicionais

* [Logs de diagnóstico do Azure](../azure-monitor/essentials/platform-logs-overview.md)
* [Análise principal por meio do portal suplementar da CDN do Azure](./cdn-analyze-usage-patterns.md)
* [Logs do Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [API REST do Log Analytics do Azure](/rest/api/loganalytics)