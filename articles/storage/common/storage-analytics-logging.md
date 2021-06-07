---
title: Log da Análise de Armazenamento do Azure
description: Use Análise de Armazenamento para registrar em log detalhes sobre as solicitações de armazenamento do Azure. Veja quais solicitações são registradas, como os logs são armazenados, como habilitar o log de armazenamento e muito mais.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: a5d1d6af68fcbd6a5822b2652ee79c464d02241f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200775"
---
# <a name="azure-storage-analytics-logging"></a>Log da Análise de Armazenamento do Azure

A análise de armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço. Isso significa que a maioria das solicitações resultará em um registro de log, mas a integridade e a pontualidade dos logs de Análise de Armazenamento não são garantidas. 

> [!NOTE]
> Recomendamos que você use os logs de armazenamento do Azure em Azure Monitor em vez de Análise de Armazenamento logs. Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Essa visualização habilita logs para BLOBs (que inclui Azure Data Lake Storage Gen2), arquivos, filas e tabelas. Para saber mais, consulte qualquer um dos seguintes artigos:
>
> - [Monitorando o armazenamento de BLOBs do Azure](../blobs/monitor-blob-storage.md)
> - [Monitorando arquivos do Azure](../files/storage-files-monitoring.md)
> - [Monitoramento do armazenamento de filas do Azure](../queues/monitor-queue-storage.md)
> - [Monitorando o armazenamento de tabelas do Azure](../tables/monitor-table-storage.md)

 O log da Análise de Armazenamento não está habilitado por padrão na conta de armazenamento. Você pode habilitá-lo no [portal do Azure](https://portal.azure.com/) ou usando o PowerShell ou CLI do Azure. Para obter orientações passo a passo, consulte [habilitar e gerenciar logs de análise de armazenamento do Azure (clássico)](manage-storage-analytics-logs.md). 

Você também pode habilitar logs de Análise de Armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use as operações [Obter Propriedades do Serviço Blob](/rest/api/storageservices/Blob-Service-REST-API), [Obter Propriedades do Serviço Fila](/rest/api/storageservices/Get-Queue-Service-Properties) e [Obter Propriedades do Serviço Tabela](/rest/api/storageservices/Get-Table-Service-Properties) para habilitar a Análise de Armazenamento para cada serviço. Para ver um exemplo que permite logs de Análise de Armazenamento usando o .NET, consulte [Habilitar logs](manage-storage-analytics-logs.md)

 As entradas de log são criadas somente se há solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividades em seu ponto de extremidade Blob, mas não em seus pontos de extremidade Tabela ou Fila, somente os logs pertencentes ao serviço Blob são criados.

> [!NOTE]
>  No momento, o log da Análise de Armazenamento está disponível apenas para os serviços de Blob, Fila e Tabela. O log de Análise de Armazenamento também está disponível para contas de [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) de desempenho premium. No entanto, ele não está disponível para contas v2 de uso geral com desempenho premium.

## <a name="requests-logged-in-logging"></a>Solicitações registradas em log
### <a name="logging-authenticated-requests"></a>Solicitações de registro em log autenticadas

 Os seguintes tipos de solicitações autenticadas são registrados:

- Solicitações bem-sucedidas
- Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros
- Solicitações que usam uma Assinatura de Acesso Compartilhado (SAS) ou OAuth, incluindo solicitações bem-sucedidas e com falha
- Solicitações de dados de análise

  As solicitações feitas pela própria análise de armazenamento, como criação de log ou exclusão, não estão conectadas. Uma lista completa dos dados registrados está documentada nos tópicos [Storage Analytics Logged Operations and Status Messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Mensagens de operações e status registradas do Storage Analytics) e [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format) (Formato do log do Storage Analytics).

### <a name="logging-anonymous-requests"></a>Registro em log de solicitações anônimas

 Os seguintes tipos de solicitações anônimas são registrados:

- Solicitações bem-sucedidas
- Erros do servidor
- Erros de tempo limite para o cliente e o servidor
- Solicitações GET com falha com o código de erro 304 (Não Modificado)

  Todas as outras solicitações anônimas com falha não estão conectadas. Uma lista completa dos dados registrados está documentada nos tópicos [Storage Analytics Logged Operations and Status Messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Mensagens de operações e status registradas do Storage Analytics) e [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format) (Formato do log do Storage Analytics).

## <a name="how-logs-are-stored"></a>Como os logs são armazenados

Todos os logs são armazenados em blobs de blocos em um contêiner denominado `$logs`, que é criado automaticamente quando a Análise de Armazenamento é habilitada para uma conta de armazenamento. O contêiner `$logs` está localizado no namespace de blob da conta de armazenamento, por exemplo: `http://<accountname>.blob.core.windows.net/$logs`. Este contêiner não pode ser excluído quando a análise de armazenamento tiver sido habilitada, embora seu conteúdo possa ser excluído. Se usar a ferramenta de navegação de armazenamento para navegar diretamente até o contêiner, você verá todos os blobs que contêm os dados de registro em log.

> [!NOTE]
>  O contêiner `$logs` não é exibido quando uma operação de listagem de contêiner é executada, como o método Listar Contêineres. Ele deve ser acessado diretamente. Por exemplo, use o método Listar Blobs para acessar os blobs no contêiner `$logs`.

Como as solicitações são registradas, a análise de armazenamento carrega resultados intermediários como blocos. Periodicamente, a análise de armazenamento confirmará esses blocos e os disponibilizará como um blob. Pode levar até uma hora para que os dados de log sejam mostrados no contêiner **$logs** devido à frequência com que o serviço de armazenamento libera os gravadores de log. Podem existir registros duplicados para os logs criados na mesma hora. Você pode determinar se um registro é uma duplicata verificando **RequestId** e o número da **Operação**.

Se tiver um grande volume de dados de log com vários arquivos para cada hora, você poderá usar os metadados de blob para determinar quais dados o log contém examinando os campos de metadados do blob. Isso também é útil porque, às vezes, pode haver um atraso enquanto os dados são gravados nos arquivos de log: os metadados do blob fornecem uma indicação mais precisa do conteúdo do blob do que o nome do blob.

A maioria das ferramentas de navegação de armazenamento permite exibir os metadados de blobs. Além disso, você pode ler essas informações usando o PowerShell ou de forma programática. O seguinte snippet do PowerShell é um exemplo de filtragem da lista de blobs de log pelo nome, para especificar uma hora, e por metadados, para identificar apenas os logs que contêm operações de **gravação**.  

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'blob/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Para obter informações sobre como listar blobs de forma programática, consulte [Enumeração de Recursos de Blob](/rest/api/storageservices/Enumerating-Blob-Resources) e [Definição e Recuperação de Propriedades e Metadados para Recursos de Blob](/rest/api/storageservices/Setting-and-Retrieving-Properties-and-Metadata-for-Blob-Resources).  

### <a name="log-naming-conventions"></a>Convenções de nomenclatura de log

 Cada log será gravado no formato a seguir:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 A tabela a seguir descreve cada atributo no nome do log:

|Atributo|Descrição|
|---------------|-----------------|
|`<service-name>`|O nome do serviço de armazenamento. Por exemplo: `blob`, `table` ou `queue`|
|`YYYY`|O ano de quatro dígitos do log. Por exemplo: `2011`|
|`MM`|O mês de dois dígitos do log. Por exemplo: `07`|
|`DD`|O dia de dois dígitos do log. Por exemplo: `31`|
|`hh`|A hora de dois dígitos que indica a hora inicial dos logs, no formato de 24 horas UTC. Por exemplo: `18`|
|`mm`|O número de dois dígitos que indica o minuto inicial dos logs. **Observação:**  Esse valor não tem suporte na versão atual da Análise de Armazenamento e seu valor será sempre `00`.|
|`<counter>`|Um contador baseado em zero com seis dígitos que indica o número de blobs de log gerado para o serviço de armazenamento em um período de uma hora. Esse contador começa em `000000`. Por exemplo: `000001`|

 O nome a seguir é um exemplo de nome de log completo, que combina os exemplos anteriores:

 `blob/2011/07/31/1800/000001.log`

 O URI a seguir é um exemplo de URI que pode ser usado para acessar o log anterior:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Quando uma solicitação de armazenamento estiver conectada, o nome do log resultante se correlaciona com a hora quando concluir a operação solicitada. Por exemplo, se uma solicitação GetBlob foi concluída às 18h30, em 31/07/2011, o log seria gravado com o seguinte prefixo: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadados de log

 Todos os blobs de log são armazenados com metadados que podem ser usados para identificar os dados de log que contém o blob. A tabela a seguir descreve cada atributo de metadados:

|Atributo|Descrição|
|---------------|-----------------|
|`LogType`|Descreve se o log contém informações referentes a operações de ler, gravar ou de exclusão. Esse valor pode incluir um tipo ou uma combinação dos três, separados por vírgulas.<br /><br /> Exemplo 1: `write`<br /><br /> Exemplo 2: `read,write`<br /><br /> Exemplo 3: `read,write,delete`|
|`StartTime`|A hora da entrada mais antiga do log, na forma de `YYYY-MM-DDThh:mm:ssZ`. Por exemplo: `2011-07-31T18:21:46Z`|
|`EndTime`|A hora da entrada mais recente do log, na forma de `YYYY-MM-DDThh:mm:ssZ`. Por exemplo: `2011-07-31T18:22:09Z`|
|`LogVersion`|A versão do formato do log.|

 A lista a seguir exibe exemplos de metadados que usam os exemplos anteriores:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`


## <a name="next-steps"></a>Próximas etapas

* [Habilitar e gerenciar logs de Análise de Armazenamento do Azure (clássico)](manage-storage-analytics-logs.md)
* [Formato de Log de análise de armazenamento](/rest/api/storageservices/storage-analytics-log-format)
* [Mensagens de operações e status registradas de análise de armazenamento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Métricas da Análise de Armazenamento (clássico)](storage-analytics-metrics.md)
