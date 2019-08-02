---
title: Ingerir dados usando a biblioteca Python do Azure Data Explorer
description: Neste artigo, você aprenderá a ingerir (carregar) dados no Azure Data Explorer usando o Python.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f109f2dd45fe90884d3947b244b3dafffd547725
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68355923"
---
# <a name="ingest-data-using-the-azure-data-explorer-python-library"></a>Ingerir dados usando a biblioteca Python do Azure Data Explorer

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. O Data Explorer do Azure fornece duas bibliotecas de cliente para Python: uma [biblioteca de ingestão](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest) e [uma biblioteca de dados](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Essas bibliotecas permitem a inclusão de dados (carga) em um cluster e dados de consulta do seu código. Neste artigo, você primeiro cria uma tabela e um mapeamento de dados em um cluster. Você, em seguida, enfileira ao cluster e valida os resultados.

Este artigo também está disponível como um [bloco de anotações do Azure](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueuedIngestSingleBlob.ipynb).

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

* [Um cluster e um banco de dados](create-cluster-database-portal.md)

* [Python](https://www.python.org/downloads/) instalado no seu computador de desenvolvimento

## <a name="install-the-data-and-ingest-libraries"></a>Instalar os dados e bibliotecas de ingestão

Instale *azure-kusto-data* e *azure-kusto-ingest*.

```
pip install azure-kusto-data
pip install azure-kusto-ingest
```

## <a name="add-import-statements-and-constants"></a>Adicionar instruções de importação e constantes

Importar classes de azure-kusto-data.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
```

Para autenticar um aplicativo, o Azure Data Explorer usa seu ID de locatário do AAD. Para encontrar seu ID de locatário, use a seguinte URL, substituindo seu domínio por *YourDomain*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Por exemplo, se o seu domínio for *contoso.com*, a URL será: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Clique nesta URL para ver os resultados; a primeira linha é a seguinte. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

A ID do locatário neste caso é `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Defina os valores para AAD_TENANT_ID, KUSTO_URI, KUSTO_INGEST_URI e KUSTO_DATABASE antes de executar esse código.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_DATABASE = "<DatabaseName>"
```

Agora, construa a cadeia de caracteres de conexão. Este exemplo usa a autenticação do dispositivo para acessar o cluster. Você também pode usar o [certificado de aplicativo AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), a [chave de aplicativo AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20) e o [usuário e a senha do AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34).

Criar a tabela de destino e o mapeamento em uma etapa posterior.

```python
KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_INGEST_URI, AAD_TENANT_ID)

KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_URI, AAD_TENANT_ID)

DESTINATION_TABLE = "StormEvents"
DESTINATION_TABLE_COLUMN_MAPPING = "StormEvents_CSV_Mapping"
```

## <a name="set-source-file-information"></a>Definir informações de arquivo de origem

Importar classes adicionais e definir constantes para o arquivo de fonte de dados. Este exemplo usa um arquivo de exemplo hospedado no armazenamento de BLOBs do Azure. O conjunto de dados de amostra **StormEvents** contém dados relacionados ao clima dos [Centros Nacionais de Informações Ambientais](https://www.ncdc.noaa.gov/stormevents/).

```python
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

CONTAINER = "samplefiles"
ACCOUNT_NAME = "kustosamplefiles"
SAS_TOKEN = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D"
FILE_PATH = "StormEvents.csv"
FILE_SIZE = 64158321    # in bytes

BLOB_PATH = "https://" + ACCOUNT_NAME + ".blob.core.windows.net/" + \
    CONTAINER + "/" + FILE_PATH + SAS_TOKEN
```

## <a name="create-a-table-on-your-cluster"></a>Criar uma tabela no cluster

Criar uma tabela que corresponde ao esquema dos dados no arquivo StormEvents.csv. Quando esse código é executado, ele retorna uma mensagem semelhante à seguinte: *Para entrar, use um navegador da Web para abrir a página https://microsoft.com/devicelogin e insira o código F3W4VWZDM para autenticar*. Siga as etapas para entrar e retorne para executar o próximo bloco de código. Blocos de código subsequente que compõem uma conexão exigem que você entre novamente.

```python
KUSTO_CLIENT = KustoClient(KCSB_DATA)
CREATE_TABLE_COMMAND = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)"

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_TABLE_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="define-ingestion-mapping"></a>Definir mapeamento de ingestão

Mapear os dados JSON de entrada para os nomes de colunas e tipos de dados usados ao criar a tabela. Isso mapeia os campos de dados de origem para colunas da tabela de destino

```python
CREATE_MAPPING_COMMAND = """.create table StormEvents ingestion csv mapping 'StormEvents_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'"""

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_MAPPING_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="queue-a-message-for-ingestion"></a>Enfileirar uma mensagem para ingestão

Enfileirar uma mensagem para extrair dados do armazenamento de BLOBs e ingerir esses dados no Data Explorer do Azure.

```python
INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)

# All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties
INGESTION_PROPERTIES = IngestionProperties(database=KUSTO_DATABASE, table=DESTINATION_TABLE, dataFormat=DataFormat.csv,
                                           mappingReference=DESTINATION_TABLE_COLUMN_MAPPING, additionalProperties={'ignoreFirstRecord': 'true'})
# FILE_SIZE is the raw size of the data in bytes
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)

print('Done queuing up ingestion with Azure Data Explorer')
```

## <a name="query-data-that-was-ingested-into-the-table"></a>Consultar os dados ingeridos na tabela

Aguardar cinco a dez minutos para a ingestão na fila agendar a ingestão e carregar os dados no Data Explorer do Azure. Em seguida, execute o seguinte código para obter a contagem de registros na tabela StormEvents.

```python
QUERY = "StormEvents | count"

RESPONSE = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, QUERY)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="run-troubleshooting-queries"></a>Executar consultas de solução de problemas

Conectar [https://dataexplorer.azure.com](https://dataexplorer.azure.com) e conectar ao seu cluster. Execute o seguinte comando no banco de dados para ver se houve alguma falha de ingestão nas últimas quatro horas. Substitua o nome do banco de dados antes da execução.

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Execute o seguinte comando para exibir o status de todas as operações de ingestão nas últimas quatro horas. Substitua o nome do banco de dados antes da execução.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Table == "StormEvents" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja seguir nossos outros artigos, mantenha os recursos que você criou. Caso contrário, execute o seguinte comando no seu banco de dados para limpar a tabela StormEvents.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Próximas etapas

* [Consultar dados usando o Python](python-query-data.md)
