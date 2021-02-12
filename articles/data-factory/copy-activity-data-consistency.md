---
title: Verificação de consistência de dados na atividade de cópia
description: Saiba mais sobre como habilitar a verificação de consistência de dados na atividade de cópia no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: 274250fecdf69b6a488c33ff25df3728a1c90af0
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556368"
---
#  <a name="data-consistency-verification-in-copy-activity"></a>Verificação de consistência de dados na atividade de cópia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Quando você move dados do repositório de origem para o de destino, a atividade de cópia do Azure Data Factory fornece uma opção para realizar a verificação de consistência de dados adicional a fim de garantir que os dados não sejam apenas copiados com êxito da origem para o repositório de destino, mas também verificados como consistentes entre o repositório de origem e destino. Depois que arquivos inconsistentes forem encontrados durante a movimentação de dados, você poderá abortar a atividade de cópia ou continuar a copiar o restante habilitando a configuração de tolerância a falhas para ignorar arquivos inconsistentes. Você pode obter os nomes de arquivo ignorados habilitando a configuração de log de sessão na atividade de cópia. Você pode consultar o [log de sessão na atividade de cópia](copy-activity-log.md) para obter mais detalhes.

## <a name="supported-data-stores-and-scenarios"></a>Armazenamentos de dados e cenários com suporte

-   A verificação de consistência de dados é suportada por todos os conectores, exceto FTP, sFTP e HTTP. 
-   Não há suporte para a verificação de consistência de dados no cenário de cópia de preparo.
-   Ao copiar arquivos binários, a verificação de consistência de dados só estará disponível quando o comportamento de ' PreserveHierarchy ' for definido na atividade de cópia.
-   Ao copiar vários arquivos binários em uma única atividade de cópia com a verificação de consistência de dados habilitada, você tem a opção de anular a atividade de cópia ou continuar a copiar o restante habilitando a configuração de tolerância a falhas para ignorar arquivos inconsistentes. 
-   Ao copiar uma tabela em atividade de cópia única com verificação de consistência de dados habilitada, a atividade de cópia falhará se o número de linhas lidas da origem for diferente do número de linhas copiadas para o destino mais o número de linhas incompatíveis que foram ignoradas.


## <a name="configuration"></a>Configuração
O exemplo a seguir fornece uma definição de JSON para habilitar a verificação de consistência de dados na atividade de cópia: 

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
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
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
validateDataConsistency | Se você definir true para essa propriedade, ao copiar arquivos binários, a atividade de cópia verificará o tamanho do arquivo, lastModifiedDate e soma de verificação MD5 para cada arquivo binário copiado do repositório de origem para destino para garantir a consistência dos dados entre o repositório de origem e destino. Ao copiar dados tabulares, a atividade de cópia verificará a contagem total de linhas após o trabalho ser concluído para garantir que o número total de linhas lidas da origem seja igual ao número de linhas copiadas para o destino mais o número de linhas incompatíveis que foram ignoradas. Lembre-se de que o desempenho da cópia será afetado pela habilitação dessa opção.  | True<br/>False (padrão) | Não
dataInconsistency | Um dos pares chave-valor dentro do recipiente de propriedades skipErrorFile para determinar se você deseja ignorar os arquivos inconsistentes. <br/> -True: você deseja copiar o restante ignorando arquivos inconsistentes.<br/> -False: você deseja anular a atividade de cópia quando um arquivo inconsistente for encontrado.<br/>Lembre-se de que essa propriedade só é válida quando você está copiando arquivos binários e define validateDataConsistency como true.  | True<br/>False (padrão) | Não
logSettings | Um grupo de propriedades que pode ser especificado para habilitar o log de sessão para registrar arquivos ignorados. | | Não
linkedServiceName | O serviço vinculado do [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md#linked-service-properties) ou [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para armazenar os arquivos de log da sessão. | O nome de um serviço vinculado `AzureBlobStorage` ou `AzureBlobFS`, que se refere à instância de armazenamento que você usa para armazenar os arquivos de log. | Não
caminho | O caminho dos arquivos de log. | Especifique o caminho que você quer armazenar os arquivos de log. Se você não fornecer um caminho, o serviço criará um contêiner para você. | Não

>[!NOTE]
>- Ao copiar arquivos binários de, ou para o BLOB ou Azure Data Lake Storage Gen2 do Azure, o ADF faz a verificação de soma de confirmação MD5 no nível de bloco utilizando a API de [blob do Azure](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy&preserve-view=true) e a [API Azure data Lake Storage Gen2](/rest/api/storageservices/datalakestoragegen2/path/update#request-headers) Se ContentMD5 em arquivos existirem no blob do Azure ou Azure Data Lake Storage Gen2 como fontes de dados, o ADF verificará a soma de verificação MD5 de nível de arquivo depois de ler os arquivos também. Depois de copiar os arquivos para o blob do Azure ou Azure Data Lake Storage Gen2 como destino de dados, o ADF grava ContentMD5 no blob do Azure ou Azure Data Lake Storage Gen2 que pode ser consumido ainda mais pelos aplicativos downstream para verificação de consistência de dados.
>- O ADF faz a verificação do tamanho do arquivo ao copiar arquivos binários entre quaisquer armazenamentos.

## <a name="monitoring"></a>Monitoramento

### <a name="output-from-copy-activity"></a>Saída da atividade de cópia
Depois que a atividade de cópia for executada completamente, você poderá ver o resultado da verificação de consistência de dados da saída de cada execução da atividade de cópia:

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
Você pode ver os detalhes da verificação de consistência de dados da "propriedade dataConsistencyVerification".

Valor de **VerificationResult**: 
-   **Verified**:  Os dados copiados foram verificados para serem consistentes entre o repositório de origem e destino. 
-   **NotVerified**: Seus dados copiados não foram verificados como consistentes porque você não habilitou o validateDataConsistency na atividade de cópia. 
-   **Unsupported**: Seus dados copiados não foram verificados como consistentes porque a verificação de consistência de dados não tem suporte para esse par de cópia em particular. 

Valor de **InconsistentData**: 
-   **Encontrado**: A atividade de cópia do ADF encontrou dados inconsistentes. 
-   **Ignorada**: A atividade de cópia do ADF encontrou e ignorou dados inconsistentes. 
-   **Nenhum**: A atividade de cópia do ADF não encontrou dados inconsistentes. Pode ser porque seus dados foram verificados para serem consistentes entre o repositório de origem e de destino ou porque você desabilitou o validateDataConsistency na atividade de cópia. 

### <a name="session-log-from-copy-activity"></a>Log de sessão da atividade de cópia

Se você configurar para registrar em log os arquivos inconsistentes, poderá encontrar o arquivo de log neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`.  Os arquivos de log serão os arquivos csv. 

O esquema de um arquivo de log é o seguinte:

Coluna | Descrição 
-------- | -----------  
Timestamp | O carimbo de data/hora quando o Azure Data Factory ignora os arquivos inconsistentes.
Nível | O nível de log deste item. Ele estará no nível de "Aviso" para o item que mostra o arquivo ignorado.
OperationName | Comportamento operacional da atividade de cópia do Azure Data Factory em cada arquivo. Será "FileSkip" para especificar o arquivo a ser ignorado.
OperationItem | O nome de arquivo a ser ignorado.
Mensagem | Mais informações para ilustrar por que os arquivos estão sendo ignorados.

O exemplo de um arquivo de log é o seguinte: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
No arquivo de log acima, você pode ver que sample1.csv foi ignorado porque ele não pôde ser verificado para ser consistente entre o repositório de origem e de destino. Você pode obter mais detalhes sobre por que o sample1.csv se torna inconsistente: ele estava sendo alterado por outros aplicativos quando a atividade de cópia do ADF estava sendo copiada ao mesmo tempo. 



## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Tolerância a falhas da atividade de cópia](copy-activity-fault-tolerance.md)