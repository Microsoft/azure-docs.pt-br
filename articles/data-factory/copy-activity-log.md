---
title: Log de sessão na atividade de cópia
description: Saiba mais sobre como habilitar o log de sessão na atividade de cópia no Azure Data Factory.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: yexu
ms.openlocfilehash: 7cb00d62556babbd8e43e2fac2faa815a63943ed
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385261"
---
#  <a name="session-log-in-copy-activity"></a>Log de sessão na atividade de cópia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Você pode registrar os nomes de arquivo copiados na atividade de cópia, o que pode ajudá-lo a garantir que os dados não sejam copiados com êxito da origem para o repositório de destino, mas também consistentes entre o repositório de origem e de destino examinando os arquivos copiados nos logs de sessão da atividade de cópia.  

Quando você habilita a configuração de tolerância a falhas na atividade de cópia para ignorar dados com falha, os arquivos ignorados e as linhas ignoradas também podem ser registrados em log.  Você pode obter mais detalhes da [tolerância a falhas na atividade de cópia](copy-activity-fault-tolerance.md). 

## <a name="configuration"></a>Configuração
O exemplo a seguir fornece uma definição de JSON para habilitar o log de sessão na atividade de cópia: 

```json
"typeProperties": {
    "source": {
        "type": "BinarySource",
        "storeSettings": {
            "type": "AzureDataLakeStoreReadSettings",
            "recursive": true
        },
        "formatSettings": {
            "type": "BinaryReadSettings"
        }
    },
    "sink": {
        "type": "BinarySink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings"
        }
    },                    
    "skipErrorFile": {
        "fileForbidden": true,
        "dataInconsistency": true
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
enableCopyActivityLog | Quando definido como true, você terá a oportunidade de registrar arquivos copiados, arquivos ignorados ou linhas ignoradas.  | True<br/>False (padrão) | Não
logLevel | "Info" registrará em log todos os arquivos copiados, arquivos ignorados e linhas ignoradas. "Aviso" registrará em log arquivos ignorados e somente linhas ignoradas.  | Info<br/>Aviso (padrão) | Não
enableReliableLogging | Quando for verdadeiro, a atividade de cópia no modo confiável limpará os logs imediatamente depois que cada arquivo for copiado para o destino.  Ao copiar enormes quantidades de arquivos com o modo de log confiável habilitado na atividade de cópia, você deve esperar que a taxa de transferência de cópia seja afetada, uma vez que as operações de gravação dupla são necessárias para cada cópia de arquivo. Uma solicitação é para o repositório de destino e outra solicitação é para o repositório de armazenamento de log.  A atividade de cópia no modo de melhor esforço limpará os logs com o lote de registros em um período de tempo, em que a taxa de transferência de cópia será muito menos afetada. A integridade e a pontualidade do registro em log não são garantidas nesse modo, pois há algumas possibilidades de que o último lote de eventos de log não tenha sido liberado para o arquivo de log quando a atividade de cópia falhou. Neste momento, você verá que alguns arquivos copiados para o destino não são registrados.  | True<br/>False (padrão) | Não
logLocationSettings | Um grupo de propriedades que pode ser usado para especificar o local para armazenar os logs de sessão. | | Não
linkedServiceName | O serviço vinculado do [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md#linked-service-properties) ou [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar os arquivos de log da sessão. | O nome de um serviço vinculado `AzureBlobStorage` ou `AzureBlobFS`, que se refere à instância de armazenamento que você usa para armazenar os arquivos de log. | Não
caminho | O caminho dos arquivos de log. | Especifique o caminho que você quer armazenar os arquivos de log. Se você não fornecer um caminho, o serviço criará um contêiner para você. | Não


## <a name="monitoring"></a>Monitoramento

### <a name="output-from-copy-activity"></a>Saída da atividade de cópia
Depois que a atividade de cópia for executada completamente, você poderá ver o caminho dos arquivos de log da saída de cada execução da atividade de cópia. Você pode encontrar os arquivos de log do caminho: `https://[your-blob-account].blob.core.windows.net/[logFilePath]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  Os arquivos de log serão os arquivos csv. 

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

> [!NOTE]
> Quando a `enableCopyActivityLog` propriedade é definida como `Enabled` , os nomes de arquivo de log são gerados pelo sistema.

### <a name="the-schema-of-the-log-file"></a>O esquema do arquivo de log

A seguir está o esquema de um arquivo de log.

Coluna | Descrição 
-------- | -----------  
Timestamp | O carimbo de data/hora quando o ADF lê, grava ou ignora o objeto.
Nível | O nível de log deste item. Pode ser ' Warning ' ou ' info '.
OperationName | Comportamento operacional da atividade de cópia do ADF em cada objeto. Pode ser "fileread", "filewrite", "fileskip" ou "TabularRowSkip".
OperationItem | Os nomes de arquivo ou linhas ignoradas.
Mensagem | Mais informações para mostrar se o arquivo foi lido do repositório de origem ou gravado no repositório de destino. Também pode ser o motivo pelo qual o arquivo ou as linhas estão sendo ignorados.

Veja a seguir um exemplo de um arquivo de log. 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-10-19 08:39:13.6688152,Info,FileRead,"sample1.csv","Start to read file: {""Path"":""sample1.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:39:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
2020-10-19 08:40:13.6688152,Info,FileRead,"sample2.csv","Start to read file: {""Path"":""sample2.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:40:13.9003981,Info,FileWrite,"sample2.csv","Start to write file from source file: sample2.csv."
2020-10-19 08:45:17.6508407,Info,FileRead,"sample2.csv","Complete reading file successfully. "
2020-10-19 08:45:28.7390083,Info,FileWrite,"sample2.csv","Complete writing file from source file: sample2.csv. File is successfully copied."
```
No arquivo de log acima, você pode ver que sample1.csv foi ignorado porque ele não pôde ser verificado para ser consistente entre o repositório de origem e de destino. Você pode obter mais detalhes sobre por que o sample1.csv se torna inconsistente: ele estava sendo alterado por outros aplicativos quando a atividade de cópia do ADF estava sendo copiada ao mesmo tempo. Você também pode ver sample2.csv foi copiado com êxito da origem para o repositório de destino.

Você pode usar vários mecanismos de análise para analisar ainda mais os arquivos de log.  Há alguns exemplos abaixo para usar a consulta SQL para analisar o arquivo de log importando o arquivo de log CSV para o banco de dados SQL em que o nome da tabela pode ser SessionLogDemo.  

-   Dê-me a lista de arquivos copiadas. 
```sql
select OperationItem from SessionLogDemo where Message like '%File is successfully copied%'
```

-   Dê-me a lista de arquivos copiada em um intervalo de tempo específico. 
```sql
select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%'
```

-   Dê-me um arquivo específico com seu tempo e metadados copiados. 
```sql
select * from SessionLogDemo where OperationItem='<file name>'
```

-   Forneça uma lista de arquivos com seus metadados copiados dentro de um intervalo de tempo. 
```sql
select * from SessionLogDemo where OperationName='FileRead' and Message like 'Start to read%' and OperationItem in (select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%')
```

-   Dê-me a lista de arquivos ignorados. 
```sql
select OperationItem from SessionLogDemo where OperationName='FileSkip'
```

-   Dê-me o motivo pelo qual um arquivo específico foi ignorado. 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip'
```

-   Dê-me a lista de arquivos ignorados devido ao mesmo motivo: "o arquivo de BLOB não existe". 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip' and Message like '%UserErrorSourceBlobNotExist%'
```

-   Dê-me o nome do arquivo que requer o tempo mais longo para copiar.
```sql
select top 1 OperationItem, CopyDuration=DATEDIFF(SECOND, min(TIMESTAMP), max(TIMESTAMP)) from SessionLogDemo group by OperationItem order by CopyDuration desc
```


## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Tolerância a falhas da atividade de cópia](copy-activity-fault-tolerance.md)
- [Consistência de dados da atividade de cópia](copy-activity-data-consistency.md)
