---
title: Monitorar e gerenciar trabalhos do Azure Stream Analytics com o PowerShell
description: Este artigo descreve como usar os cmdlets do Azure PowerShell para monitorar e gerenciar trabalhos do Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/28/2017
ms.openlocfilehash: 6b404516c513dea0888974ffb4fa3d8d43db6c44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015158"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitorar e gerenciar trabalhos do Stream Analytics usando cmdlets do Azure PowerShell
Saiba como monitorar e gerenciar os recursos do Stream Analytics com os cmdlets do Azure PowerShell e script do PowerShell que executam tarefas básicas de análise de fluxo.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Pré-requisitos para a execução de cmdlets do PowerShell do Azure para Stream Analytics
* Crie um grupo de recursos do Azure em sua assinatura. O seguinte é um exemplo de script do PowerShell do Azure. Para obter mais informações sobre o PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure](/powershell/azure/).  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1.0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription -SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Os trabalhos do Stream Analytics criados programaticamente não têm monitoramento habilitado por padrão.  Você pode habilitar manualmente o monitoramento no Portal do Azure, navegando até a página de monitoramento do trabalho e clicando no botão Habilitar. Ou então, você pode fazer isso programaticamente, seguindo as etapas em [Azure Stream Analytics – Monitorar programaticamente os trabalhos do Stream Analytics](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Cmdlets do PowerShell do Azure para Stream Analytics
Os seguintes cmdlets do PowerShell do Azure podem ser usados para monitorar e gerenciar trabalhos de Stream Analytics do Azure. Observe que o Azure PowerShell tem diferentes versões. 
**Nos exemplos listados, o primeiro comando é para o Azure PowerShell 0.9.8 e o segundo comando é para o Azure PowerShell 1.0.** Os comandos do Azure PowerShell 1.0 sempre terão “Az” no comando.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Lista todas os trabalhos de Stream Analytics definidos na assinatura do Azure ou especificados no grupo de recursos ou obtém informações sobre um trabalho específico dentro de um grupo de recursos.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

Esse comando do PowerShell retorna informações sobre todos os trabalhos do Stream Analytics na assinatura do Azure.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Esse comando do PowerShell retorna informações sobre todos os trabalhos de Stream Analytics no grupo de recursos StreamAnalytics-Default-Central-US.

**Exemplo 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Esse comando do PowerShell retorna informações sobre o trabalho StreamingJob do Stream Analytics no grupo de recursos StreamAnalytics-Default-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Lista todas as entradas que são definidas em um trabalho específico de Stream Analytics ou obtém informações sobre uma entrada específica.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Esse comando do PowerShell retorna informações sobre todas as entradas definidas no trabalho StreamingJob.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Esse comando do PowerShell retorna informações sobre a entrada denominada EntryStream definida no trabalho StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Lista todas as saídas que são definidas em um trabalho específico de Stream Analytics ou obtém informações sobre uma saída específica.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Esse comando do PowerShell retorna informações sobre as saídas definidas no trabalho StreamingJob.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Esse comando do PowerShell retorna informações sobre a saída denominada Output definida no trabalho StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Obtém informações sobre a cota de streaming de unidades em uma região especificada.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

Esse comando do PowerShell retorna informações sobre a cota e o uso de unidades de streaming na região Central dos Estados Unidos.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Obtém informações sobre uma transformação específica definida no trabalho de Stream Analytics.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Esse comando do PowerShell retorna informações sobre a transformação chamada StreamingJob no trabalho StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Cria uma nova entrada dentro do trabalho do Stream Analytics ou atualiza uma entrada existente especificada.

O nome da entrada pode ser especificado no arquivo .json ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do arquivo.

Se você especificar uma entrada que já existe e não especificar o parâmetro -Force, o cmdlet perguntará se deseja ou não substituir a entrada existente.

Se você especificar o parâmetro -Force e especificar um nome de entrada existente, a entrada será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura do arquivo JSON e o conteúdo, consulte a seção [Criar entrada (Stream Analytics do Azure)][msdn-rest-api-create-stream-analytics-input] da [Biblioteca de referência da API REST de gerenciamento do Stream Analytics][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Esse comando do PowerShell cria uma nova entrada do arquivo Input.json. Se uma entrada existente com o nome especificado no arquivo de definição de entrada já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Esse comando do PowerShell cria uma nova entrada no trabalho chamado EntryStream. Se uma entrada existente com esse nome já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Esse comando do PowerShell substitui a definição da fonte de entrada existente chamada EntryStream com a definição do arquivo.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Cria um novo trabalho de Stream Analytics no Microsoft Azure ou atualiza a definição de um trabalho existente especificado.

O nome do trabalho pode ser especificado no arquivo .json ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do arquivo.

Se você especificar um nome de trabalho que já existe e não especificar o parâmetro -Force, o cmdlet perguntará se deseja ou não substituir o trabalho existente.

Se você especificar o parâmetro -Force e especificar um nome de trabalho existente, a definição do trabalho será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura do arquivo JSON e o conteúdo, consulte a seção [Criar trabalho de Stream Analytics][msdn-rest-api-create-stream-analytics-job] da [Biblioteca de referência da API REST de gerenciamento do Stream Analytics][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Esse comando do PowerShell cria um novo trabalho por meio da definição em JobDefinition.json. Se um trabalho existente com o nome especificado no arquivo de definição de trabalho já estiver definido, o cmdlet perguntará se deseja ou não substituí-lo.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Esse comando do PowerShell substitui a definição de trabalho para StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Cria uma nova saída dentro de um trabalho de Stream Analytics ou atualiza uma saída existente.  

O nome da saída pode ser especificado no arquivo .json ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do arquivo.

Se você especificar uma saída que já existe e não especificar o parâmetro -Force, o cmdlet perguntará se deseja ou não substituir a saída existente.

Se você especificar o parâmetro -Force e especificar um nome de saída existente, a saída será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura do arquivo JSON e o conteúdo, consulte a seção [Criar Saída (Stream Analytics do Azure)][msdn-rest-api-create-stream-analytics-output] da [Biblioteca de referência da API REST de gerenciamento do Stream Analytics][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Esse comando do PowerShell cria uma nova saída chamada "output" no trabalho StreamingJob. Se uma saída existente com esse nome já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Esse comando do PowerShell substitui a definição de “output" no trabalho StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Cria uma nova transformação dentro de um trabalho de Stream Analytics ou atualiza uma transformação existente.

O nome da transformação pode ser especificado no arquivo .json ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do arquivo.

Se você especificar uma transformação que já existe e não especificar o parâmetro -Force, o cmdlet perguntará se deseja ou não substituir a transformação existente.

Se você especificar o parâmetro -Force e especificar um nome de transformação existente, a transformação será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura do arquivo JSON e o conteúdo, consulte a seção [Criar transformação (Stream Analytics do Azure)][msdn-rest-api-create-stream-analytics-transformation] da [Biblioteca de referência da API REST de gerenciamento do Stream Analytics][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Esse comando do PowerShell cria uma nova transformação chamada StreamingJobTransform no trabalho StreamingJob. Se uma transformação existente com esse nome já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 Esse comando do PowerShell substitui a definição de StreamingJobTransform no trabalho StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Exclui de maneira assíncrona uma entrada específica de um trabalho do Stream Analytics no Microsoft Azure.  
Se você especificar o parâmetro -Force, a entrada será excluída sem confirmação.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Esse comando do PowerShell remove a entrada EventStream do trabalho StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Exclui de maneira assíncrona um trabalho específico do Stream Analytics no Microsoft Azure.  
Se você especificar o parâmetro -Force, o trabalho será excluído sem confirmação.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Esse comando do PowerShell remove o trabalho StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Exclui de maneira assíncrona uma saída específica de um trabalho do Stream Analytics no Microsoft Azure.  
Se você especificar o parâmetro -Force, a saída será excluída sem confirmação.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Esse comando do PowerShell remove a saída Output do trabalho StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Implanta de maneira assíncrona e inicia um trabalho do Stream Analytics no Microsoft Azure.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Esse comando do PowerShell inicia o trabalho StreamingJob com uma hora personalizada de início de saída definida como 12 de dezembro de 2012, 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Interrompe de maneira assíncrona um trabalho do Stream Analytics para que não seja executado no Microsoft Azure e desaloca os recursos que estavam sendo usados. A definição de trabalho e os metadados permanecerão disponíveis dentro da sua assinatura por meio de APIs de gerenciamento e do Portal do Azure, de modo que o trabalho possa ser editado e reiniciado. Você não será cobrado por um trabalho no estado Interrompido.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Esse comando do PowerShell para o trabalho StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Testa a capacidade do Stream Analytics de se conectar a uma entrada especificada.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Esse comando do PowerShell testa o status de conexão da entrada EntryStream no StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Testa a capacidade do Stream Analytics de se conectar a uma saída especificada.

**Exemplo 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Esse comando do PowerShell testa o status de conexão da entrada Output no StreamingJob.  

## <a name="get-support"></a>Obtenha suporte
Para obter mais assistência, veja nossa [página de perguntas e respostas da Microsoft do Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](/rest/api/streamanalytics/)

[msdn-switch-azuremode]: /previous-versions/azure/dn722470(v=azure.100)
[powershell-install]: /powershell/azure/
[msdn-rest-api-create-stream-analytics-job]: ./stream-analytics-quick-create-portal.md
[msdn-rest-api-create-stream-analytics-input]: ./stream-analytics-define-inputs.md
[msdn-rest-api-create-stream-analytics-output]: ./stream-analytics-define-outputs.md
[msdn-rest-api-create-stream-analytics-transformation]: /cli/azure/ext/stream-analytics/stream-analytics/transformation

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/