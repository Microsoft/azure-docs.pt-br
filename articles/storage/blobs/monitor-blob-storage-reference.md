---
title: Referência de dados de monitoramento do armazenamento de BLOBs do Azure | Microsoft Docs
description: Referência de log e métricas para monitorar dados do armazenamento de BLOBs do Azure.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: 6dd53358b67eef481948d3349afc07086ea26fcd
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584899"
---
# <a name="azure-blob-storage-monitoring-data-reference"></a>Referência de dados de monitoramento do armazenamento de BLOBs do Azure

Consulte [Monitoramento do armazenamento do Azure](monitor-blob-storage.md) para obter detalhes sobre como coletar e analisar dados de monitoramento para o armazenamento do Azure.

## <a name="metrics"></a>Métricas

As tabelas a seguir listam as métricas da plataforma coletadas para o armazenamento do Azure. 

### <a name="capacity-metrics"></a>Métricas de capacidade

Os valores de métricas de capacidade são atualizados diariamente (até 24 horas). O intervalo de agregação define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de agregação compatível com todas as métricas de capacidade é uma hora (PT1H).

O Armazenamento do Azure fornece as seguintes métricas de capacidade no Azure Monitor.

#### <a name="account-level"></a>Nível de conta

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="blob-storage"></a>Armazenamento de Blobs

Esta tabela mostra as [métricas de armazenamento de BLOBs](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices).

| Métrica | Descrição |
| ------------------- | ----------------- |
| BlobCapacity | O total de armazenamento de Blob usado na conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensões: **BlobType** e **BlobTier** ([Definição](#metrics-dimensions)) |
| BlobCount    | O número de objetos blob armazenados na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensões: **BlobType** e **BlobTier** ([Definição](#metrics-dimensions)) |
| BlobProvisionedSize | A quantidade de armazenamento provisionada na conta de armazenamento. Essa métrica é aplicável somente a contas de armazenamento Premium. <br/><br/> Unidade: bytes <br/> Tipo de agregação: Média |
| ContainerCount    | O número de contêineres na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| IndexCapacity     | A quantidade de armazenamento usado pelo Índice ADLS Gen2 Hierárquico <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="transaction-metrics"></a>Métricas de transação

As métricas de transação são emitidas, do Armazenamento do Azure para o Azure Monitor, em cada solicitação a uma conta de armazenamento. Caso não haja nenhuma atividade em sua conta de armazenamento, não haverá nenhum dado nas métricas de transações no período. Todas as métricas de transação estão disponíveis no nível de serviço de armazenamento de conta e de BLOB. O intervalo de agregação define o intervalo de tempo para o qual os valores das métricas são apresentados. Os intervalos de agregação para todas as métricas de transação são PT1H e PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensões das métricas

O Armazenamento do Azure oferece suporte às seguintes dimensões para métricas no Azure Monitor.

### <a name="dimensions-available-to-all-storage-services"></a>Dimensões disponíveis para todos os serviços de armazenamento

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

### <a name="dimensions-specific-to-blob-storage"></a>Dimensões específicas ao armazenamento de BLOBs

| Nome da dimensão | Descrição |
| ------------------- | ----------------- |
| **BlobType** | O tipo de blob somente para métricas de Blob. Os valores com suporte são **BlockBlob**, **PageBlob** e **Azure Data Lake Storage**. Os blobs de acréscimo estão incluídos no **BlockBlob**. |
| **BlobTier** | O Armazenamento do Azure oferece diferentes camadas de acesso que permitem armazenar dados de objeto Blob da maneira mais econômica. Confira mais informações em [Camada de blob de armazenamento do Microsoft Azure](../blobs/storage-blob-storage-tiers.md). Os valores com suporte incluem: <br/> <li>**Frequente**: Camada frequente</li> <li>**Esporádico**: Camada esporádica</li> <li>**Arquivar**: Camada de arquivo</li> <li>**Premium**: Camada Premium para blob de blocos</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Tipos de camada para o Blob de páginas premium</li> <li>**Standard**: Tipo de camada para o Blob de páginas padrão</li> <li>**Sem camadas**: Tipo de camada de conta de armazenamento v1 de uso geral</li> |

Para saber as dimensões de métricas com suporte, você precisa especificar o valor da dimensão para exibir os valores correspondentes das métricas. Por exemplo, se você pesquisar o valor de **Transações** para respostas com sucesso, você precisa filtrar a dimensão **ResponseType** por **Sucesso**. Se você examinar o valor de **BlobCount** para blob de blocos, precisará filtrar a dimensão de **BlobType** com **BlockBlob**.

## <a name="resource-logs-preview"></a>Logs de recurso (versão prévia)

> [!NOTE]
> Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Essa versão preliminar habilita logs para blobs (incluindo o Azure Data Lake Storage Gen2), arquivos, filas, tabelas, contas de armazenamento Premium nas contas de armazenamento GPv1 e GPv2. Não há suporte para contas de armazenamento clássicas.

A tabela a seguir lista as propriedades dos logs de recursos do armazenamento do Azure quando eles são coletados nos logs de Azure Monitor ou no armazenamento do Azure. As propriedades descrevem a operação, o serviço e o tipo de autorização que foi usado para executar a operação.

### <a name="fields-that-describe-the-operation"></a>Campos que descrevem a operação

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Campos que descrevem como a operação foi autenticada

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Campos que descrevem o serviço

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Confira também

- Consulte [Monitoramento do armazenamento do Azure](monitor-blob-storage.md) para obter uma descrição do monitoramento do armazenamento do Azure.
- Confira [Como monitorar os recursos do Azure com o Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) para obter detalhes sobre o monitoramento de recursos do Azure.
