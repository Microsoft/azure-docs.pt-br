---
title: "Logs de diagnóstico do Azure | Microsoft Docs"
description: "O cliente pode habilitar a análise de log para a CDN do Azure."
services: cdn
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: v-deasim
ms.openlocfilehash: 3e8727e80571be70124fb439f4c7e448f521b692
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostic-logs"></a>Logs de diagnóstico do Azure

Com os logs de diagnóstico do Azure, é possível exibir análises de núcleo e salvá-las em um ou mais destinos, incluindo:

 - Conta de Armazenamento do Azure
 - Hubs de eventos do Azure
 - [Repositório do OMS Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Esse recurso está disponível para todos os pontos de extremidade da CDN que pertencem a perfis CDN Verizon (Standard e Premium) e Akamai (Standard). 

Os logs de diagnóstico do Azure permitem que você exporte métricas de uso básicas do seu ponto de extremidade da CDN para uma variedade de origens para poder consumi-las de forma personalizada. Por exemplo, você pode realizar os seguintes tipos de exportação de dados:

- Exportar os dados para o armazenamento de blobs, exportar para CSV e gerar grafos no Excel.
- Exportar dados para Hubs de Eventos e correlacionar com os dados de outros serviços do Azure.
- Exportar dados para o Log Analytics e exibir dados no seu próprio espaço de trabalho do OMS

A figura a seguir mostra uma exibição de análise de núcleo de CDN típica dos dados.

![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Figura 1 – Exibição de análise de núcleo de CDN*

Para obter mais informações sobre logs de diagnóstico, consulte [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-azure-portal"></a>Habilitar registro em log com o Portal do Azure

Siga estas etapas para habilitar o registro com a análise de núcleo de CDN:

Entre no [Portal do Azure](http://portal.azure.com). Se você ainda não tiver a CDN habilitada para o fluxo de trabalho, [Habilite a CDN do Azure](cdn-create-new-endpoint.md) antes de continuar.

1. No portal, navegue até **Perfil CDN**.
2. Selecione um perfil CDN e, em seguida, o ponto de extremidade da CDN para o qual você deseja habilitar os **Logs de Diagnóstico**.

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Selecione **Logs de diagnóstico** na seção **Monitoramento**.

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Habilitar registro em log com o Armazenamento do Azure
    
1. Para usar o armazenamento do Azure para armazenar os logs, selecione **Arquivar em uma conta de armazenamento**, selecione **CoreAnalytics** e, em seguida, o número de dias de retenção em **Retenção (dias)**. Uma retenção de zero dias armazena os logs indefinidamente. 
2. Digite um nome para a sua configuração e clique em **Conta de armazenamento**. Depois que você selecionou uma conta de armazenamento, clique em **Salvar**.

![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png)

*Figura 2 – Registro em log com o Armazenamento do Azure*

### <a name="logging-with-oms-log-analytics"></a>Registro em log com o OMS Log Analytics

Para usar o OMS Log Analytics para armazenar os logs, siga estas etapas:

1. Na folha **Logs de diagnóstico**, selecione **Enviar para o Log Analytics**. 

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Clique em **Configurar** para configurar o registro da análise do log. Na caixa de diálogo Espaços de trabalho do OMS, é possível selecionar um espaço de trabalho anterior ou criar um novo.

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Clique em **Criar Novo Espaço de Trabalho**.

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/07_Create-new.png)

4. Insira um novo nome do espaço de trabalho do OMS. Um nome de espaço de trabalho do OMS deve ser exclusivo e conter apenas letras, números e hifens; não são permitidos espaços nem sublinhados. 
5. Em seguida, selecione uma assinatura existente, um grupo de recursos (novo ou existente), um local e um tipo de preço. Você também tem a opção de fixar essa configuração em seu painel. Clique em **OK** para concluir a configuração.

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/08_Workspace-resource.png)

5.  Depois que seu espaço de trabalho é criado, você retorna para suas janelas de logs de diagnóstico. Confirme o nome do seu novo espaço de trabalho de análise do log.

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/09_Return-to-logging.png)

    Depois que você configurar a configuração de análise do log, verifique se você selecionou **CoreAnalytics**.

6. Clique em **Salvar**.

7. Para exibir seu novo espaço de trabalho do OMS, vá para o painel do Portal do Azure e clique no nome do seu espaço de trabalho de análise do log. Clique no bloco do Portal do OMS para exibir seu espaço de trabalho no repositório do OMS. 

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    O repositório do OMS está pronto para registrar dados em log. Para consumir esses dados, você deve usar uma [solução do OMS](#consuming-oms-log-analytics-data), abordada posteriormente neste artigo.

Para obter mais informações sobre atrasos em dados de log, consulte [Log data delays](#log-data-delays) (Atrasos nos dados de log).

## <a name="enable-logging-with-powershell"></a>Habilitar o registro em log com o PowerShell

O exemplo a seguir mostra como habilitar os Logs de Diagnóstico por meio dos Cmdlets do Azure PowerShell.

###<a name="enabling-diagnostic-logs-in-a-storage-account"></a>Habilitando os Logs de Diagnóstico em uma conta de armazenamento

Primeiro, faça logon e selecione uma assinatura:

    Login-AzureRmAccount 

    Select-AzureSubscription -SubscriptionId 


Para habilitar os Logs de Diagnóstico em uma Conta de Armazenamento, use este comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```
Para habilitar os logs de diagnóstico em um espaço de trabalho do OMS, use este comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
```



## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Consumir logs de diagnóstico do Armazenamento do Azure
Esta seção descreve o esquema da análise de núcleo da CDN, como ele é organizado dentro de uma conta de armazenamento do Azure e oferece um exemplo de código para baixar os logs em um arquivo CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Usando o Gerenciador de Armazenamento do Microsoft Azure
Antes de poder acessar os dados da análise principal da Conta de Armazenamento do Azure, primeiro você precisa de uma ferramenta para acessar o conteúdo em uma conta de armazenamento. Embora haja várias ferramentas disponíveis no mercado, a que recomendamos é o Gerenciador de Armazenamento do Microsoft Azure. Para baixar a ferramenta, consulte [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/). Depois de baixar e instalar o software, configure-o para usar a mesma conta de armazenamento do Azure que foi configurada como um destino para os Logs de diagnóstico da CDN.

1.  Abra o **Gerenciador de Armazenamento do Microsoft Azure**
2.  Localize a conta de armazenamento
3.  Vá para o nó **"Contêineres de Blob"** nessa conta de armazenamento e expanda o nó
4.  Selecione o contêiner chamado **“insights-logs-coreanalytics”** e clique duas vezes nele
5.  Os resultados são mostrados no painel da direita começando com o primeiro nível, que se parece com **“resourceId=”**. Continue clicando em todo o caminho até encontrar o arquivo **PT1H.json**. Consulte a observação a seguir para obter a explicação do caminho.
6.  Cada blob **PT1H.json** representa os logs de análise de uma hora para um ponto de extremidade da CDN específico ou seu domínio personalizado.
7.  O esquema do conteúdo desse arquivo JSON é descrito na seção Esquema dos logs de análise de núcleo


> [!NOTE]
> **Formato de caminho de blob**
> 
> Os logs de análise de núcleo são gerados a cada hora e os dados são coletados e armazenados dentro de um único blob do Azure como um payload JSON. Como a ferramenta do Gerenciador de armazenamento interpreta '/' como um separador de diretório e mostra a hierarquia, o caminho para o blob do Azure é exibido como se houvesse uma estrutura hierárquica e representa o nome do blob. O nome do blob segue a convenção de nomenclatura abaixo: 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Descrição dos campos:**

|value|Descrição|
|-------|---------|
|ID da assinatura    |A ID da assinatura do Azure no formato Guid.|
|Recurso |Nome do Grupo   Nome do grupo de recursos ao qual os recursos da CDN pertencem.|
|Nome do Perfil |Nome do perfil CDN|
|Nome do Ponto de Extremidade |Nome do ponto de extremidade da CDN|
|Ano|  Representação de 4 dígitos do ano, por exemplo, 2017|
|Mês| Representação de 2 dígitos do número do mês. 01 = janeiro... 12 = dezembro|
|Dia|   Representação de dois dígitos do dia do mês|
|PT1H.json| Arquivo JSON real em que os dados da análise são armazenados|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportando os dados de análise de núcleo para um arquivo CSV

Para facilitar o acesso à análise de núcleo, o exemplo de código de uma ferramenta é fornecido. Esta ferramenta permite baixar os arquivos JSON em um formato de arquivo simples separado por vírgula, que pode ser usado para criar facilmente gráficos ou outras agregações.

Aqui está como você pode usar a ferramenta:

1.  Visite o link do GitHub: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.  Baixe o código.
3.  Siga as instruções a serem compiladas e configuradas.
4.  Execute a ferramenta.
5.  O arquivo CSV resultante mostra os dados de análise em uma hierarquia simples.

## <a name="consuming-diagnostics-logs-from-an-oms-log-analytics-repository"></a>Consumir logs de diagnóstico de um repositório do OMS Log Analytics
O Log Analytics é um serviço no Operations Management Suite (OMS) que monitora seus ambientes na nuvem e locais a fim de manter a disponibilidade e o desempenho. Ele coleta dados gerados pelos recursos em seus ambientes de nuvem e locais e de outras ferramentas de monitoramento para fornecer análise de várias fontes. 

Para usar o Log Analytics, você deve [habilitar o registro em log](#enable-logging-with-azure-storage) para o repositório do OMS Log Analytics do Azure, que é discutido anteriormente neste artigo.

### <a name="using-the-oms-repository"></a>Usando o repositório do OMS

 O diagrama a seguir mostra a arquitetura das entradas e saídas do repositório:

![Repositório do OMS Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Figura 3 – Repositório do Log Analytics*

Você pode exibir os dados de várias maneiras usando soluções de gerenciamento. Você pode obter as soluções de gerenciamento do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Você pode instalar as soluções de gerenciamento do Azure marketplace clicando no link **Obtenha agora** na parte inferior de cada solução.

### <a name="adding-an-oms-cdn-management-solution"></a>Adicionar uma solução de gerenciamento de CDN do OMS

Siga estas etapas para adicionar uma solução de gerenciamento:

1.   Se ainda não tiver feito isso, entre no Portal do Azure usando a sua assinatura do Azure e vá para o seu painel.
    ![Painel do Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Na folha **Novo** em **Marketplace**, selecione **Monitoramento + gerenciamento**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Na folha **Monitoramento + gerenciamento**, clique em **Ver todos**.

    ![Ver tudo](./media/cdn-diagnostics-log/15_See-all.png)

4.  Pesquise pela CDN na caixa de pesquisa.

    ![Ver tudo](./media/cdn-diagnostics-log/16_Search-for.png)

5.  Selecione **Análise de Núcleo de CDN do Azure**. 

    ![Ver tudo](./media/cdn-diagnostics-log/17_Core-analytics.png)

6.  Depois de clicar em **Criar**, você deverá criar um novo espaço de trabalho do OMS ou usar um existente. 

    ![Ver tudo](./media/cdn-diagnostics-log/18_Adding-solution.png)

7.  Selecione o espaço de trabalho que você criou antes. Em seguida, você precisa adicionar uma conta de automação.

    ![Ver tudo](./media/cdn-diagnostics-log/19_Add-automation.png)

8. A tela a seguir mostra o formulário de conta de automação que você deve preencher. 

    ![Ver tudo](./media/cdn-diagnostics-log/20_Automation.png)

9. Depois que você criar a conta de automação, você estará pronto para adicionar sua solução. Selecione o botão **Criar** .

    ![Ver tudo](./media/cdn-diagnostics-log/21_Ready.png)

10. A solução foi agora adicionada ao espaço de trabalho. Retorne para seu painel do Portal do Azure.

    ![Ver tudo](./media/cdn-diagnostics-log/22_Dashboard.png)

    Clique no espaço de trabalho do Log Analytics que você criou para ir para o seu espaço de trabalho. 

11. Clique no bloco **Portal do OMS** para ver a nova solução no portal do OMS.

    ![Ver tudo](./media/cdn-diagnostics-log/23_workspace.png)

12. O portal do OMS deve agora ser semelhante à tela a seguir:

    ![Ver tudo](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Clique em um dos blocos para ver várias exibições nos dados.

    ![Ver tudo](./media/cdn-diagnostics-log/25_Interior-view.png)

    Você pode rolar à esquerda ou direita para ver mais blocos representando exibições individuais nos dados. 

    Clicar em um dos blocos lhe fornece mais detalhes sobre seus dados.

     ![Ver tudo](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Ofertas e tipos de preços

Você pode ver ofertas e camadas de preços para soluções de gerenciamento do OMS e ofertas [aqui](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Personalizando exibições

Você pode personalizar a exibição em seus dados usando o **Designer de Exibição**. Para começar a criar, acesse seu espaço de trabalho do OMS e clique no bloco **Designer de Exibição**.

![Criador de Modos de Exibição](./media/cdn-diagnostics-log/27_Designer.png)

É possível arrastar e soltar os tipos de gráficos e preencher os detalhes de dados que você deseja analisar.

![Criador de Modos de Exibição](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Atrasos em dados de log

Atrasos em dados de log da Verizon | Atrasos em dados de log da Akamai
--- | ---
Os dados de log de Verizon estão 1 hora atrasados e demoram até 2 horas para começar a aparecer após a conclusão da propagação do ponto de extremidade. | Os dados de log do Akamai estão atrasados em 24 horas; se foram criados há mais de 24 horas, leva até 2 horas para eles começarem a ser exibidos. Se eles tiverem sido criados recentemente, poderá demorar até 25 horas para os logs começarem a aparecer.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Tipos de log de diagnóstico para análise de núcleo de CDN

No momento, oferecemos somente logs de análise de núcleo, que contêm métricas que mostram estatísticas de resposta HTTP e estatísticas de saída como visto nos POPs/bordas da CDN.

### <a name="core-analytics-metrics-details"></a>Detalhes das métricas da análise de núcleo
A tabela a seguir mostra uma lista de métricas disponíveis nos logs de análise de núcleo. Nem todas as métricas estão disponíveis de todos os provedores, embora essas diferenças sejam mínimas. A tabela a seguir também mostra se uma determinada métrica está disponível de um provedor. Observe que as métricas estão disponíveis apenas para os pontos de extremidade da CDN que têm tráfego neles.


|Métrica                     | Descrição   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |Número total de ocorrências de solicitação durante esse período| Sim  |Sim   |
| RequestCountHttpStatus2xx |Contagem de todas as solicitações que resultaram em um código HTTP 2xx (por exemplo, 200, 202)              | Sim  |Sim   |
| RequestCountHttpStatus3xx | Contagem de todas as solicitações que resultaram em um código HTTP 3xx (por exemplo, 300, 302)              | Sim  |Sim   |
| RequestCountHttpStatus4xx |Contagem de todas as solicitações que resultaram em um código HTTP 4xx (por exemplo, 400, 404)               | Sim   |Sim   |
| RequestCountHttpStatus5xx | Contagem de todas as solicitações que resultaram em um código HTTP 5xx (por exemplo, 500, 504)              | Sim  |Sim   |
| RequestCountHttpStatusOthers |  Contagem de todos os outros códigos HTTP (fora de 2xx a 5xx) | Sim  |Sim   |
| RequestCountHttpStatus200 | Contagem de todas as solicitações que resultaram em um código de resposta HTTP 200              |Não   |Sim   |
| RequestCountHttpStatus206 | Contagem de todas as solicitações que resultaram em um código de resposta HTTP 206              |Não   |Sim   |
| RequestCountHttpStatus302 | Contagem de todas as solicitações que resultaram em um código de resposta HTTP 302              |Não   |Sim   |
| RequestCountHttpStatus304 |  Contagem de todas as solicitações que resultaram em um código de resposta HTTP 304             |Não   |Sim   |
| RequestCountHttpStatus404 | Contagem de todas as solicitações que resultaram em um código de resposta HTTP 404              |Não   |Sim   |
| RequestCountCacheHit |Contagem de todas as solicitações que resultaram em uma Ocorrência no Cache. O ativo foi servido diretamente do POP para o cliente.               | Sim  |Não   |
| RequestCountCacheMiss | Contagem de todas as solicitações que resultaram em uma Perda do Cache. Isso significa que o ativo não foi encontrado no POP mais próximo ao cliente e, portanto, foi recuperado da Origem.              |Sim   | Não  |
| RequestCountCacheNoCache | Contagem de todas as solicitações para um ativo que são impedidas de serem armazenadas em cache devido a uma configuração do usuário na borda.              |Sim   | Não  |
| RequestCountCacheUncacheable | Contagem de todas as solicitações para ativos que são impedidas de serem armazenadas em cache pelos cabeçalhos Cache-Control e Expires do ativo, que indicam que não devem ser armazenadas em cache em um POP ou pelo cliente HTTP                |Sim   |Não   |
| RequestCountCacheOthers | Contagem de todas as solicitações com o status de cache não cobertas pelos itens acima.              |Sim   | Não  |
| EgressTotal | Transferência de dados de saída em GB              |Sim   |Sim   |
| EgressHttpStatus2xx | Transferência de dados de saída* para respostas com códigos de status HTTP 2xx em GB            |Sim   |Não   |
| EgressHttpStatus3xx | Transferência de dados de saída para respostas com códigos de status HTTP 3xx em GB              |Sim   |Não   |
| EgressHttpStatus4xx | Transferência de dados de saída para respostas com códigos de status HTTP 4xx em GB               |Sim   | Não  |
| EgressHttpStatus5xx | Transferência de dados de saída para respostas com códigos de status HTTP 5xx em GB               |Sim   |  Não |
| EgressHttpStatusOthers | Transferência de dados de saída para respostas com outros códigos de status HTTP em GB                |Sim   |Não   |
| EgressCacheHit |  Transferência de dados de saída para respostas que foram entregues diretamente do cache da CDN nos POPs/Bordas da CDN  |Sim   |  Não |
| EgressCacheMiss | Transferência de dados de saída de respostas que não foram encontradas no servidor POP mais próximo e foram recuperadas do servidor de origem              |Sim   |  Não |
| EgressCacheNoCache | Transferência de dados de saída para ativos que são impedidos de serem armazenados em cache devido a uma configuração do usuário na borda.                |Sim   |Não   |
| EgressCacheUncacheable | Transferência de dados de saída para ativos impedidos de serem armazenados em cache pelos cabeçalhos Cache-Control e/ou Expires do ativo. Indica que não deve ser armazenado em cache em um POP ou pelo cliente HTTP.                   |Sim   | Não  |
| EgressCacheOthers |  Transferências de dados de saída para outros cenários de cache.             |Sim   | Não  |

*Transferência de dados de saída refere-se ao tráfego entregue de servidores POP da CDN para o cliente.


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

Em que ‘time’ representa a hora de início do limite da hora pela qual as estatísticas são relatadas. Quando uma métrica não é compatível com um provedor da CDN, em vez de um valor duplo ou inteiro, há um valor nulo. Esse valor nulo indica a ausência de uma métrica e isso é diferente de um valor de 0. Há um conjunto dessas métricas por domínio configurado no ponto de extremidade.

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

* [Logs de Diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Análise principal por meio do portal suplementar da CDN do Azure](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Log Analytics do OMS do Azure](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-overview)
* [API REST do Log Analytics do Azure](https://docs.microsoft.com/en-us/rest/api/loganalytics)







