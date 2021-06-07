---
title: Monitorar logs do firewall do aplicativo Web do Azure
description: Saiba como habilitar e gerenciar logs e para o Firewall do aplicativo Web do Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 116c12900f015c849c9492ed67bc11d116286c43
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611922"
---
# <a name="resource-logs-for-azure-web-application-firewall"></a>Logs de recursos para o Firewall do aplicativo Web do Azure

Você pode monitorar os recursos de firewall do aplicativo Web usando logs. Você pode salvar o desempenho, o acesso e outros dados ou consumi-los de um recurso para fins de monitoramento.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>Logs de diagnóstico

Você pode usar tipos diferentes de logs no Azure para gerenciar e solucionar problemas de gateways de aplicativo. Você pode acessar alguns desses logs por meio do portal. Todos os logs podem ser extraídos de um Armazenamento de blobs do Azure e exibidos em diferentes ferramentas, como [logs do Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md), Excel e Power BI. Saiba mais sobre os tipos diferentes de logs na lista a seguir:

* **Log de atividades**: você pode usar os [logs de atividades do Azure](../../azure-resource-manager/management/view-activity-logs.md) para exibir todas as operações que são enviadas à sua assinatura do Azure e seu status. As entradas do log de atividades são coletadas por padrão e podem ser exibidas no portal do Azure.
* **Acessar o log de recursos**: você pode usar esse log para exibir padrões de acesso do gateway de aplicativo e analisar informações importantes. Isso inclui o IP do chamador, a URL solicitada, a latência de resposta, o código de retorno e os bytes de entrada e saída. Um log de acesso é coletado a cada 300 segundos. Esse log contém um registro por instância do Gateway de Aplicativo. A instância do Gateway de Aplicativo é identificada pela propriedade instanceId.
* **Log de recursos de desempenho**: você pode usar esse log para exibir como as instâncias do gateway de aplicativo estão sendo executadas. Esse log captura informações de desempenho de cada instância, incluindo o total de solicitações atendidas, a vazão de dados em bytes, o total de solicitações atendidas, a contagem de solicitações com falha e a contagem de instâncias de back-end íntegras ou não íntegras. Um log de desempenho é coletado a cada 60 segundos. O log de desempenho está disponível apenas para a SKU v1. Para a SKU v2, use [métricas](../../application-gateway/application-gateway-metrics.md) para dados de desempenho.
* **Log de recursos do firewall**: você pode usar esse log para exibir as solicitações que são registradas por meio do modo de detecção ou prevenção de um gateway de aplicativo configurado com o Firewall do aplicativo Web.

> [!NOTE]
> Os logs estão disponíveis apenas para os recursos implantados no modelo de implantação do Azure Resource Manager. Você não pode usar logs para recursos do modelo de implantação clássico. Para obter um melhor entendimento dos dois modelos, consulte o artigo [Noções básicas sobre a implantação do Resource Manager e a implantação clássica](../../azure-resource-manager/management/deployment-models.md).

Você tem três opções para armazenar os logs:

* **Conta de armazenamento**: as contas de armazenamento são mais adequadas para os logs quando eles são armazenados por mais tempo e examinados quando necessário.
* **Hubs de eventos**: os hubs de eventos são uma ótima opção para integração com outras ferramentas de Siem (gerenciamento de eventos e informações de segurança) para obter alertas sobre seus recursos.
* **Logs de Azure monitor**: os logs de Azure monitor são mais bem usados para o monitoramento geral em tempo real de seu aplicativo ou a análise de tendências.

### <a name="enable-logging-through-powershell"></a>Habilitar o log por meio do PowerShell

O log de atividade é habilitado automaticamente para todos os recursos do Resource Manager. Você deve habilitar o log de acesso e de desempenho para começar a coletar os dados disponíveis por meio desses logs. Para habilitar o log, use as seguintes etapas:

1. Anote a ID do recurso da conta de armazenamento, na qual os dados de log são armazenados. Esse valor está no formato:/subscriptions/ \<subscriptionId\> /ResourceGroups/ \<resource group name\> /Providers/Microsoft.Storage/storageAccounts/ \<storage account name\> . Use qualquer conta de armazenamento em sua assinatura. Use o portal do Azure para encontrar essas informações.

    ![Portal: ID do recurso da conta de armazenamento](../media/web-application-firewall-logs/diagnostics1.png)

2. Anote a ID do Recurso do gateway de aplicativo para o qual o log está habilitado. Esse valor está no formato:/subscriptions/ \<subscriptionId\> /ResourceGroups/ \<resource group name\> /Providers/Microsoft.Network/applicationGateways/ \<application gateway name\> . Use o portal para encontrar essas informações.

    ![Portal: ID do recurso do gateway de aplicativo](../media/web-application-firewall-logs/diagnostics2.png)

3. Habilite o log de recursos usando o seguinte cmdlet do PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>Os logs de atividades não exigem uma conta de armazenamento separada. O uso do armazenamento para logs de acesso e de desempenho gera encargos de serviço.

### <a name="enable-logging-through-the-azure-portal"></a>Habilitar o log por meio do portal do Azure

1. No portal do Azure, localize o recurso e selecione **configurações de diagnóstico**.

   Para o Gateway de Aplicativo, três logs estão disponíveis:

   * Log de acesso
   * Log de desempenho
   * Log de firewall

2. Para começar a coletar dados, selecione **Ativar diagnóstico**.

   ![Ativando o diagnóstico][1]

3. A página **configurações de diagnóstico** fornece as configurações para os logs de recursos. Neste exemplo, o Log Analytics armazena os logs. Você também pode usar os hubs de eventos e uma conta de armazenamento para salvar os logs de recursos.

   ![Iniciando o processo de configuração][2]

5. Digite um nome para as configurações, confirme as configurações e selecione **salvar**.

### <a name="activity-log"></a>Log de atividades

O Azure gera o log de atividades por padrão. Os logs são preservados por 90 dias no armazenamento de logs de eventos do Azure. Saiba mais sobre esses logs lendo o artigo [Exibir eventos e o log de atividades](../../azure-resource-manager/management/view-activity-logs.md).

### <a name="access-log"></a>Log de acesso

O log de acesso é gerado apenas se você o habilitou em cada instância do Gateway de Aplicativo, conforme detalhado nas etapas anteriores. Os dados são armazenados na conta de armazenamento especificada quando o log foi habilitado. Cada acesso do gateway de aplicativo é registrado no formato JSON, conforme mostrado no exemplo a seguir para V1:

|Valor  |Descrição  |
|---------|---------|
|instanceId     | Instância do Gateway de Aplicativo que atendeu à solicitação.        |
|clientIP     | IP de origem da solicitação.        |
|clientPort     | Porta de origem da solicitação.       |
|httpMethod     | Método HTTP usado pela solicitação.       |
|requestUri     | URI da solicitação recebida.        |
|RequestQuery     | **Server-Routed**: instância do pool de back-end que recebeu a solicitação.</br>**X-AzureApplicationGateway-LOG-ID**: ID de Correlação usada para a solicitação. Ela pode ser usada para solucionar problemas de tráfego nos servidores back-end. </br>**SERVER-STATUS**: código de resposta HTTP que o Gateway de Aplicativo recebeu do back-end.       |
|UserAgent     | Agente do usuário do cabeçalho da solicitação HTTP.        |
|httpStatus     | Código de status HTTP retornado ao cliente do Gateway de Aplicativo.       |
|httpVersion     | Versão HTTP da solicitação.        |
|receivedBytes     | Tamanho do pacote recebido, em bytes.        |
|sentBytes| Tamanho do pacote enviado, em bytes.|
|timeTaken| Duração (em milissegundos) necessária para que uma solicitação seja processada e sua resposta seja enviada. Isso é calculado como o intervalo a partir da hora em que o Gateway de Aplicativo recebe o primeiro byte de uma solicitação HTTP até a hora em que a operação de envio de resposta é concluída. É importante observar que o campo Time-Taken geralmente inclui a hora em que os pacotes de solicitação e resposta são transmitidos pela rede. |
|sslEnabled| Se a comunicação com os pools de back-end usavam TLS/SSL. Os valores válidos são ativado e desativado.|
|host| O nome do host com o qual a solicitação foi enviada para o servidor de back-end. Se o nome de host de back-end estiver sendo substituído, este deverá refletir isso.|
|originalHost| O nome do host com o qual a solicitação foi recebida pelo gateway de aplicativo do cliente.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "timestamp": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
Para o gateway de aplicativo e o WAF v2, os logs mostram um pouco mais de informações:

|Valor  |Descrição  |
|---------|---------|
|instanceId     | Instância do Gateway de Aplicativo que atendeu à solicitação.        |
|clientIP     | IP de origem da solicitação.        |
|clientPort     | Porta de origem da solicitação.       |
|httpMethod     | Método HTTP usado pela solicitação.       |
|requestUri     | URI da solicitação recebida.        |
|UserAgent     | Agente do usuário do cabeçalho da solicitação HTTP.        |
|httpStatus     | Código de status HTTP retornado ao cliente do Gateway de Aplicativo.       |
|httpVersion     | Versão HTTP da solicitação.        |
|receivedBytes     | Tamanho do pacote recebido, em bytes.        |
|sentBytes| Tamanho do pacote enviado, em bytes.|
|timeTaken| Duração (em milissegundos) necessária para que uma solicitação seja processada e sua resposta seja enviada. Isso é calculado como o intervalo a partir da hora em que o Gateway de Aplicativo recebe o primeiro byte de uma solicitação HTTP até a hora em que a operação de envio de resposta é concluída. É importante observar que o campo Time-Taken geralmente inclui a hora em que os pacotes de solicitação e resposta são transmitidos pela rede. |
|sslEnabled| Se a comunicação com os pools de back-end usava o TLS. Os valores válidos são ativado e desativado.|
|sslCipher| Conjunto de codificação que está sendo usado para comunicação TLS (se o TLS estiver habilitado).|
|sslProtocol| Protocolo TLS que está sendo usado (se o TLS estiver habilitado).|
|serverRouted| O servidor back-end para o qual o gateway de aplicativo roteia a solicitação.|
|serverStatus| Código de status HTTP do servidor de back-end.|
|serverResponseLatency| Latência da resposta do servidor de back-end.|
|host| Endereço listado no cabeçalho do host da solicitação.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>Log de desempenho

O log de desempenho é gerado apenas se você o habilitou em cada instância do Gateway de Aplicativo, conforme detalhado nas etapas anteriores. Os dados são armazenados na conta de armazenamento especificada quando o log foi habilitado. Os dados do log de desempenho são gerados em intervalos de 1 minuto. Ele está disponível apenas para a SKU v1. Para a SKU v2, use [métricas](../../application-gateway/application-gateway-metrics.md) para dados de desempenho. Os seguintes dados são registrados em log:


|Valor  |Descrição  |
|---------|---------|
|instanceId     |  Instância do Gateway de Aplicativo para a qual os dados de desempenho estão sendo gerados. Para um gateway de aplicativo de várias instâncias, há uma linha por instância.        |
|healthyHostCount     | Número de hosts íntegros no pool de back-end.        |
|unHealthyHostCount     | Número de hosts não íntegros no pool de back-end.        |
|requestCount     | Número de solicitações atendidas.        |
|latência | Latência média (em milissegundos) de solicitações da instância para o back-end que atende às solicitações. |
|failedRequestCount| Número de solicitações com falha.|
|throughput| Vazão de dados média desde o último log, medida em bytes por segundo.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> A latência é calculada a partir da hora em que o primeiro byte da solicitação HTTP é recebido até a hora em que o último byte da resposta HTTP é enviado. É a soma do tempo de processamento do Gateway de Aplicativo e do custo de rede para o back-end, mais o tempo que o back-end leva para processar a solicitação.

### <a name="firewall-log"></a>Log de firewall

O log de firewall é gerado apenas se você o habilitou em cada gateway de aplicativo, conforme detalhado nas etapas anteriores. Esse log também exige a configuração de um firewall de aplicativo Web em um gateway de aplicativo. Os dados são armazenados na conta de armazenamento especificada quando o log foi habilitado. Os seguintes dados são registrados em log:


|Valor  |Descrição  |
|---------|---------|
|instanceId     | Instância do Gateway de Aplicativo para a qual os dados de firewall estão sendo gerados. Para um gateway de aplicativo de várias instâncias, há uma linha por instância.         |
|clientIp     |   IP de origem da solicitação.      |
|clientPort     |  Porta de origem da solicitação.       |
|requestUri     | URL da solicitação recebida.       |
|ruleSetType     | Tipo de conjunto de regras. O valor disponível é OWASP.        |
|ruleSetVersion     | Versão utilizada do conjunto de regras. Os valores disponíveis são 2.2.9 e 3.0.     |
|ruleId     | ID da Regra do evento de gatilho.        |
|message     | Mensagem amigável para o evento de gatilho. Mais detalhes são fornecidos na seção de detalhes.        |
|ação     |  Ação executada na solicitação. Os valores disponíveis são bloqueados e permitidos (para regras personalizadas), correspondidas (quando uma regra corresponde a uma parte da solicitação) e detectadas e bloqueadas (elas são ambas para regras obrigatórias, dependendo de se o WAF está no modo de detecção ou prevenção).      |
|site     | Site para o qual o log foi gerado. No momento, somente Global é listado porque as regras são globais.|
|detalhes     | Detalhes do evento de gatilho.        |
|details.message     | Descrição da regra.        |
|details.data     | Dados específicos encontrados na solicitação que corresponderam à regra.         |
|details.file     | Arquivo de configuração que continha a regra.        |
|details.line     | Número de linha no arquivo de configuração que disparou o evento.       |
|hostname   | Nome do host ou endereço IP do gateway de aplicativo.    |
|transactionId  | ID exclusiva para uma determinada transação que ajuda a agrupar várias violações de regra que ocorreram na mesma solicitação.   |
|policyId   | ID exclusiva da política de firewall associada ao gateway de aplicativo, ao ouvinte ou ao caminho.   |
|policyScope    | O local dos valores de política pode ser "global", "ouvinte" ou "local".   |
|policyScopeName   | O nome do objeto em que a política é aplicada.    |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.147",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343",
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/perListener",
      "policyScope": "Listener",
      "policyScopeName": "httpListener1"
    }
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>Exibir e analisar o log de atividades

Você pode exibir e analisar os dados do log de atividades usando um dos seguintes métodos:

* **Ferramentas do Azure**: recupere informações do log de atividades por meio do Azure PowerShell, da CLI do Azure, da API REST do Azure ou do portal do Azure. As instruções passo a passo para cada método são detalhadas no artigo [Activity operations with Resource Manager](../../azure-resource-manager/management/view-activity-logs.md) (Operações de atividade com o Resource Manager).
* **Power BI**: se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing), experimente uma gratuitamente. Usando os [aplicativos de modelo de Power bi](/power-bi/service-template-apps-overview), você pode analisar seus dados.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Exibir e analisar os logs de acesso, de desempenho e de firewall

Os [logs do Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md) podem coletar os arquivos de log de contadores e eventos de sua conta de Armazenamento de blobs. Ele inclui visualizações e funcionalidades de pesquisa avançadas para analisar os logs.

Você também pode se conectar à sua conta de armazenamento e recuperar as entradas de log JSON para logs de desempenho e acesso. Depois de baixar os arquivos JSON, você pode convertê-los em CSV e exibi-los no Excel, no Power BI ou em qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com o Visual Studio e os conceitos básicos de alteração de valores de constantes e variáveis em C#, você poderá usar as [ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Analisar logs de acesso por meio do GoAccess

Publicamos um modelo do Resource Manager que instala e executa o popular analisador de logs [GoAccess](https://goaccess.io/) para logs de acesso do Gateway de Aplicativo. O GoAccess fornece valiosas estatísticas de tráfego HTTP, tais como visitantes exclusivos, arquivos solicitados, hosts, sistemas operacionais, navegadores, códigos de status HTTP e muito mais. Para obter mais detalhes, consulte o [arquivo Leiame na pasta de modelo do Resource Manager no GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Próximas etapas

* Visualize o contador e os logs de eventos com os [logs do Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md).
* [Visualize o log de atividades do Azure com](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) a postagem de blog Power bi.
* Postagem no blog [View and analyze Azure Activity Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Exibir e analisar os Logs de Atividades do Azure no Power BI e muito mais).

[1]: ../media/web-application-firewall-logs/figure1.png
[2]: ../media/web-application-firewall-logs/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
