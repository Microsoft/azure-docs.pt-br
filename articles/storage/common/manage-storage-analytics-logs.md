---
title: Habilitar e gerenciar logs de Análise de Armazenamento do Azure (clássico) | Microsoft Docs
description: Saiba como monitorar uma conta de armazenamento no Azure usando Análise de Armazenamento do Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: bc6632b55ba8fd90317a8b5046a3e84d863bf0ef
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221517"
---
# <a name="enable-and-manage-azure-storage-analytics-logs-classic"></a>Habilitar e gerenciar logs de Análise de Armazenamento do Azure (clássico)

[Análise de armazenamento do Azure](storage-analytics.md) fornece logs para BLOBs, filas e tabelas. Você pode usar o [portal do Azure](https://portal.azure.com) para configurar logs são registrados para sua conta. Este artigo mostra como habilitar e gerenciar logs. Para saber como habilitar as métricas, consulte [habilitar e gerenciar métricas de análise de armazenamento do Azure (clássico)](storage-monitor-storage-account.md).  Há custos associados ao exame e armazenamento de dados de monitoramento no portal do Azure. Para obter mais informações, consulte [Análise de Armazenamento](storage-analytics.md).

> [!NOTE]
> Recomendamos que você use os logs de armazenamento do Azure em Azure Monitor em vez de Análise de Armazenamento logs. Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Essa visualização habilita logs para BLOBs (que inclui Azure Data Lake Storage Gen2), arquivos, filas e tabelas. Para saber mais, consulte qualquer um dos seguintes artigos:
>
> - [Monitorando o armazenamento de BLOBs do Azure](../blobs/monitor-blob-storage.md)
> - [Monitorando arquivos do Azure](../files/storage-files-monitoring.md)
> - [Monitoramento do armazenamento de filas do Azure](../queues/monitor-queue-storage.md)
> - [Monitorando o armazenamento de tabelas do Azure](../tables/monitor-table-storage.md)

Para um guia aprofundado sobre como usar a Análise de Armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao Armazenamento do Azure, consulte [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

<a id="configure-logging"></a>

## <a name="enable-logs"></a>Habilitar logs

Você pode instruir o Armazenamento do Azure a salvar logs de diagnóstico para ler, gravar e excluir solicitações para os serviços de fila, blob e tabela. A política de retenção de dados que você define também se aplica a esses logs.

> [!NOTE]
> Atualmente, os arquivos do Azure dão suporte a Análise de Armazenamento métricas, mas não oferece suporte ao registro em log de Análise de Armazenamento.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://portal.azure.com), selecione **Contas de armazenamento** e o nome da conta de armazenamento para abrir a folha de conta de armazenamento.

2. Selecione **configurações de diagnóstico (clássico)** na seção **monitoramento (clássico)** da folha do menu.

    ![Item do menu de diagnóstico em MONITORAMENTO no portal do Azure.](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Verifique se **Status** está definido como **Ativado** e selecione os **Serviços** para os quais deseja habilitar os logs.

   > [!div class="mx-imgBorder"]
   > ![Configure os logs no portal do Azure.](./media/manage-storage-analytics-logs/enable-diagnostics.png)    


4. Verifique se a caixa de seleção **excluir dados** está marcada.  Em seguida, defina o número de dias que você gostaria que os dados de log fossem retidos movendo o controle deslizante sob a caixa de seleção ou modificando diretamente o valor que aparece na caixa de texto ao lado do controle deslizante. O padrão para novas contas de armazenamento é de sete dias. Se não desejar definir uma política de retenção, digite zero. Se não houver nenhuma política de retenção, cabe a você excluir os dados de log.

   > [!WARNING]
   > Os logs são armazenados como dados em sua conta. os dados de log podem ser acumulados em sua conta ao longo do tempo, o que pode aumentar o custo de armazenamento. Se você precisar de dados de log por apenas um pequeno período de tempo, poderá reduzir os custos modificando a política de retenção de dados. Dados de log obsoletos (dados anteriores à sua política de retenção) são excluídos pelo sistema. É recomendável definir uma política de retenção com base em quanto tempo você deseja manter os dados de log para sua conta. Consulte [Cobrança de métricas de armazenamento](storage-analytics-metrics.md#billing-on-storage-metrics) para obter mais informações.

4. Clique em **Save** (Salvar).

   Os logs de diagnóstico são salvos em um contêiner de blob denominado *$logs* em sua conta de armazenamento. Você pode exibir os dados de log usando um Gerenciador de armazenamento como o [Gerenciador de armazenamento do Microsoft Azure](https://storageexplorer.com)ou programaticamente usando a biblioteca de cliente de armazenamento ou o PowerShell.

   Para obter informações sobre como acessar o contêiner $logs, consulte [Log de análise de armazenamento](storage-analytics-logging.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Abra uma janela de comando do Windows PowerShell.

2. Entre na sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

   ```powershell
   Connect-AzAccount
   ```

3. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua o valor de espaço reservado `<subscription-id>` pela ID da sua assinatura.

5. Obtenha o contexto da conta de armazenamento que define a conta de armazenamento que você deseja usar.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Substitua o valor do espaço reservado `<resource-group-name>` pelo nome do grupo de recursos.

   * Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento. 

6. Use o **set-AzStorageServiceLoggingProperty** para alterar as configurações de log atuais. Os cmdlets que controlam o Registro em Log do Armazenamento usam um parâmetro **LoggingOperations** que é uma cadeia de caracteres contendo uma lista de tipos de solicitação separados por vírgulas para o log. Os três tipos possíveis de solicitação são **leitura**, **gravação** e **exclusão**. Para desativar o registro em log, use o valor **none** no parâmetro **LoggingOperations**.  

   O seguinte comando ativa o registro em log para solicitações de leitura, gravação e exclusão no serviço de Fila em sua conta de armazenamento padrão com a retenção definida como cinco dias:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5 -Context $ctx
   ```  

   > [!WARNING]
   > Os logs são armazenados como dados em sua conta. os dados de log podem ser acumulados em sua conta ao longo do tempo, o que pode aumentar o custo de armazenamento. Se você precisar de dados de log por apenas um pequeno período de tempo, poderá reduzir os custos modificando a política de retenção de dados. Dados de log obsoletos (dados anteriores à sua política de retenção) são excluídos pelo sistema. É recomendável definir uma política de retenção com base em quanto tempo você deseja manter os dados de log para sua conta. Consulte [Cobrança de métricas de armazenamento](storage-analytics-metrics.md#billing-on-storage-metrics) para obter mais informações.
   
   O seguinte comando desativa o registro em log para o serviço de tabela em sua conta de armazenamento padrão:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none -Context $ctx 
   ```  

   Para saber mais sobre como configurar os cmdlets do Azure PowerShell para funcionar com sua assinatura do Azure e como escolher a conta de armazenamento padrão para usar, confira: [Como instalar e configurar o Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
``` 

---

<a id="modify-retention-policy"></a>

## <a name="modify-log-data-retention-period"></a>Modificar período de retenção de dados de log

Os dados de log podem ser acumulados em sua conta ao longo do tempo, o que pode aumentar o custo de armazenamento. Se você precisar de dados de log por apenas um pequeno período de tempo, poderá reduzir os custos modificando o período de retenção de dados de log. Por exemplo, se você precisar de logs por apenas três dias, defina o período de retenção de dados de log para um valor de `3` . Dessa forma, os logs serão excluídos automaticamente da sua conta após 3 dias. Esta seção mostra como exibir o período de retenção de dados de log atual e, em seguida, atualizar esse período se isso for o que você deseja fazer.

> [!NOTE]
> Essas etapas se aplicam somente a contas que não têm a configuração de **namespace hierárquico** habilitada neles. Se você habilitou essa configuração em sua conta, a configuração para dias de retenção ainda não tem suporte. Em vez disso, você precisará excluir os logs manualmente usando qualquer ferramenta com suporte, como Gerenciador de Armazenamento do Azure, REST ou um SDK. Para localizar esses logs em sua conta de armazenamento, consulte [como os logs são armazenados](storage-analytics-logging.md#how-logs-are-stored).

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://portal.azure.com), selecione **Contas de armazenamento** e o nome da conta de armazenamento para abrir a folha de conta de armazenamento.
2. Selecione **configurações de diagnóstico (clássico)** na seção **monitoramento (clássico)** da folha do menu.

    ![Item de menu diagnóstico em monitoramento no portal do Azure](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Verifique se a caixa de seleção **excluir dados** está marcada.  Em seguida, defina o número de dias que você gostaria que os dados de log fossem retidos movendo o controle deslizante sob a caixa de seleção ou modificando diretamente o valor que aparece na caixa de texto ao lado do controle deslizante.

   > [!div class="mx-imgBorder"]
   > ![Modificar o período de retenção no portal do Azure](./media/manage-storage-analytics-logs/modify-retention-period.png)

   O número padrão de dias para novas contas de armazenamento é de sete dias. Se não desejar definir uma política de retenção, digite zero. Se não houver nenhuma política de retenção, cabe a você excluir os dados de monitoramento.
   
4. Clique em **Save** (Salvar).

   Os logs de diagnóstico são salvos em um contêiner de blob denominado *$logs* em sua conta de armazenamento. Você pode exibir os dados de log usando um Gerenciador de armazenamento como o [Gerenciador de armazenamento do Microsoft Azure](https://storageexplorer.com)ou programaticamente usando a biblioteca de cliente de armazenamento ou o PowerShell.

   Para obter informações sobre como acessar o contêiner $logs, consulte [Log de análise de armazenamento](storage-analytics-logging.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Abra uma janela de comando do Windows PowerShell.

2. Entre na sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

   ```powershell
   Connect-AzAccount
   ```

3. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua o valor de espaço reservado `<subscription-id>` pela ID da sua assinatura.

5. Obtenha o contexto da conta de armazenamento que define a conta de armazenamento.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Substitua o valor do espaço reservado `<resource-group-name>` pelo nome do grupo de recursos.

   * Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento. 

6. Use [Get-AzStorageServiceLoggingProperty](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageserviceloggingproperty) para exibir a política de retenção de log atual. O exemplo a seguir imprime no console o período de retenção para serviços de armazenamento de BLOB e fila.

   ```powershell
   Get-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -Context $ctx
   ```  

   Na saída do console, o período de retenção é exibido abaixo do `RetentionDays` título da coluna.

   > [!div class="mx-imgBorder"]
   > ![Política de retenção na saída do PowerShell](./media/manage-storage-analytics-logs/retention-period-powershell.png)

7. Use o [set-AzStorageServiceLoggingProperty](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageserviceloggingproperty) para alterar o período de retenção. O exemplo a seguir altera o período de retenção para 4 dias.  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -RetentionDays 4 -Context $ctx
   ```  

   Para saber mais sobre como configurar os cmdlets do Azure PowerShell para funcionar com sua assinatura do Azure e como escolher a conta de armazenamento padrão para usar, confira: [Como instalar e configurar o Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

O exemplo a seguir imprime no console o período de retenção para serviços de armazenamento de BLOB e fila.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ViewRetentionPeriod":::

O exemplo a seguir altera o período de retenção para 4 dias. 

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ModifyRetentionPeriod":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

O exemplo a seguir imprime no console o período de retenção para serviços de armazenamento de BLOB e fila.

```csharp
var storageAccount = CloudStorageAccount.Parse(connectionString);

var blobClient = storageAccount.CreateCloudBlobClient();
var queueClient = storageAccount.CreateCloudQueueClient();

var blobserviceProperties = blobClient.GetServiceProperties();
var queueserviceProperties = queueClient.GetServiceProperties();

Console.WriteLine("Retention period for logs from the blob service is: " +
   blobserviceProperties.Logging.RetentionDays.ToString());

Console.WriteLine("Retention period for logs from the queue service is: " +
   queueserviceProperties.Logging.RetentionDays.ToString());
```

O exemplo a seguir altera o período de retenção dos logs para os serviços de armazenamento de BLOB e fila para 4 dias. 

```csharp

blobserviceProperties.Logging.RetentionDays = 4;
queueserviceProperties.Logging.RetentionDays = 4;

blobClient.SetServiceProperties(blobserviceProperties);
queueClient.SetServiceProperties(queueserviceProperties);  
``` 

---

### <a name="verify-that-log-data-is-being-deleted"></a>Verificar se os dados de log estão sendo excluídos

Você pode verificar se os logs estão sendo excluídos exibindo o conteúdo do `$logs` contêiner da sua conta de armazenamento. A imagem a seguir mostra o conteúdo de uma pasta no `$logs` contêiner. A pasta corresponde a janeiro de 2021 e cada pasta contém logs para um dia. Se o dia hoje foi de 29 de janeiro de 2021 e sua política de retenção estiver definida como apenas um dia, essa pasta deverá conter logs para apenas um dia.

> [!div class="mx-imgBorder"]
> ![Lista de pastas de log no portal do Azure](./media/manage-storage-analytics-logs/verify-and-delete-logs.png)

<a id="download-storage-logging-log-data"></a>

## <a name="view-log-data"></a>Exibir dados de log

 Para exibir e analisar seus dados de log, baixe os blobs que contêm os dados de log nos quais você está interessado para um computador local. Muitas ferramentas de navegação de armazenamento permitem baixar blobs de sua conta de armazenamento. Você também pode usar a Ferramenta de Cópia do Azure da linha de comando fornecida pela equipe do Armazenamento do Azure [AzCopy](storage-use-azcopy-v10.md) para baixar os dados de log.  
 
>[!NOTE]
> O contêiner `$logs` não é integrado à Grade de Eventos, portanto, você não receberá notificações quando os arquivos de log forem gravados. 

 Para baixar os dados de log nos quais você está interessado e para evitar o download dos mesmos dados de log mais de uma vez:  

-   Use a convenção de nomenclatura de data e hora para blobs contendo dados de log a fim de acompanhar quais blobs você já baixou para análise e não baixar novamente os mesmos dados mais de uma vez.  

-   Use os metadados nos blobs que contêm dados de log para identificar o período específico para o qual o blob contém dados de log a fim de identificar o blob exato que você precisa baixar.  

Para começar a usar o AzCopy, veja [Introdução ao AzCopy](storage-use-azcopy-v10.md) 

O exemplo a seguir mostra como você pode baixar os dados de log para o serviço fila para as horas começando às 9h, às 10h e às 11h de 20 de maio de 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Para saber mais sobre como baixar arquivos específicos, consulte [baixar BLOBs do armazenamento de BLOBs do Azure usando AzCopy V10](./storage-use-azcopy-blobs-download.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Após baixar seus dados de log, veja as entradas de log nos arquivos. Esses arquivos de log usam um formato de texto delimitado que muitas ferramentas de leitura de log podem analisar (para obter mais informações, consulte o guia [monitoramento, diagnóstico e solução de problemas armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)). Ferramentas diferentes têm recursos diferentes para formatação, filtragem, classificação e pesquisa de anúncios para o conteúdo de seus arquivos de log. Para obter mais informações sobre o formato e o conteúdo do arquivo de log do Registro em Log do Armazenamento, consulte [Formato do Log de Análise do Armazenamento](/rest/api/storageservices/storage-analytics-log-format) e [Operações Registradas em Log na Análise de Armazenamento e Mensagem de Status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre Análise de Armazenamento, consulte [análise de armazenamento](storage-analytics.md) para análise de armazenamento.
* [Configure análise de armazenamento métricas](storage-monitor-storage-account.md).
* Para obter mais informações sobre como usar uma linguagem .NET para configurar o Log de Armazenamento, consulte [Referência da Biblioteca do Clientes do Armazenamento](/previous-versions/azure/dn261237(v=azure.100)). 
* Para obter informações gerais sobre a configuração do Registro em Log do Armazenamento usando a API REST, veja [Habilitar e configurar a Análise do Armazenamento](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).
* Saiba mais sobre o formato dos logs de Análise de Armazenamento. Consulte [análise de armazenamento formato de log](/rest/api/storageservices/storage-analytics-log-format).