---
title: Preparar para alterar o formato para Azure Monitor logs de recursos
description: Os logs de recursos do Azure foram movidos para usar blobs de acréscimo em 1º de novembro de 2018.
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.openlocfilehash: d4be3983d70a1ca78d849e231b8cc55e2b5895d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033190"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>Preparar para a alteração de formato para Azure Monitor logs de plataforma arquivados em uma conta de armazenamento

> [!WARNING]
> Se você estiver enviando [logs de recursos do Azure ou métricas para uma conta de armazenamento usando configurações de diagnóstico](./resource-logs.md#send-to-azure-storage) ou [logs de atividade para uma conta de armazenamento usando perfis de log](./resource-logs.md#send-to-azure-storage), o formato dos dados na conta de armazenamento será alterado para linhas JSON em Nov. 1, 2018. As instruções abaixo descrevem o impacto e como atualizar suas ferramentas para manipular o novo formato.
>

## <a name="what-changed"></a>O que mudou

O Azure Monitor oferece um recurso que permite que você envie logs de recursos e logs de atividade para uma conta de armazenamento do Azure, namespace de hubs de eventos ou em um espaço de trabalho Log Analytics no Azure Monitor. Para resolver um problema de desempenho do sistema, em **1º de novembro de 2018 às 12:00, meia-noite UTC** o formato dos dados de log enviados ao armazenamento de BLOBs foi alterado. Caso tenha ferramentas que leem dados fora do armazenamento de blobs, você precisará atualizá-las para que elas reconheçam o novo formato de dados.

* Na quinta-feira, 1º de novembro de 2018 às 12:00, meia-noite UTC, o formato de blob mudou para [as linhas JSON](http://jsonlines.org/). Isso significa que cada registro será delimitado por uma nova linha, sem nenhuma matriz de registros externa e sem vírgulas entre os registros JSON.
* O formato de blob alterado para todas as configurações de diagnóstico em todas as assinaturas de uma só vez. O primeiro PT1H.jsno arquivo emitido para 1º de novembro usou esse novo formato. Os nomes de blob e de contêiner permanecem os mesmos.
* Definir uma configuração de diagnóstico entre antes de 1º de novembro continuou a emitir dados no formato atual até 1º de novembro.
* Essa alteração ocorreu ao mesmo tempo em todas as regiões de nuvem pública. A alteração não ocorrerá no Microsoft Azure operado pelas nuvens da 21Vianet, do Azure Alemanha ou do Azure governamental ainda.
* Essa alteração afeta os seguintes tipos de dados:
  * [Logs de recursos do Azure](./resource-logs.md#send-to-azure-storage) ([consulte a lista de recursos aqui](./resource-logs-schema.md))
  * [Métricas de recursos do Azure exportadas pelas configurações de diagnóstico](../essentials/diagnostic-settings.md)
  * [Dados de Log de atividades do Azure exportados pelos perfis de log](./activity-log.md)
* Essa alteração não afeta:
  * Logs de fluxo de rede
  * Logs de serviço do Azure não disponibilizados por meio do Azure Monitor ainda (por exemplo, Azure App logs de recursos do serviço, logs de análise de armazenamento)
  * Roteamento de logs de recursos do Azure e logs de atividade para outros destinos (hubs de eventos, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Como ver se você foi afetado

Você só é afetado por essa alteração se:
1. Está enviando dados de log para uma conta de armazenamento do Azure usando uma configuração de diagnóstico e
2. Tem ferramentas que dependem da estrutura JSON desses logs no armazenamento.
 
Para identificar se você tem configurações de diagnóstico que estão enviando dados para uma conta de armazenamento do Azure, você pode navegar até a seção **Monitor** do portal, clicar em **configurações de diagnóstico** e identificar todos os recursos que têm o **status de diagnóstico** definido como **habilitado**:

![Folha Configurações de Diagnóstico do Azure Monitor](media/resource-logs-blob-format/portal-diag-settings.png)

Se o Status de Diagnóstico estiver definido como habilitado, você terá uma configuração de diagnóstico ativa nesse recurso. Clique no recurso para ver se há alguma configuração de diagnóstico que está enviando dados para uma conta de armazenamento:

![Conta de armazenamento habilitada](media/resource-logs-blob-format/portal-storage-enabled.png)

Se você tiver recursos que estão enviando dados para uma conta de armazenamento usando essas configurações de diagnóstico de recurso, o formato dos dados nessa conta de armazenamento será afetado por essa alteração. A menos que você tenha ferramentas personalizadas que operem fora dessas contas de armazenamento, a alteração de formato não afetará você.

### <a name="details-of-the-format-change"></a>Detalhes da alteração de formato

O formato atual do arquivo PT1H.json no armazenamento de blobs do Azure usa uma matriz de registros JSON. Esta é uma amostra de um arquivo de log do KeyVault agora:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

O novo formato usa [Linhas JSON](http://jsonlines.org/), em que cada evento é uma linha, e o caractere de nova linha indica um novo evento. Esta será a aparência da amostra acima no arquivo PT1H.json após a alteração:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Esse novo formato permite que o Azure Monitor efetue push de arquivos de log usando [blobs de acréscimo](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), que são mais eficientes para o acréscimo contínuo de novos dados de evento.

## <a name="how-to-update"></a>Como atualizar

Você só precisará fazer atualizações se tiver ferramentas personalizadas que ingerem esses arquivos de log para processamento adicional. Se você estiver usando uma ferramenta externa de SIEM ou de análise de logs, recomendamos o [uso de hubs de eventos para a ingestão desses dados](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). A integração aos hubs de eventos é mais fácil em termos de processamento de logs de muitos serviços e de indicação do local em determinado log.

As ferramentas personalizadas devem ser atualizadas para manipular o formato atual e o formato Linhas JSON descrito acima. Isso garantirá que, quando os dados começarem a ser exibidos no novo formato, as ferramentas não sejam interrompidas.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [arquivar logs de recursos de recurso em uma conta de armazenamento](./resource-logs.md#send-to-azure-storage)
* Saiba mais sobre como [arquivar dados de log de atividades em uma conta de armazenamento](./activity-log.md#legacy-collection-methods)
