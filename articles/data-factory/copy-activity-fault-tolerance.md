---
title: Tolerância a falhas da atividade de cópia no Azure Data Factory
description: Saiba como adicionar a tolerância a falhas da atividade de cópia no Azure Data Factory ignorando dados incompatíveis.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: yexu
ms.openlocfilehash: 0fb6beb776f5a553e85f690d49e3433f93b9ee16
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809534"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Tolerância a falhas da atividade de cópia no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Versão atual](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Quando você copia dados do repositório de origem para o de destino, a atividade de cópia do Azure Data Factory fornece um determinado nível de tolerância a falhas para impedir a interrupção por causa de falhas no meio da movimentação de dados. Por exemplo, você está copiando milhões de linhas do repositório de origem para o de destino, em que uma chave primária foi criada no banco de dados de destino, mas o banco de dados de origem não tem nenhuma chave primária definida. Quando você for copiar linhas duplicadas da origem para o destino, ocorrerá a falha de violação de CP no banco de dados de destino. Neste momento, a atividade de cópia oferece duas maneiras de lidar com esses erros: 
- Você pode anular a atividade de cópia quando qualquer falha for encontrada. 
- Você pode continuar a copiar o restante habilitando a tolerância a falhas para ignorar os dados incompatíveis. Por exemplo, ignorar a linha duplicada neste caso. Além disso, você pode registrar os dados ignorados habilitando o log de sessão na atividade de cópia. Você pode consultar o [log de sessão na atividade de cópia](copy-activity-log.md) para obter mais detalhes.

## <a name="copying-binary-files"></a>Copiar arquivos binários 

O Azure Data Factory oferece suporte aos seguintes cenários de tolerância a falhas ao copiar arquivos binários. Você pode optar por anular a atividade de cópia ou continuar copiando o restante nos seguintes cenários:

1. Os arquivos a serem copiados pelo Azure Data Factory estão sendo excluídos por outros aplicativos ao mesmo tempo.
2. Algumas pastas ou arquivos específicos não permitem o acesso ao Azure Data Factory porque as listas de controle de acesso desses arquivos ou pastas exigem um nível de permissão mais alto que as informações de conexão configuradas no Azure Data Factory.
3. Um ou mais arquivos não são verificados como consistentes entre o repositório de origem e de destino, se você habilitar a configuração de verificação de consistência de dados no Azure Data Factory.

### <a name="configuration"></a>Configuração 
Ao copiar arquivos binários entre repositórios de armazenamento, você pode habilitar a tolerância a falhas da seguinte maneira: 

```json
"typeProperties": { 
    "source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
            } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
    }, 
    "skipErrorFile": { 
        "fileMissing": true, 
        "fileForbidden": true, 
        "dataInconsistency": true,
        "invalidFileName": true     
    }, 
    "validateDataConsistency": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
} 
```
Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | -------- 
skipErrorFile | Um grupo de propriedades para especificar os tipos de falhas que você deseja ignorar durante a movimentação de dados. | | Não
fileMissing | Um dos pares chave-valor dentro do conjunto de propriedades skipErrorFile para determinar se você deseja ignorar arquivos, que estão sendo excluídos por outros aplicativos enquanto o Azure Data Factory está copiando. <br/> -True: você deseja copiar o restante ignorando os arquivos que estão sendo excluídos por outros aplicativos. <br/> -False: você deseja anular a atividade de cópia depois que todos os arquivos forem excluídos do repositório de origem no meio da movimentação de dados. <br/>Lembre-se de que essa propriedade é definida como true como padrão. | True (padrão) <br/>Falso | Não
fileForbidden | Um dos pares chave-valor dentro do conjunto de propriedades skipErrorFile para determinar se você deseja ignorar os arquivos específicos, quando as ACLs desses arquivos ou pastas exigem um nível de permissão maior do que a conexão configurada no Azure Data Factory. <br/> -True: você deseja copiar o restante ignorando os arquivos. <br/> -False: você deseja anular a atividade de cópia depois de encontrar o problema de permissão em pastas ou arquivos. | True <br/>False (padrão) | Não
dataInconsistency | Um dos pares chave-valor dentro do conjunto de propriedades skipErrorFile para determinar se você deseja ignorar os dados inconsistentes entre o repositório de origem e de destino. <br/> -True: você deseja copiar o restante ignorando arquivos inconsistentes. <br/> - Falso: você deseja anular a atividade de cópia quando dados inconsistentes forem encontrados. <br/>Lembre-se de que essa propriedade só é válida quando você define validateDataConsistency como True. | True <br/>False (padrão) | Não
invalidFileName | Um dos pares chave-valor dentro do recipiente de propriedades skipErrorFile para determinar se você deseja ignorar os arquivos específicos, quando os nomes de arquivo são inválidos para o repositório de destino. <br/> -True: você deseja copiar o restante, ignorando os arquivos com nomes de arquivo inválidos. <br/> -False: você deseja anular a atividade de cópia depois que os arquivos tiverem nomes de arquivo inválidos. <br/>Lembre-se de que essa propriedade funciona ao copiar arquivos binários de qualquer armazenamento de armazenamento para ADLS Gen2 ou copiar arquivos binários do AWS S3 para qualquer armazenamento de armazenamento somente. | True <br/>False (padrão) | Não
logSettings  | Um grupo de propriedades que poderá ser especificado quando você desejar registrar os nomes dos objetos ignorados. | &nbsp; | Não
linkedServiceName | O serviço vinculado do [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md#linked-service-properties) ou [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar os arquivos de log da sessão. | O nome de um serviço vinculado `AzureBlobStorage` ou `AzureBlobFS`, que se refere à instância de armazenamento que você usa para armazenar o arquivo de log. | Não
caminho | O caminho dos arquivos de log. | Especifique o caminho que você usa para armazenar os arquivos de log. Se você não fornecer um caminho, o serviço criará um contêiner para você. | Não

> [!NOTE]
> Os seguintes são os pré-requisitos de habilitar a tolerância a falhas na atividade de cópia ao copiar arquivos binários.
> Para ignorar arquivos específicos quando eles estão sendo excluídos do repositório de origem:
> - O conjunto de código de origem e o conjunto de fonte de coletor devem ser do formato binário, e o tipo de compactação não pode ser especificado. 
> - Os tipos de armazenamento de dados com suporte são armazenamento de BLOBs do Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, armazenamento de arquivos do Azure, sistema de arquivos, FTP, SFTP, Amazon S3, Google Cloud Storage e HDFS.
> - Somente se você especificar vários arquivos no conjunto de dado de origem, que pode ser uma pasta, curinga ou uma lista de arquivos, a atividade de cópia poderá ignorar os arquivos de erro específicos. Se um único arquivo for especificado no conjunto de fonte de origem a ser copiado para o destino, a atividade de cópia falhará se ocorrer algum erro.
>
> Para ignorar arquivos específicos quando seu acesso é proibido do repositório de origem:
> - O conjunto de código de origem e o conjunto de fonte de coletor devem ser do formato binário, e o tipo de compactação não pode ser especificado. 
> - Os tipos de armazenamento de dados com suporte são o armazenamento de BLOBs do Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, armazenamento de arquivos do Azure, SFTP, Amazon S3 e HDFS.
> - Somente se você especificar vários arquivos no conjunto de dado de origem, que pode ser uma pasta, curinga ou uma lista de arquivos, a atividade de cópia poderá ignorar os arquivos de erro específicos. Se um único arquivo for especificado no conjunto de fonte de origem a ser copiado para o destino, a atividade de cópia falhará se ocorrer algum erro.
>
> Para ignorar arquivos específicos quando eles são verificados como inconsistentes entre o repositório de origem e de destino:
> - Você pode obter mais detalhes do documento de consistência de dados [aqui](./copy-activity-data-consistency.md).

### <a name="monitoring"></a>Monitoramento 

#### <a name="output-from-copy-activity"></a>Saída da atividade de cópia
Você pode obter o número de arquivos que estão sendo lidos, gravados e ignorados por meio da saída de cada execução de atividade de cópia. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

#### <a name="session-log-from-copy-activity"></a>Log de sessão da atividade de cópia

Se você configurar para registrar em log os nomes de arquivos ignorados, poderá encontrar o arquivo de log neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Os arquivos de log devem ser arquivos csv. O esquema do arquivo de log é o seguinte:

Coluna | Descrição 
-------- | -----------  
Timestamp | O carimbo de data/hora quando o Azure Data Factory ignora o arquivo.
Nível | O nível de log deste item. Ele estará no nível de "Aviso" para o item que mostra o arquivo ignorado.
OperationName | Comportamento operacional da atividade de cópia do Azure Data Factory em cada arquivo. Será "FileSkip" para especificar o arquivo a ser ignorado.
OperationItem | Os nomes de arquivo a serem ignorados.
Mensagem | Mais informações para ilustrar por que o arquivo está sendo ignorado.

O exemplo de um arquivo de log é o seguinte: 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
No log acima, você pode ver que o bigfile.csv foi ignorado porque outro aplicativo excluiu esse arquivo quando o ADF o estava copiando. E 3_nopermission.txt foi ignorado porque o Azure Data Factory não tem permissão para acessá-lo devido a um problema de permissão.


## <a name="copying-tabular-data"></a>Copiar dados de tabela 

### <a name="supported-scenarios"></a>Cenários com suporte
A atividade de cópia oferece suporte a três cenários para detectar, ignorar e registrar dados de tabela incompatíveis:

- **Incompatibilidade entre o tipo de dados de origem e o tipo nativo do coletor**. 

    Por exemplo:  Copiar dados de um arquivo CSV no Armazenamento de Blobs para um banco de dados SQL com uma definição de esquema que contenha três colunas do tipo INT. As linhas do arquivo CSV que contêm dados numéricos, como 123.456.789, são copiadas com êxito para o repositório de coletores. No entanto, as linhas que contêm valores não numéricos, como 123.456, abc, são detectadas como incompatíveis e ignoradas.

- **Incompatibilidade no número de colunas entre a origem e o coletor**.

    Por exemplo:  Copiar dados de um arquivo CSV no Armazenamento de Blobs para um banco de dados SQL com uma definição de esquema que contenha seis colunas. As linhas do arquivo CSV que contêm seis colunas são copiadas com êxito no armazenamento do coletor. As linhas do arquivo CSV que contêm mais de seis colunas são detectadas como incompatíveis e ignoradas.

- **Violação de chave primária ao gravar no SQL Server/Banco de Dados SQL do Azure/Azure Cosmos DB**.

    Por exemplo:  Copiar dados de um servidor SQL para um banco de dados SQL. Uma chave primária é definida no banco de dados SQL do coletor, mas nenhuma chave primária é definida no SQL Server de origem. As linhas duplicadas que existem na origem não podem ser copiadas no coletor. A atividade de cópia copia apenas a primeira linha dos dados de origem no coletor. As linhas da origem subsequentes que contêm o valor de chave primária duplicado são detectadas como incompatíveis e ignoradas.

>[!NOTE]
>- Para carregar dados no Azure Synapse Analytics usando o polybase, defina as configurações nativas de tolerância a falhas do polybase especificando políticas de rejeição por meio de "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" na atividade de cópia. Você ainda pode habilitar o redirecionamento de linhas incompatíveis com o PolyBase para o Blob ou ADLS como mostrado abaixo.
>- Esse recurso não se aplica quando a atividade de cópia é configurada para chamar [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Esse recurso não se aplica quando a atividade de cópia é configurada para chamar um [procedimento armazenado de um coletor SQL](./connector-azure-sql-database.md#invoke-a-stored-procedure-from-a-sql-sink).

### <a name="configuration"></a>Configuração
O seguinte exemplo fornece uma definição de JSON que configura a omissão de linhas incompatíveis na atividade de cópia:

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    } 
}, 
```

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Especifica se deve ignorar linhas incompatíveis durante a cópia ou não. | True<br/>False (padrão) | Não
logSettings | Um grupo de propriedades que poderá ser especificado quando você desejar registrar as linhas incompatíveis. | &nbsp; | Não
linkedServiceName | O serviço vinculado do [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md#linked-service-properties) ou [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar o log que contém as linhas ignoradas. | O nome de um serviço vinculado `AzureBlobStorage` ou `AzureBlobFS`, que se refere à instância de armazenamento que você usa para armazenar o arquivo de log. | Não
caminho | O caminho dos arquivos de log que contém as linhas ignoradas. | Especifique o caminho que você deseja usar para registrar em log os dados incompatíveis. Se você não fornecer um caminho, o serviço criará um contêiner para você. | Não

### <a name="monitor-skipped-rows"></a>Monitorar linhas ignoradas
Depois que a execução da atividade de cópia for concluída, você verá o número de linhas ignoradas na saída da atividade de cópia:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

Se você configurar para registrar em log as linhas incompatíveis, poderá encontrar o arquivo de log neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Os arquivos de log serão os arquivos csv. O esquema do arquivo de log é o seguinte:

Coluna | Descrição 
-------- | -----------  
Timestamp | O carimbo de data/hora quando o Azure Data Factory ignora as linhas incompatíveis
Nível | O nível de log deste item. Ele estará no nível de "Aviso" se este item mostrar as linhas ignoradas
OperationName | Comportamento operacional da atividade de cópia do Azure Data Factory em cada linha. Será "TabularRowSkip" para especificar que a linha incompatível específica foi ignorada
OperationItem | As linhas ignoradas do repositório de dados de origem.
Mensagem | Mais informações para ilustrar por que a incompatibilidade dessa linha específica.


Um exemplo do conteúdo do arquivo de log é o seguinte:

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

No arquivo de log de amostra acima, é possível ver que uma linha "dados1, dados2, dados3" foi ignorada devido ao problema de conversão de tipo do repositório de origem para o de destino. Outra linha "dados4, dados5, dados6" foi ignorada devido a um problema de violação de CP do repositório de origem ao de destino. 


## <a name="copying-tabular-data-legacy"></a>Copiar dados de tabela (herdados):

A seguir, é apresentada a maneira herdada de habilitar a tolerância a falhas para copiar apenas dados de tabela. Se você estiver criando um novo pipeline ou atividade, é recomendável começar a partir [daqui](#copying-tabular-data).

### <a name="configuration"></a>Configuração
O seguinte exemplo fornece uma definição de JSON que configura a omissão de linhas incompatíveis na atividade de cópia:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Especifica se deve ignorar linhas incompatíveis durante a cópia ou não. | True<br/>False (padrão) | Não
redirectIncompatibleRowSettings | Um grupo de propriedades que poderá ser especificado quando você desejar registrar as linhas incompatíveis. | &nbsp; | Não
linkedServiceName | O serviço vinculado do [Armazenamento do Azure](connector-azure-blob-storage.md#linked-service-properties) ou [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) para armazenar o log que contém as linhas ignoradas. | Os nomes de um serviço vinculado `AzureStorage` ou `AzureDataLakeStore`, que se referem à instância de armazenamento que você deseja usar para armazenar o arquivo de log. | Não
caminho | O caminho do arquivo de log que contém as linhas ignoradas. | Especifique o caminho que você deseja usar para registrar em log os dados incompatíveis. Se você não fornecer um caminho, o serviço criará um contêiner para você. | Não

### <a name="monitor-skipped-rows"></a>Monitorar linhas ignoradas
Depois que a execução da atividade de cópia for concluída, você verá o número de linhas ignoradas na saída da atividade de cópia:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Se você configurar para registrar em log as linhas incompatíveis, poderá encontrar o arquivo de log neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Os arquivos de log só podem ser arquivos csv. Os dados originais ignorados serão registrados com uma vírgula como delimitador de coluna, se necessário. Mais duas colunas "ErrorCode" e "ErrorMessage" são adicionadas além dos dados de origem originais no arquivo de log, em que é possível ver a causa raiz da incompatibilidade. ErrorCode e ErrorMessage aparecerão entre aspas duplas. 

Um exemplo do conteúdo do arquivo de log é o seguinte:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Próximas etapas
Confira os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)