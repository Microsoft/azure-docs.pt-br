---
title: Log de diagnósticos no Azure Cosmos DB
description: Saiba mais sobre as diferentes maneiras de registrar em log e monitorar dados armazenados no Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 41fa5a859e738c2bb70e4885aa856f247e922492
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448992"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Log de diagnósticos no Azure Cosmos DB 

Depois de começar a usar um ou mais bancos de dados do Azure Cosmos DB, talvez você deseje monitorar como e quando os bancos de dados são acessados. Este artigo fornece uma visão geral dos logs disponíveis na plataforma do Azure. Você aprenderá a habilitar o log de diagnósticos para fins para enviar logs de monitoramento [armazenamento do Azure](https://azure.microsoft.com/services/storage/), como transmitir logs para [Hubs de eventos](https://azure.microsoft.com/services/event-hubs/)e como exportar logs para [registraemlogdoAzureMonitor](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Logs disponíveis no Azure

Antes falarmos sobre como monitorar a conta do Azure Cosmos DB, vamos esclarecer alguns aspectos sobre monitoramento e registro em log. Há diferentes tipos de logs na plataforma do Azure. Há [Logs de Atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [Logs de Diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [Métricas do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), eventos, monitoramento de pulsação, logs de operações etc. Há uma grande quantidade de logs. Você pode ver a lista completa de logs no [registra em log do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) no portal do Azure. 

A imagem a seguir mostra o tipo diferente de logs do Azure que estão disponíveis:

![Diferentes tipos de logs do Azure](./media/logging/azurelogging.png)

Na imagem, os **Recursos de computação** representam os recursos do Azure para os quais você pode acessar o sistema operacional convidado da Microsoft. Por exemplo, Máquinas Virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Serviço de Contêiner do Azure etc. são considerados recursos de computação. Recursos de computação geram Logs de atividades, Logs de diagnóstico e Logs de aplicativos. Para saber mais, consulte o artigo [Fontes de dados de monitoramento no Azure](../azure-monitor/platform/data-sources.md).

Os **Recursos que não são de computação** são recursos nos quais você não pode acessar o sistema operacional subjacente e trabalhar diretamente com o recurso. Por exemplo, Grupos de Segurança de Rede, Aplicativos Lógicos etc. Azure Cosmos DB é um recurso que não é de computação. Você pode exibir os logs dos recursos que não são de computação no Log de Atividades ou habilitar a opção Logs de Diagnóstico no portal. Para saber mais, consulte o artigo [Fontes de dados no Azure Monitor](../azure-monitor/platform/data-sources.md).

O Log de Atividades registra as operações em um nível de assinatura para o Azure Cosmos DB. Operações como ListKeys, Write DatabaseAccounts etc. são registradas. Os Logs de Diagnóstico fornecem um registro em log mais granular e permitem que você registre DataPlaneRequests (Criar, Ler, Consultar etc.) e MongoRequests.


Neste artigo, vamos nos concentrar no Log de Atividades do Azure, nos Logs de Diagnóstico do Azure e nas métricas do Azure. Qual é a diferença entre esses três logs? 

### <a name="azure-activity-log"></a>Log de Atividades do Azure

O Log de Atividades do Azure é um log de assinatura que fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. O Log de Atividades relata eventos do plano de controle para suas assinaturas na categoria Administrativa. Você pode usar o Log de Atividades para determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) nos recursos em sua assinatura. Também é possível compreender o status da operação e outras propriedades relevantes. 

O Log de Atividades difere dos Logs de Diagnóstico. O Log de Atividades fornece dados sobre as operações em um recurso externo (o _plano de controle_). No contexto do Azure Cosmos DB, as operações do plano de controle incluem criar contêiner, listar chaves, excluir chaves, listar banco de dados e assim por diante. Os Logs de Diagnóstico são emitidos por um recurso e fornecem informações sobre a operação do recurso (o _plano de dados_). Alguns exemplos das operações do plano de dados no log de diagnóstico são Excluir, Inserir e ReadFeed.

Os Logs de Atividades (operações do plano de controle) podem ser muito mais completos e incluir endereço de email completo do chamador, endereço IP do chamador, nome do recurso, nome da operação, TenantId etc. O Log de Atividades contém várias [categorias](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) de dados. Para obter todos os detalhes sobre o esquema dessas categorias, veja o [esquema de eventos de Log de Atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). No entanto, Logs de Diagnóstico podem ser restritivos, pois dados pessoais geralmente são retirados deles. Você pode ter o endereço IP do chamador, mas o último octant é removido.

### <a name="azure-metrics"></a>Métricas do Azure

As [Métricas do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) têm o tipo de dados de telemetria do Azure mais importantes (também chamadas de _contadores de desempenho_) emitidos pela maioria dos recursos do Azure. As métricas permitem que você exiba informações sobre a taxa de transferência, armazenamento, consistência, disponibilidade e a latência de seus recursos do Azure Cosmos DB. Para obter mais informações, consulte [Como monitorar e depurar com métricas no Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Logs de Diagnóstico do Azure

Os Logs de Diagnóstico do Azure são emitidos por um recurso e fornecem dados avançados e frequentes sobre a operação do recurso. Esses logs são capturados por solicitação. O conteúdo desses logs varia de acordo com o tipo de recurso. Os logs de diagnóstico no nível do recurso também diferem dos logs de diagnóstico no nível do sistema operacional convidado. Os logs de diagnóstico do sistema operacional convidado são coletados por um agente em execução dentro de uma máquina virtual ou outro tipo de recurso com suporte. Os logs de diagnóstico no nível de recursos não exigem nenhum dado específico de recurso de captura e agente da própria plataforma do Azure. Os logs de diagnóstico no nível de sistema operacional convidado capturam dados do sistema operacional e dos aplicativos que estão em execução em uma máquina virtual.

![Log de diagnósticos para Armazenamento, Hubs de Eventos ou logs do Azure Monitor](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>O que é registrado por Logs de Diagnóstico do Azure?

* Todas as solicitações de back-end autenticadas (TCP/REST) em todas as APIs são registradas, incluindo as solicitações com falha como resultado de permissões de acesso, erros do sistema ou solicitações inválidas. O suporte para solicitações de Graph, Cassandra e API de tabela iniciadas pelo usuário não está disponível no momento.
* Operações no próprio banco de dados, que incluem operações CRUD em todos os documentos, contêineres e bancos de dados.
* Operações em chaves de conta, que incluem a criação, a modificação ou a exclusão das chaves.
* Solicitações não autenticadas que resultam em uma resposta 401. Por exemplo, as solicitações que não têm um token de portador estão malformadas ou expiradas ou têm um token inválido.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Ativar o log no portal do Azure

Use as etapas a seguir para habilitar o log de diagnóstico no portal do Azure:

1. Faça logon no [Portal do Azure](https://portal.azure.com). 

1. Navegue até a conta do Azure Cosmos. Abra o **configurações de diagnóstico** painel e, em seguida, selecione **Adicionar configuração de diagnóstico** opção.

    ![Ativar o log de diagnósticos do Azure Cosmos DB no portal do Azure](./media/logging/turn-on-portal-logging.png)

1. No **configurações de diagnóstico** da página, preenchimento do formulário com os seguintes detalhes: 

    * **Nome**: Insira um nome para os logs a serem criados.

    * Você pode armazenar os logs para os seguintes serviços:

      * **Arquivar em uma conta de armazenamento**: Para usar essa opção, você precisa de uma conta de armazenamento existente à qual se conectar. Para criar uma nova conta de armazenamento no portal, consulte [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md) artigo. Em seguida, retorne ao painel de configurações de diagnóstico do Azure Cosmos Db no portal para selecionar sua conta de armazenamento. Pode levar alguns minutos para que as contas de armazenamento recém-criadas sejam exibidas no menu suspenso.

      * **Transmitir para um hub de eventos**: Para usar essa opção, é necessário ter um namespace dos Hubs de Eventos e um hub de eventos ao qual se conectar. Para criar um namespace do Hubs de Eventos, consulte [Criar um namespace dos Hubs de Eventos e um hub de eventos usando o portal do Azure](../event-hubs/event-hubs-create.md). Em seguida, retorne a esta página no portal para selecionar o nome de namespace e a política do Hub de eventos.

      * **Enviar para o Log Analytics**: Para usar essa opção, use um espaço de trabalho existente ou crie um espaço de trabalho do Log Analytics seguindo as etapas para [Criar um espaço de trabalho](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) no portal. 

   * Você pode registrar os dados a seguir:

      * **DataPlaneRequests**: Selecione esta opção para registrar solicitações de back-end para todas as APIs que inclui contas SQL, Graph, MongoDB, Cassandra e API de tabela no Azure Cosmos DB. Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente depois que o período de retenção expira. Os seguintes dados JSON são um exemplo de saída dos detalhes registrados usando DataPlaneRequests. Propriedades de chave observar são: Requestcharge, statusCode, clientIPaddress e partitionID:

       ```
       { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
       ```

      * **MongoRequests**: Selecione esta opção para registrar solicitações iniciadas pelo usuário do front-end para atender às solicitações à API do Azure Cosmos DB para MongoDB. Solicitações de MongoDB aparecerá no MongoRequests, bem como DataPlaneRequests. Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente depois que o período de retenção expira. Os seguintes dados JSON são um exemplo de saída dos detalhes registrados usando MongoRequests. Propriedades de chave observar são: Requestcharge, opCode:

       ```
       { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
       ```

      * **QueryRuntimeStatistics**: Selecione esta opção para registrar o texto da consulta que foi executado.  Os seguintes dados JSON são um exemplo de saída dos detalhes registrados usando QueryRuntimeStatistics:

       ```
       { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
       ```

      * **Solicitações de Métricas**: Selecione essa opção para armazenar dados detalhados em [métricas do Azure](../azure-monitor/platform/metrics-supported.md). Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente depois que o período de retenção expira.

3. Clique em **Salvar**.

    Se você receber um erro que diga "Falha ao atualizar o diagnóstico para \<nome do workspace&gt;. Para a assinatura \<id da assinatura> não está registrado para usar o microsoft.insights", siga as instruções de [Solucionar problemas de diagnóstico do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) para registrar a conta e repetir esse procedimento.

    Se você quiser alterar como os seus logs de diagnóstico são salvos em qualquer ponto no futuro, retorne a esta página para modificar as configurações de log de diagnóstico para sua conta.

## <a name="turn-on-logging-by-using-azure-cli"></a>Ativar o registro em log usando a CLI do Azure

Para habilitar as métricas e o log de diagnósticos usando a CLI do Azure, use os seguintes comandos:

- Para habilitar o armazenamento dos Logs de Diagnóstico em uma conta de armazenamento, use este comando:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   A `resource` é o nome da conta do Azure Cosmos DB. O recurso está no formato "/subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/providers/Microsoft.DocumentDB/databaseAccounts/ < Azure_Cosmos_account_name >" o `storage-account` é o nome da conta de armazenamento para que você deseja enviar os logs. Você pode registrar outros logs ao atualizar os valores de parâmetro de categoria para "MongoRequests" ou "DataPlaneRequests". 

- Para habilitar o streaming dos Logs de Diagnóstico para um hub de eventos, use este comando:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   A `resource` é o nome da conta do Azure Cosmos DB. O `event-hub-rule` é a ID de regra de hub de eventos. 

- Para habilitar o envio dos Logs de Diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

## <a name="turn-on-logging-by-using-powershell"></a>Ativar o registro em log usando o PowerShell

Para ativar o log de diagnósticos usando o PowerShell, você precisa do Azure PowerShell com uma versão mínima de 1.0.1.

Para instalar o Azure PowerShell e associá-lo à sua assinatura do Azure, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

Se já tiver instalado o Azure PowerShell e não souber a versão, no console do PowerShell, digite `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Conectar-se a suas assinaturas
Inicie uma sessão do PowerShell do Azure e entre em sua conta do Azure com o seguinte comando:  

```powershell
Connect-AzAccount
```

Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. O Azure PowerShell obtém todas as assinaturas que estão associadas a essa conta e, por padrão, usa a primeira.

Se você tiver mais de uma assinatura, será preciso especificar a assinatura específica que foi usada para criar seu cofre de chaves do Azure. Digite o seguinte comando para ver as assinaturas da sua conta:

```powershell
Get-AzSubscription
```

Em seguida, para especificar a assinatura associada à conta do Azure Cosmos DB que está sendo registrada em log, digite o seguinte comando:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Caso tenha mais de uma assinatura associada à sua conta, é importante especificar a assinatura que deseja usar.
>
>

Para saber mais sobre configurar o Azure PowerShell, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Criar uma nova conta de armazenamento para seus logs
Embora você possa usar uma conta de armazenamento existente para os logs, neste tutorial, criamos uma nova conta de armazenamento dedicada aos logs do Azure Cosmos DB. Para conveniência, armazenamos os detalhes da conta de armazenamento em uma variável denominada **sa**.

Para facilidade de gerenciamento adicional, neste tutorial, usamos o mesmo grupo de recursos que contém o banco de dados do Azure Cosmos DB. Substitua os valores para os parâmetros **ContosoResourceGroup**, **contosocosmosdblogs**, e **North Central US**, conforme aplicável:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Se você decidir usar uma conta de armazenamento existente, a conta deverá usar a mesma assinatura como sua assinatura do Azure Cosmos DB. A conta de armazenamento deve também usar o modelo de implantação do Gerenciador de Recursos, em vez do modelo de implantação clássico.
>
>

### <a id="identify"></a>Identificar a conta do Azure Cosmos DB para os logs
Defina o nome da conta do Azure Cosmos DB como uma variável chamada **account**, em que **ResourceName** é o nome da conta do Azure Cosmos DB.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Habilitar o registro em log
Para habilitar o log no Azure Cosmos DB, use o cmdlet `Set-AzDiagnosticSetting` com as variáveis da nova conta de armazenamento, a conta do Azure Cosmos DB e a categoria para habilitar o registro em log. Execute o seguinte comando e defina o sinalizador **-Enabled** como **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

O resultado do comando deve ser semelhante à seguinte amostra:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

A saída do comando confirma que o registro em log está agora ativado para o seu banco de dados e as informações estão sendo salvas na sua conta de armazenamento.

Opcionalmente, você também pode definir a política de retenção para os logs, de modo que os logs mais antigos sejam automaticamente excluídos. Por exemplo, defina a política de retenção com o sinalizador **-RetentionEnabled** definido como **$true**. Defina o parâmetro **-RetentionInDays** como **90** para que os logs de mais de 90 dias sejam automaticamente excluídos.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Acessar seus logs
Logs do Azure Cosmos DB para o **DataPlaneRequests** categoria são armazenadas em do **insights-logs-dataplanerequests** contêiner na conta de armazenamento que você forneceu. 

Primeiro, crie uma variável para o nome do contêiner. A variável é usada em todo o passo a passo.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Para listar todos os blobs desse contêiner, digite:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

O resultado do comando deve ser semelhante à seguinte amostra:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Como você pode ver neste resultado, os blobs seguem uma convenção de nomenclatura: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Os valores de data e hora usam UTC.

Como a mesma conta de armazenamento pode ser usada para coletar logs de vários recursos, você pode usar a ID totalmente qualificada do recurso no nome do blob para acessar e baixar os blobs de que você precisa. Antes de fazer isso, primeiro vamos mostrar como baixar todos os blobs.

Primeiro, crie uma pasta para baixar os blobs. Por exemplo:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Em seguida, obtenha uma lista de todos os blobs:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Direcione essa lista por meio do comando `Get-AzStorageBlobContent` para baixar os blobs na pasta de destino:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Quando você executa esse segundo comando, o delimitador **/** nos nomes de blob cria uma estrutura de pastas completa na pasta de destino. Essa estrutura de pastas é usada para baixar e armazenar os blobs como arquivos.

Use caracteres curinga para baixar seletivamente os blobs. Por exemplo:

* Caso você tenha vários bancos de dados e deseje baixar apenas os logs de um banco de dados chamado**CONTOSOCOSMOSDB3**, use o comando:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Se você tiver vários grupos de recursos e quiser baixar os logs para apenas um grupo de recursos, use o comando `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Se desejar baixar todos os logs do mês de julho de 2017, use o comando `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Você pode também executar os seguintes comandos:

* Para consultar o status das configurações de diagnóstico do recurso de banco de dados, use o comando `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* Para desabilitar o registro em log da categoria **DataPlaneRequests** do recurso de conta do banco de dados, use o comando `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Os blobs que são retornados em cada uma dessas consultas são armazenados como texto e formatados como um blob JSON, conforme mostrado no código a seguir:

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Para saber mais sobre os dados em cada blob JSON, consulte [Interpretar seus logs do Azure Cosmos DB](#interpret).

## <a name="manage-your-logs"></a>Gerenciar seus logs

Os logs de diagnóstico são disponibilizados em sua conta duas horas após a hora de execução da operação do Azure Cosmos DB. Cabe a você gerenciar os logs em sua conta de armazenamento:

* Use os métodos de controle de acesso padrão do Azure para proteger seus logs ao restringir quem pode acessá-los.
* Exclua os logs que você não deseja manter em sua conta de armazenamento.
* O período de retenção para solicitações de plano de dados arquivadas em uma conta de Armazenamento é configurado no portal quando a configuração **Registrar DataPlaneRequests** é selecionada. Para alterar essa configuração, consulte [Ativar o log no portal do Azure](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Exibir logs nos logs do Azure Monitor

Se você tiver selecionado a **enviar para Log Analytics** opção quando você ativou o log de diagnóstico, diagnóstico do seu contêiner, os dados são encaminhados para os logs do Azure Monitor dentro de duas horas. Quando você examinar os logs do Azure Monitor imediatamente depois de ativar o registro em log, você não verá nenhum dado. Apenas aguarde duas horas e tente novamente. 

Antes de exibir os logs, verifique e veja se o seu espaço de trabalho do Log Analytics foi atualizado para usar a nova linguagem de consulta do Kusto. Para verificar, abra o [portal do Azure](https://portal.azure.com), selecione **espaços de trabalho do Log Analytics** na extrema esquerda, selecione o nome do espaço de trabalho conforme mostrado na próxima imagem. A página **Espaço de Trabalho do Log Analytics** é exibida:

![O Azure Monitor registra em log no portal do Azure](./media/logging/azure-portal.png)

>[!NOTE]
>Os workspaces do OMS agora são chamados de workspaces do Log Analytics.  

Se você vir a seguinte mensagem na página **Espaço de Trabalho do Log Analytics**, isso significa que não foi feito o upgrade do seu espaço de trabalho para usar a nova linguagem. Para obter mais informações sobre como fazer o upgrade para a nova linguagem de consulta, confira [Atualizar o seu espaço de trabalho do Azure Log Analytics para nova pesquisa de logs](../log-analytics/log-analytics-log-search-upgrade.md). 

![Mensagem de atualização de logs do Azure Monitor](./media/logging/upgrade-notification.png)

Para exibir seus dados de diagnóstico nos logs do Azure Monitor, abra o **pesquisa de logs** página no menu à esquerda ou o **Management** área da página, conforme mostrado na imagem a seguir:

![Opções de pesquisa de logs no portal do Azure](./media/logging/log-analytics-open-log-search.png)

Agora que você habilitou a coleta de dados, execute o seguinte exemplo de pesquisa de logs usando a nova linguagem de consulta para ver os 10 logs mais recentes `AzureDiagnostics | take 10`.

![Pesquisa de logs de exemplo para os 10 logs mais recentes](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Consultas

Aqui estão algumas consultas adicionais que você pode inserir na caixa **Pesquisa de logs** para ajudá-lo a monitorar seus contêineres do Azure Cosmos DB. Essas consultas funcionam com a [nova linguagem](../log-analytics/log-analytics-log-search-upgrade.md). 

Para saber mais sobre o significado dos dados retornados pelas pesquisas de logs, consulte [Interpretar seus logs do Azure Cosmos DB](#interpret).

* Para consultar todos os logs de diagnóstico do Azure Cosmos DB para um período especificado:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Para consultar os 10 eventos registrados em log mais recentemente:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Para consultar todas as operações, agrupadas por tipo de operação:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Para consultar todas as operações, agrupadas por **Recurso**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Para consultar toda a atividade do usuário, agrupada por recurso:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Este comando é para um log de atividades, não de um log de diagnóstico.

* Para consultar quais operações demoram mais de 3 milissegundos:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Para consultar qual agente está executando as operações:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Para consultar quando foram executadas as operações de longa execução:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Para obter mais informações sobre como usar a nova linguagem de pesquisa de Log, consulte [entender pesquisas de log nos logs do Azure Monitor](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretar seus logs

Dados de diagnóstico que são armazenados nos logs de armazenamento e o Azure Monitor do Azure usam um esquema semelhante. 

A tabela a seguir descreve o conteúdo de cada entrada de log.

| Propriedade ou campo de Armazenamento do Microsoft Azure | O Azure Monitor registra a propriedade | DESCRIÇÃO |
| --- | --- | --- |
| **time** | **TimeGenerated** | A data e hora (UTC) em que a operação ocorreu. |
| **resourceId** | **Recurso** | A conta do Azure Cosmos DB na qual os logs estão habilitados.|
| **category** | **Categoria** | Para os logs do Azure Cosmos DB, **DataPlaneRequests** é o único valor disponível. |
| **operationName** | **OperationName** | Nome da operação. Esse valor pode ser uma das seguintes operações: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed ou Upsert.   |
| **properties** | n/d | O conteúdo desse campo é descrito nas linhas a seguir. |
| **activityId** | **activityId_g** | O GUID exclusivo da operação registrada. |
| **userAgent** | **userAgent_s** | Uma cadeia de caracteres que especifica o agente do usuário cliente que executa a solicitação. O formato é {nome do agente do usuário}/{versão}.|
| **requestResourceType** | **requestResourceType_s** | O tipo do recurso acessado. Esse valor pode ser um dos seguintes tipos de recursos: Database, Container, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction ou Offer. |
| **statusCode** | **statusCode_s** | O status da resposta da operação. |
| **requestResourceId** | **ResourceId** | O resourceId referente à solicitação. O valor pode apontar para databaseRid, collectionRid ou documentRid, dependendo da operação executada.|
| **clientIpAddress** | **clientIpAddress_s** | Endereço IP do cliente. |
| **requestCharge** | **requestCharge_s** | O número de RUs usadas pela operação |
| **collectionRid** | **collectionId_s** | A ID exclusiva da coleção.|
| **duration** | **duration_s** | A duração da operação, em tiques. |
| **requestLength** | **requestLength_s** | O tamanho da solicitação, em bytes. |
| **responseLength** | **responseLength_s** | O tamanho da resposta, em bytes.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Esse valor não fica vazio quando [tokens de recurso](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) são usados para autenticação. O valor aponta para a ID de recurso do usuário. |

## <a name="next-steps"></a>Próximas etapas

- Para entender não apenas como habilitar o registro em log e também as métricas e as categorias de log com suporte nos vários serviços do Azure, leia os artigos [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Visão geral dos Logs de Diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md).
- Leia estes artigos para saber mais sobre os hubs de eventos:
   - [O que é Hub de Eventos do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Leia [Baixar métricas e logs de diagnósticos do Armazenamento do Microsoft Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Leia [entender pesquisas de log nos logs do Azure Monitor](../log-analytics/log-analytics-log-search-new.md).
