---
title: Monitoramento, métricas e logs brutos para a CDN do Azure
description: Este artigo descreve como configurar e usar o monitoramento, as métricas e os logs brutos da CDN do Azure.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: 43f53d1098e08a0f913e3baec2c6aaf3d65054d0
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501383"
---
# <a name="real-time-monitoring-metrics-and-access-logs-for-azure-cdn"></a>Monitoramento em tempo real, métricas e logs de acesso para a CDN do Azure
Com a CDN do Azure da Microsoft, você pode monitorar recursos das seguintes maneiras para ajudá-lo a solucionar problemas, rastrear e depurar questões. 

* Os logs brutos fornecem informações avançadas sobre cada solicitação que a CDN recebe. Os logs brutos são diferentes dos logs de atividades. Os logs de atividade fornecem visibilidade das operações realizadas nos recursos do Azure.
* Métricas, que exibem quatro métricas-chave na CDN, incluindo a taxa de acertos de bytes, a contagem de solicitações, o tamanho da resposta e a latência total. Ele também fornece dimensões diferentes para dividir as métricas.
* Alerta, que permite que o cliente configure o alerta para métricas-chave
* Métricas adicionais, que permitem que os clientes usem o Azure Log Analytics para habilitar métricas adicionais de valor. Também fornecemos exemplos de consulta para algumas outras métricas no Azure Log Analytics.

> [!IMPORTANT]
> O recurso de logs brutos HTTP está disponível para a CDN do Azure da Microsoft.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration---azure-portal"></a>Configuração-portal do Azure

Para configurar logs brutos para seu perfil da CDN do Azure da Microsoft: 

1. No menu portal do Azure, selecione **todos os recursos**  >>  **\<your-CDN-profile>** .

2. Em **Monitoramento**, selecione **Configurações de diagnóstico**.

3. Selecione **+ Adicionar configuração de diagnóstico**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="Adicionar configuração de diagnóstico para o perfil CDN." border="true":::
    
    > [!IMPORTANT]
    > Os logs brutos só estão disponíveis no nível do perfil enquanto os logs de código de status http agregados estão disponíveis no nível do ponto de extremidade.

4. Em **Configurações de diagnóstico**, insira um nome em **Nome das configurações de diagnóstico**.

5. Selecione o **AzureCdnAccessLog** e defina a retenção em dias.

6. Selecione os **Detalhes do destino**. As opções de destino são:
    * **Enviar para o Log Analytics**
        * Selecione a **Assinatura** e o **Workspace do Log Analytics**.
    * **Arquivar em uma conta de armazenamento**
        * Selecione a **Assinatura** e a **Conta de Armazenamento**.
    * **Transmitir por streaming para um hub de eventos**
        * Selecione a **Assinatura**, **Namespace do hub de eventos**, **Nome do hub de eventos (opcional)** e **Nome de política do hub de eventos**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="Configure o destino para as configurações de log." border="true":::

7. Selecione **Salvar**.

## <a name="configuration---azure-powershell"></a>Configuração-Azure PowerShell

Use [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) para definir a configuração de diagnóstico para logs brutos.

Os dados de retenção são definidos pela opção **-RetentionInDays** no comando.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Habilitar os logs de diagnóstico em uma conta de armazenamento

1. Entre no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Para habilitar os logs de diagnóstico em uma conta de armazenamento, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Habilitar logs de diagnóstico para Log Analytics espaço de trabalho

1. Entre no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Para habilitar os logs de diagnóstico para um espaço de trabalho Log Analytics, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Habilitar logs de diagnóstico para o namespace do hub de eventos

1. Entre no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Para habilitar os logs de diagnóstico para um namespace de Hub de eventos, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>Propriedades de logs brutos

Atualmente, a CDN do Azure do serviço da Microsoft fornece logs brutos. Os logs brutos fornecem solicitações de API individuais com cada entrada com o seguinte esquema: 

| Propriedade  | Descrição |
| ------------- | ------------- |
| BackendHostname | Se a solicitação estiver sendo encaminhada para um back-end, esse campo representará o nome do host do back-end. Esse campo ficará em branco se a solicitação for redirecionada ou encaminhada para um cache regional (quando o Caching for habilitado para a regra de roteamento). |
| CacheStatus | Para cenários de cache, esse campo define o impacto/perda do cache no POP |
| ClientIp | Endereço IP do cliente que fez a solicitação. Se houver um cabeçalho X-Forwardd-for na solicitação, o IP do cliente será escolhido do mesmo. |
| ClientPort | A porta IP do cliente que fez a solicitação. |
| HttpMethod | Método HTTP usado pela solicitação. |
| HttpStatusCode | O código de status HTTP retornado do proxy. |
| HttpStatusDetails | Status resultante na solicitação. O significado desse valor de cadeia de caracteres pode ser encontrado em uma tabela de referência de status. |
| HttpVersion | O tipo da conexão ou solicitação. |
| POP | Nome curto da borda em que a solicitação descarregou. |
| RequestBytes | O tamanho da mensagem de solicitação HTTP em bytes, incluindo os cabeçalhos de solicitação e o corpo da solicitação. |
| RequestUri | URI da solicitação recebida. |
| ResponseBytes | Bytes enviados pelo servidor de back-end como a resposta.  |
| RoutingRuleName | O nome da regra de roteamento com a qual a solicitação foi correspondida. |
| RulesEngineMatchNames | Os nomes das regras que a solicitação correspondeu. |
| SecurityProtocol | A versão do protocolo TLS/SSL usada pela solicitação ou NULL se não houver criptografia. |
| SentToOriginShield </br> (preterido) * **Veja observações sobre a reprovação na seção a seguir.**| Se for verdadeiro, significa que a solicitação foi respondida do cache da blindagem de origem em vez do pop de borda. A blindagem de origem é um cache pai usado para melhorar a taxa de acertos do cache. |
| isReceivedFromClient | Se for true, significa que a solicitação veio do cliente. Se for false, a solicitação será um erro na borda (POP filho) e será respondida do escudo de origem (POP pai). |
| TimeTaken | O período de tempo do primeiro byte de solicitação na porta frontal até o último byte de resposta, em segundos. |
| TrackingReference | A cadeia de caracteres de referência exclusiva que identifica uma solicitação atendida pela Front Door, também enviada como o cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos logs de acesso para uma solicitação específica. |
| UserAgent | O tipo de navegador que o cliente usou. |
| ErrorInfo | Esse campo contém o tipo específico de erro para restringir a área de solução de problemas. </br> Os valores possíveis incluem: </br> **NOERROR**: indica que nenhum erro foi encontrado. </br> **CertificateError**: erro de certificado SSL genérico.</br> **CertificateNameCheckFailed**: o nome do host no certificado SSL é inválido ou não corresponde. </br> **ClientDisconnected**: falha de solicitação devido à conexão de rede do cliente. </br> **UnspecifiedClientError**: erro de cliente genérico. </br> **InvalidRequest**: solicitação inválida. Pode ocorrer devido a cabeçalho, corpo e URL malformados. </br> **DNSFailure**: falha de DNS. </br> **DNSNameNotResolved**: não foi possível resolver o nome ou o endereço do servidor. </br> **OriginConnectionAborted**: a conexão com a origem foi interrompida abruptamente. </br> **OriginConnectionError**: erro de conexão de origem genérica. </br> **OriginConnectionRefused**: a conexão com a origem não foi capaz de estabelecer. </br> **OriginError**: erro de origem genérica. </br> **OriginInvalidResponse**: Origin retornou uma resposta inválida ou não reconhecida. </br> **OriginTimeout**: o período de tempo limite para a solicitação de origem expirou. </br> **ResponseHeaderTooBig**: a origem retornou muito grande de um cabeçalho de resposta. </br> **RestrictedIP**: a solicitação foi bloqueada devido ao IP restrito. </br> **SSLHandshakeError**: não é possível estabelecer a conexão com a origem devido a uma falha de mistura de SSL. </br> **UnspecifiedError**: ocorreu um erro que não se ajustou a nenhum dos erros na tabela. |
| TimeToFirstByte | O período de tempo, em milissegundos, desde quando a CDN da Microsoft recebe a solicitação até o momento em que o primeiro byte é enviado ao cliente. O tempo é medido somente do lado da Microsoft. Os dados do lado do cliente não são medidos. |
> [!NOTE]
> Os logs podem ser exibidos em seu perfil de Log Analytics executando uma consulta. Uma consulta de exemplo teria A seguinte aparência:
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>Enviado à substituição da blindagem de origem
A propriedade de log bruto **isSentToOriginShield** foi preterida e substituída por um novo campo **isReceivedFromClient**. Use o novo campo se você já estiver usando o campo preterido. 

Os logs brutos incluem logs gerados a partir da borda da CDN (POP filho) e da blindagem de origem. A blindagem de origem se refere a nós pai que estão estrategicamente localizados em todo o mundo. Esses nós se comunicam com servidores de origem e reduzem a carga de tráfego na origem. 

Para cada solicitação que vai para a blindagem de origem, há duas entradas de log:

* Um para nós de borda
* Uma para a blindagem de origem. 

Para diferenciar a saída ou as respostas dos nós de borda versus a blindagem de origem, você pode usar o campo **isReceivedFromClient** para obter os dados corretos. 

Se o valor for false, isso significa que a solicitação é respondida da blindagem de origem para nós de borda. Essa abordagem é eficaz para comparar logs brutos com dados de cobrança. Os encargos não são incorridos para a saída da blindagem de origem para os nós de borda. Os encargos são incorridos para a saída dos nós de borda para os clientes. 

**Exemplo de consulta Kusto para excluir logs gerados no escudo de origem no Log Analytics.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> O recurso de logs brutos HTTP está disponível automaticamente para todos os perfis criados ou atualizados após **25 de fevereiro de 2020**. Para perfis da CDN criados anteriormente, é necessário atualizar o ponto de extremidade da CDN depois de configurar o registro em log. Por exemplo, é possível navegar para a filtragem geográfica em pontos de extremidade da CDN e bloquear qualquer país/região que não seja relevante para sua carga de trabalho e clicar em salvar.


## <a name="metrics"></a>Métricas
A CDN do Azure da Microsoft é integrada com Azure Monitor e publica quatro métricas de CDN para ajudar a controlar, solucionar problemas e depurar problemas. 

As métricas são exibidas em gráficos e acessíveis por meio do PowerShell, da CLI e da API. As métricas da CDN são gratuitas.

A CDN do Azure da Microsoft mede e envia suas métricas em intervalos de 60 segundos. As métricas podem levar até 3 minutos para aparecer no Portal. 

Para obter mais informações, consulte [Azure monitor métricas](../azure-monitor/platform/data-platform-metrics.md).

**Métricas com suporte da CDN do Azure da Microsoft**

| Métricas  | Descrição | Dimensões |
| ------------- | ------------- | ------------- |
| Taxa de acertos de bytes * | A porcentagem de egresso do cache da CDN, computada com relação à saída total. | Ponto de extremidade |
| RequestCount | O número de solicitações de cliente atendidas pela CDN. | Ponto de extremidade </br> País do cliente. </br> Região do cliente. </br> Código de status HTTP. </br> Grupo de status HTTP. |
| ResponseSize | O número de bytes enviados como respostas da borda CDN para os clientes. |Ponto de extremidade </br> País do cliente. </br> Região do cliente. </br> Código de status HTTP. </br> Grupo de status HTTP. |
| TotalLatency | O tempo total da solicitação do cliente recebida pela CDN **até que o último byte de resposta seja enviado da CDN para o cliente**. |Ponto de extremidade </br> País do cliente. </br> Região do cliente. </br> Código de status HTTP. </br> Grupo de status HTTP. |

**_Bytes atingido ração = (saída da borda-saída da origem)/egress do Edge_*

Cenários excluídos no cálculo da taxa de acertos de bytes:

* Você não configura explicitamente nenhum cache por meio do mecanismo de regras ou do comportamento de cache de cadeia de caracteres de consulta.
* Você configura explicitamente a diretiva de controle de cache com cache não armazenado ou privado.

### <a name="metrics-configuration"></a>Configuração de métricas

1. No menu portal do Azure, selecione **todos os recursos**  >>  **\<your-CDN-profile>** .

2. Em **monitoramento**, selecione **métricas**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="Métricas para o perfil CDN." border="true":::

3. Selecione **Adicionar métrica**, selecione a métrica a ser adicionada:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="Adicione e selecione a métrica para o perfil CDN." border="true":::

4. Selecione **Adicionar filtro** para adicionar um filtro:
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="Aplicar filtro à métrica." border="true":::

5. Selecione **aplicar** divisão para ver a tendência por dimensões diferentes:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="Aplique a divisão à métrica." border="true":::

6. Selecione **novo gráfico** para adicionar um novo gráfico:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="Adicione um novo gráfico à exibição de métrica." border="true":::

### <a name="alerts"></a>Alertas

Você pode configurar alertas na CDN da Microsoft selecionando alertas de **monitoramento**  >>  **Alerts**.

Selecione **nova regra de alerta** para as métricas listadas na seção métricas:

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="Configure alertas para o ponto de extremidade CDN." border="true":::

O alerta será cobrado com base em Azure Monitor. Para obter mais informações sobre alertas, consulte [Azure monitor alertas](../azure-monitor/platform/alerts-overview.md).

### <a name="additional-metrics"></a>Métricas adicionais
Você pode habilitar métricas adicionais usando o Azure Log Analytics e logs brutos para obter um custo adicional.

1. Siga as etapas acima em habilitando o diagnóstico para enviar o log bruto para o log Analytics.

2. Selecione o espaço de trabalho Log Analytics que você criou:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="Selecionar espaço de trabalho do log Analytics" border="true":::   

3. Selecione **logs** em **geral** no espaço de trabalho do log Analytics.  Em seguida, selecione **introdução**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Espaço de trabalho de recursos do log Analytics." border="true":::   
 
4. Selecione **perfis CDN**.  Selecione um exemplo de consulta para executar ou fechar a tela de exemplo para inserir uma consulta personalizada:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="Exemplo de tela de consulta." border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="Execução da consulta." border="true":::   

4. Para exibir dados por gráfico, selecione **gráfico**.  Selecione **fixar no painel** para fixar o gráfico no painel do Azure:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="Fixe um gráfico no painel." border="true"::: 

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você habilitou os logs brutos HTTP para o serviço de CDN da Microsoft.

Para obter mais informações sobre a CDN do Azure e os outros serviços do Azure mencionados neste artigo, consulte:

* [Analisar](cdn-log-analysis.md) os padrões de uso da CDN do Azure.

* Saiba mais sobre o [Azure Monitor](../azure-monitor/overview.md).

* Configurar o [Log Analytics no Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md).