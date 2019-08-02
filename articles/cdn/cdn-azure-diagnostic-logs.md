---
title: Logs de diagnóstico do Azure | Microsoft Docs
description: O cliente pode habilitar a análise de log para a CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: magattus
ms.openlocfilehash: 86696ed6715b4e43a9d02232c013eb64feb61f67
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594200"
---
# <a name="azure-diagnostic-logs"></a>Logs de diagnóstico do Azure

Com os logs de diagnóstico do Azure, é possível exibir análises de núcleo e salvá-las em um ou mais destinos, incluindo:

 - Conta de Armazenamento do Azure
 - Hubs de eventos do Azure
 - [Espaço de Trabalho do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Este recurso está disponível em pontos de extremidade CDN para todos os tipos de preço. 

Os logs de diagnóstico do Azure permitem que você exporte métricas de uso básicas do seu ponto de extremidade da CDN para uma variedade de origens para poder consumi-las de forma personalizada. Por exemplo, você pode realizar os seguintes tipos de exportação de dados:

- Exportar os dados para o armazenamento de blobs, exportar para CSV e gerar grafos no Excel.
- Exportar dados para Hubs de Eventos e correlacionar com os dados de outros serviços do Azure.
- Exportar dados para logs do Azure Monitor e exibir dados em seu próprio espaço de trabalho do Log Analytics

O diagrama a seguir mostra uma visualização típica de dados básicos da CDN.

![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Figura 1 – Exibição de análise de núcleo de CDN*

Para obter mais informações sobre logs de diagnóstico, consulte [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Ativar o registro em log com o portal do Azure

Siga estas etapas para habilitar o registro com a análise de núcleo de CDN:

Entre no [Portal do Azure](https://portal.azure.com). Se você não já ativou a CDN para o fluxo de trabalho, [criar um perfil de CDN do Azure e o ponto de extremidade](cdn-create-new-endpoint.md) antes de continuar.

1. No portal do Azure, navegue até **perfil CDN**.

2. No portal do Azure, procure um perfil CDN ou selecione um do seu painel. Em seguida, selecione o ponto de extremidade CDN para o qual você deseja ativar os logs de diagnóstico.

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Selecione **Logs de diagnóstico** na seção MONITORAÇÃO.

   O **logs de diagnóstico** página será exibida.

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Habilitar registro em log com o Armazenamento do Azure

Para usar uma conta de armazenamento para armazenar os logs, siga estas etapas:
    
1. Para **Nome**, digite um nome para as configurações do log de diagnóstico.
 
2. Selecione **arquivo para uma conta de armazenamento**, em seguida, selecione **CoreAnalytics**. 

2. Para **retenção (dias)** , escolha o número de dias de retenção. Uma retenção de zero dias armazena os logs indefinidamente. 

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Selecione **conta de armazenamento**.

    A página **Selecionar uma conta de armazenamento** é exibida.

4. Selecione uma conta de armazenamento na lista suspensa e selecione **OK**.

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Depois de terminar de fazer as configurações de log de diagnóstico, selecione **salvar**.

### <a name="logging-with-azure-monitor"></a>Registro em log com o Azure Monitor

Para usar o Azure Monitor para armazenar os logs, siga estas etapas:

1. Na página **Logon de diagnósticos**, selecione **Enviar para o Log Analytics**. 

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Selecione **configurar** para configurar o log do Azure Monitor. 

   A página **Workspace do Log Analytics** é exibida.

    >[!NOTE] 
    >Os workspaces do OMS agora são chamados de workspaces do Log Analytics.

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Clique em **Criar Novo Workspace**.

    A página **Espaço de Trabalho do Log Analytics** é exibida.

    >[!NOTE] 
    >Os workspaces do OMS agora são chamados de workspaces do Log Analytics.

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/07_Create-new.png)

4. Para **espaço de trabalho do Log Analytics**, insira um nome de espaço de trabalho do Log Analytics. O nome do espaço de trabalho do Log Analytics deve ser exclusivo e conter apenas letras, números e hifens; espaços e sublinhados não são permitidos. 

5. Para **Assinatura**, selecione uma assinatura existente na lista suspensa. 

6. Para **Grupo de recursos**, crie um novo grupo de recursos ou selecione um existente.

7. Para **Local**, selecione um local na lista.

8. Selecione **Fixar no painel** se quiser salvar a configuração de registro no seu painel. 

9. Selecione **OK** para concluir a configuração.

10. Depois que seu workspace é criado, você é retornado à página **Logs de diagnóstico**. Confirme o nome do seu novo espaço de trabalho do Log Analytics.

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Selecione **CoreAnalytics**, em seguida, selecione **salvar**.

12. Para exibir o novo espaço de trabalho do Log Analytics, selecione **Análise principal** na página do ponto de extremidade da CDN.

    ![portal – Logs de diagnóstico](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    O espaço de trabalho do Log Analytics agora está pronta para registrar dados. Para consumir esses dados, você deve usar um [solução de logs do Azure Monitor](#consuming-diagnostics-logs-from-a-log-analytics-workspace), abordada posteriormente neste artigo.

Para obter mais informações sobre atrasos em dados de log, consulte [Log data delays](#log-data-delays) (Atrasos nos dados de log).

## <a name="enable-logging-with-powershell"></a>Habilitar o registro em log com o PowerShell

O exemplo a seguir mostra como habilitar os Logs de Diagnóstico por meio dos Cmdlets do Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Habilitando os Logs de Diagnóstico em uma conta de armazenamento

1. Faça o login e selecione uma assinatura:

    Connect-AzAccount 

    Select-AzureSubscription -SubscriptionId 

2. Para ativar os Logs de Diagnóstico em uma conta de Armazenamento, insira este comando:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Para ativar os logs de diagnósticos em um espaço de trabalho do Log Analytics, insira este comando:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Consumir logs de diagnóstico do Armazenamento do Azure
Esta seção descreve o esquema da análise de núcleo da CDN, como ele é organizado dentro de uma conta de armazenamento do Azure e oferece um exemplo de código para baixar os logs em um arquivo CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Usando o Gerenciador de Armazenamento do Microsoft Azure
Antes de poder acessar os dados da análise principal da Conta de Armazenamento do Azure, primeiro você precisa de uma ferramenta para acessar o conteúdo em uma conta de armazenamento. Embora haja várias ferramentas disponíveis no mercado, a que recomendamos é o Gerenciador de Armazenamento do Microsoft Azure. Para baixar a ferramenta, consulte [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/). Depois de baixar e instalar o software, configure-o para usar a mesma conta de armazenamento do Azure que foi configurada como um destino para os Logs de diagnóstico da CDN.

1.  Abra o **Gerenciador de Armazenamento do Microsoft Azure**
2.  Localize a conta de armazenamento
3.  Expanda o nó **Blob Containers** sob esta conta de armazenamento.
4.  Selecione o contêiner denominado *insights-logs-coreanalytics*.
5.  Os resultados aparecem no painel direito, começando com o primeiro nível, como *resourceId =* . Continue selecionando cada nível até encontrar o arquivo *PT1H.json*. Para obter uma explicação do caminho, confira [Formato de caminho de blob](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Cada arquivo blob *PT1H.json* representa os logs de análise por uma hora para um ponto de extremidade CDN específico ou seu domínio personalizado.
7.  O esquema do conteúdo desse arquivo JSON é descrito na seção Esquema dos logs de análise de núcleo.


#### <a name="blob-path-format"></a>Formato de caminho de blob

Os logs de análise de núcleo são gerados a cada hora e os dados são coletados e armazenados dentro de um único blob do Azure como um payload JSON. Como a ferramenta do Gerenciador de armazenamento interpreta '/' como um separador de diretório e mostra a hierarquia, o caminho para o blob do Azure é exibido como se houvesse uma estrutura hierárquica e representa o nome do blob. O nome do blob segue a convenção de nomenclatura abaixo:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Descrição dos campos:**

|Valor|DESCRIÇÃO|
|-------|---------|
|ID da assinatura    |A ID da assinatura do Azure no formato Guid.|
|Nome do Grupo de Recursos |Nome do grupo de recursos ao qual os recursos da CDN pertencem.|
|Nome do Perfil |Nome do perfil CDN|
|Nome do Ponto de Extremidade |Nome do ponto de extremidade da CDN|
|Ano|  Representação de quatro dígitos do ano, por exemplo, 2017|
|Mês| Representação de dois dígitos do número do mês. 01 = janeiro... 12 = dezembro|
|Day|   Representação de dois dígitos do dia do mês|
|PT1H.json| Arquivo JSON real em que os dados da análise são armazenados|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportando os dados de análise de núcleo para um arquivo CSV

Para facilitar o acesso à análise de núcleo, o exemplo de código de uma ferramenta é fornecido. Esta ferramenta permite fazer o download do arquivos JSON em um formato de arquivo simples separado por vírgula, que pode ser usado para criar gráficos ou outras agregações.

Aqui está como você pode usar a ferramenta:

1.  Visite o link do GitHub: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Baixe o código.
3.  Siga as instruções a serem compiladas e configuradas.
4.  Execute a ferramenta.
5.  O arquivo CSV resultante mostra os dados de análise em uma hierarquia simples.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Consumir logs de diagnóstico de um espaço de trabalho do Log Analytics
O Azure Monitor é um serviço do Azure que monitora a sua nuvem e ambientes para manter a disponibilidade e desempenho no local. Ele coleta dados gerados pelos recursos em seus ambientes de nuvem e locais e de outras ferramentas de monitoramento para fornecer análise de várias fontes. 

Para usar o Azure Monitor, você deve [habilitar registro em log](#enable-logging-with-azure-storage) no espaço de trabalho do Log Analytics do Azure, que é discutido neste artigo.

### <a name="using-the-log-analytics-workspace"></a>Usar o espaço de trabalho do Log Analytics

 O diagrama a seguir mostra a arquitetura das entradas e saídas do repositório:

![Espaço de trabalho do Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Figura 3 – Repositório do Log Analytics*

Você pode exibir os dados de várias maneiras usando soluções de gerenciamento. Você pode obter as soluções de gerenciamento do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Você pode instalar soluções de monitoramento do Azure marketplace, selecionando o **obtê-lo agora** link na parte inferior de cada solução.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Adicionar uma solução de monitoramento de CDN do Azure Monitor

Siga estas etapas para adicionar uma solução de monitoramento do Azure Monitor:

1.   Entre no portal do Azure usando sua assinatura do Azure e vá para o seu painel.
    ![Painel do Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Na página **Novo**, no **Marketplace**, selecione **Monitoring + management**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Na página **Monitoramento + gerenciamento**, selecione **Ver todos os**.

    ![Ver tudo](./media/cdn-diagnostics-log/15_See-all.png)

4. Pesquise pela CDN na caixa de pesquisa.

    ![Ver tudo](./media/cdn-diagnostics-log/16_Search-for.png)

5. Selecione **Análise de Núcleo de CDN do Azure**. 

    ![Ver tudo](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Depois de selecionar **Criar**, será solicitado que você crie um novo espaço de trabalho do Log Analytics ou use uma existente. 

    ![Ver tudo](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Selecione o workspace que você criou antes. Em seguida, você precisa adicionar uma conta de automação.

    ![Ver tudo](./media/cdn-diagnostics-log/19_Add-automation.png)

8. A tela a seguir mostra o formulário de conta de automação que você deve preencher. 

    ![Ver tudo](./media/cdn-diagnostics-log/20_Automation.png)

9. Depois que você criar a conta de automação, você estará pronto para adicionar sua solução. Selecione o botão **Criar**.

    ![Ver tudo](./media/cdn-diagnostics-log/21_Ready.png)

10. A solução foi agora adicionada ao workspace. Retorne para seu painel do Portal do Azure.

    ![Ver tudo](./media/cdn-diagnostics-log/22_Dashboard.png)

    Selecione o espaço de trabalho do Log Analytics que você criou para ir para o seu espaço de trabalho. 

11. Selecione o bloco **OMS Portal** para ver sua nova solução.

    ![Ver tudo](./media/cdn-diagnostics-log/23_workspace.png)

12. O portal deve agora ser semelhante à tela a seguir:

    ![Ver tudo](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Selecione um dos blocos para ver várias visualizações em seus dados.

    ![Ver tudo](./media/cdn-diagnostics-log/25_Interior-view.png)

    Você pode rolar à esquerda ou direita para ver mais blocos representando exibições individuais nos dados. 

    Selecione um dos blocos para ver mais detalhes sobre seus dados.

     ![Ver tudo](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Ofertas e tipos de preços

É possível visualizar as ofertas e os tipos de preço para soluções de gerenciamento [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions).

### <a name="customizing-views"></a>Personalizando exibições

Você pode personalizar a exibição em seus dados usando o **Designer de Exibição**. Para começar a projetar, vá para o espaço de trabalho do Log Analytics e selecione o bloco **View Designer**.

![Criador de Modos de Exibição](./media/cdn-diagnostics-log/27_Designer.png)

Arraste e solte os tipos de gráficos e preencha os detalhes dos dados que deseja analisar.

![Criador de Modos de Exibição](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Atrasos em dados de log

A tabela a seguir mostra atrasos em dados de log para **CDN Standard do Azure da Microsoft**, **CDN Standard do Azure da Akamai** e da **CDN Standard/Premium do Azure da Verizon**.

Atrasos em dados de log da Microsoft | Atrasos em dados de log da Verizon | Atrasos em dados de log da Akamai
--- | --- | ---
Atrasado em 1 hora. | Atrasado em 1 hora e pode demorar até 2 horas para começar a aparecer após a conclusão da propagação do ponto de extremidade. | Atrasado em 24 horas; se foram criados há mais de 24 horas, leva até 2 horas para eles começarem a ser exibidos. Se eles tiverem sido criados recentemente, poderá demorar até 25 horas para os logs começarem a aparecer.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Tipos de log de diagnóstico para análise de núcleo de CDN

Atualmente, a Microsoft oferece somente logs analíticos principais, que contêm métricas que mostram estatísticas de respostas HTTP e estatísticas de saída, conforme vistas dos POPs / arestas CDN.

### <a name="core-analytics-metrics-details"></a>Detalhes das métricas da análise de núcleo
A tabela a seguir mostra uma lista de métricas disponíveis nos logs de análises principais para **CDN Standard do Azure da Microsoft**, **CDN Standard do Azure da Akamai** e da **CDN Standard/Premium do Azure da Verizon**. Nem todas as métricas estão disponíveis de todos os provedores, embora essas diferenças sejam mínimas. A tabela também mostra se uma determinada métrica está disponível de um provedor. As métricas estão disponíveis somente para os terminais CDN que possuem tráfego neles.


|Métrica                     | DESCRIÇÃO | Microsoft | Verizon | Akamai |
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
| RequestCountCacheHit | Contagem de todas as solicitações que resultaram em um hit do Cache. O ativo foi servido diretamente do POP para o cliente. | Sim | sim | Não  |
| RequestCountCacheMiss | Contagem de todas as solicitações que resultaram em uma Perda do Cache. Uma falha no cache significa que o recurso não foi encontrado no POP mais próximo do cliente e, portanto, foi recuperado da Origem. | Sim | sim | Não |
| RequestCountCacheNoCache | Contagem de todas as solicitações para um ativo que são impedidas de serem armazenadas em cache devido a uma configuração do usuário na borda. | Sim | sim | Não |
| RequestCountCacheUncacheable | Contagem de todas as solicitações para ativos que são impedidas de serem armazenadas em cache pelos cabeçalhos Cache-Control e Expires do ativo, que indicam que não devem ser armazenadas em cache em um POP ou pelo cliente HTTP. | Sim | sim | Não |
| RequestCountCacheOthers | Contagem de todas as solicitações com o status de cache não cobertas pelos itens acima. | Não | Sim | Não  |
| EgressTotal | Transferência de dados de saída em GB | Sim |Sim |Sim |
| EgressHttpStatus2xx | Transferência de dados de saída* para respostas com códigos de status HTTP 2xx em GB. | Sim | sim | Não  |
| EgressHttpStatus3xx | Transferência de dados de saída para respostas com códigos de status HTTP 3xx em GB. | Sim | sim | Não  |
| EgressHttpStatus4xx | Transferência de dados de saída para respostas com códigos de status HTTP 4xx em GB. | Sim | sim | Não  |
| EgressHttpStatus5xx | Transferência de dados de saída para respostas com códigos de status HTTP 5xx em GB. | Sim | sim | Não |
| EgressHttpStatusOthers | Transferência de dados de saída para respostas com outros códigos de status HTTP em GB. | Sim | sim | Não  |
| EgressCacheHit | Transferência de dados de saída para respostas que foram entregues diretamente do cache da CDN nos POPs/Bordas da CDN. | Sim | sim | Não |
| EgressCacheMiss. | Transferência de dados de saída de respostas que não foram encontradas no servidor POP mais próximo e foram recuperadas do servidor de origem. | Sim | sim | Não |
| EgressCacheNoCache | Transferência de dados de saída para ativos que são impedidos de serem armazenados em cache devido a uma configuração do usuário na borda. | Sim | sim | Não |
| EgressCacheUncacheable | Transferência de dados de saída para ativos impedidos de serem armazenados em cache pelos cabeçalhos Cache-Control e/ou Expires do ativo. Indica que não deve ser armazenado em cache em um POP ou pelo cliente HTTP. | Sim | sim | Não |
| EgressCacheOthers | Transferências de dados de saída para outros cenários de cache. | Não | Sim | Não |

\* Transferência de dados de saída refere-se ao tráfego entregue de servidores POP da CDN para o cliente.


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

Em que *time* representa a hora de início do limite da hora pela qual as estatísticas são relatadas. Quando uma métrica não é compatível com um provedor da CDN, em vez de um valor duplo ou inteiro, há um valor nulo. Esse valor nulo indica a ausência de uma métrica e isso é diferente de um valor de 0. Há um conjunto dessas métricas por domínio configurado no ponto de extremidade.

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
* [Logs do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [API REST do Log Analytics do Azure](https://docs.microsoft.com/rest/api/loganalytics)







