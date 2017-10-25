---
title: Arquivar o Log de Atividades do Azure | Microsoft Docs
description: "Saiba como arquivar o Log de Atividades do Azure para retenção de longo prazo em uma conta de armazenamento."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: johnkem
ms.openlocfilehash: 0e3a5b84f57eac96249430fa1c2c4cc076c2926a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="archive-the-azure-activity-log"></a>Arquivar o Log de Atividades do Azure
Neste artigo, mostraremos como você pode usar o portal do Azure, os cmdlets do PowerShell ou a CLI de Plataforma Cruzada para arquivar seu [**Log de Atividades do Azure**](monitoring-overview-activity-logs.md) em uma conta de armazenamento. Essa opção será útil se você quiser manter seu Log de Atividades por mais de 90 dias (com controle total sobre a política de retenção) para auditoria, análise estática ou backup. Se você só precisar manter seus eventos por 90 dias ou menos, não será necessário configurar o arquivamento em uma conta de armazenamento, já que os eventos de Log de Atividades são mantidos na plataforma do Azure por 90 dias sem habilitar o arquivamento.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você precisará [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) na qual é possível arquivar o seu Log de Atividades. É altamente recomendável que você não use uma conta de armazenamento existente que tenha outros dados sem monitoramento armazenados para que você possa controlar melhor o acesso aos dados de monitoramento. No entanto, se você estiver arquivando também os Logs de Diagnóstico e as métricas em uma conta de armazenamento, talvez faça sentido usar essa conta de armazenamento para o Log de Atividades, bem como manter todos os dados de monitoramento em um local central. A conta de armazenamento usada deve ser uma conta de armazenamento de finalidade geral e não uma conta de armazenamento de blobs. A conta de armazenamento não precisa estar na mesma assinatura que a assinatura que emite os logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas.

## <a name="log-profile"></a>Perfil de Log
Para arquivar o Log de Atividades usando qualquer um dos métodos abaixo, você deverá definir o **Log de Perfil** para uma assinatura. O Perfil de Log define o tipo de eventos armazenados ou transmitidos e as saídas — conta de armazenamento e/ou hub de eventos. Ele também define a política de retenção (número de dias para manter) para eventos armazenados em uma conta de armazenamento. Se a política de retenção for definida como zero, os eventos serão armazenados indefinidamente. Caso contrário, isso pode ser definido como qualquer valor entre 1 e 2147483647. As políticas de retenção são aplicadas por dia, para que, ao final de um dia (UTC), os logs do dia após a política de retenção sejam excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início do dia de hoje, os logs de anteontem serão excluídos. [Você pode ler mais sobre perfis de log aqui](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). 

## <a name="archive-the-activity-log-using-the-portal"></a>Arquivar o Log de Atividades usando o portal
1. No portal, clique no link **Log de atividades** na barra de navegação do lado esquerdo. Se você não vir um link para o Log de Atividades, clique no link **Mais Serviços** primeiro.
   
    ![Navegue até a folha Log de Atividades](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. Na parte superior da folha, clique **Exportar**.
   
    ![Clique no botão Exportar](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. Na folha que aparece, marque a caixa de **Exportar para uma conta de armazenamento** e selecione uma conta de armazenamento.
   
    ![De uma conta de armazenamento](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. Usando o controle deslizante ou a caixa de texto, defina um número de dias para os quais eventos do Log de Atividades devem ser mantidos em sua conta de armazenamento. Se você preferir que os dados persistam na conta de armazenamento indefinidamente, defina esse número como zero.
5. Clique em **Salvar**.

## <a name="archive-the-activity-log-via-powershell"></a>Arquivar o Log de Atividades por meio do PowerShell
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| StorageAccountId |Não |A ID de Recurso da Conta de Armazenamento na qual os Logs de Atividades devem ser salvos. |
| Locais |Sim |Lista separada por vírgulas de regiões para as quais você gostaria de coletar eventos do Log de Atividades. Você pode exibir uma lista de todas as regiões [ao visitar esta página](https://azure.microsoft.com/en-us/regions) ou usando [a API REST de Gerenciamento do Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| RetentionInDays |Sim |Número de dias durante os quais os eventos devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazena os logs indefinidamente (para sempre). |
| Categorias |Sim |Lista separada por vírgulas de categorias de eventos que devem ser coletados. Os valores possíveis são Gravação, Exclusão e Ação. |

## <a name="archive-the-activity-log-via-cli"></a>Arquivar o Log de Atividades por meio da CLI
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| name |Sim |Nome de seu perfil de log. |
| storageId |Não |A ID de Recurso da Conta de Armazenamento na qual os Logs de Atividades devem ser salvos. |
| Locais |Sim |Lista separada por vírgulas de regiões para as quais você gostaria de coletar eventos do Log de Atividades. Você pode exibir uma lista de todas as regiões [ao visitar esta página](https://azure.microsoft.com/en-us/regions) ou usando [a API REST de Gerenciamento do Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| RetentionInDays |Sim |Número de dias durante os quais os eventos devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazenará os logs indefinidamente (para sempre). |
| Categorias |Sim |Lista separada por vírgulas de categorias de eventos que devem ser coletados. Os valores possíveis são Gravação, Exclusão e Ação. |

## <a name="storage-schema-of-the-activity-log"></a>Esquema de armazenamento do Log de Atividades
Depois de você configurar arquivamento, um contêiner de armazenamento será criado na conta de armazenamento assim que ocorrer um evento de Log de Atividades. Os blobs no contêiner seguem o mesmo formato em todos os Logs de Diagnóstico e no Log de Atividades. A estrutura desses blobs é:

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{ID da assinatura}/y={ano de quatro dígitos}/m={mês numérico de dois dígitos}/d={dia numérico de dois dígitos}/h={hora de relógio de 24 horas de dois dígitos}/m=00/PT1H.json
> 
> 

Por exemplo, um nome de blob poderia ser:

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro de uma hora especificada na URL do blob (por exemplo, h=12). Durante a hora presente, os eventos são acrescentados ao arquivo PT1H.json conforme eles ocorrem. O valor de minuto (m=00) é sempre 00, como eventos de Log de Atividades são divididos em blobs individuais por hora.

No arquivo PT1H.json, cada evento é armazenado na matriz de "registros", seguindo este formato:

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Nome do elemento | Descrição |
| --- | --- |
| tempo real |Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| ResourceId |ID de recurso do recurso afetado. |
| operationName |Nome da operação. |
| categoria |Categoria da ação, por exemplo, Gravação, Leitura e Ação. |
| resultType |O tipo do resultado, por exemplo, Êxito, Falha e Início |
| resultSignature |Depende do tipo de recurso. |
| durationMs |Duração da operação em milissegundos |
| callerIpAddress |Endereço IP do usuário que realizou a operação, declaração UPN ou declaração SPN com base na disponibilidade. |
| correlationId |Geralmente, um GUID no formato de cadeia de caracteres. Os eventos que compartilham um correlationId pertencem à mesma ação superior. |
| identidade |Blob JSON que descreve a autorização e as declarações. |
| authorization |Blob de propriedades RBAC do evento. Geralmente, inclui as propriedades "action", "role" e "scope". |
| level |Nível do evento. Um dos seguintes valores: “Crítico”, “Erro”, “Aviso”, “Informativo” e “Detalhado” |
| location |Região na qual ocorreu o local (ou global). |
| propriedades |Conjunto de pares de `<Key, Value>` (ou seja, Dicionário) que descreve os detalhes do evento. |

> [!NOTE]
> As propriedades e o uso dessas propriedades podem variar dependendo do recurso.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Baixar blobs para análise](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)
* [Transmitir o Log de Atividades para os Hubs de Eventos](monitoring-stream-activity-logs-event-hubs.md)
* [Leia mais sobre o Log de Atividades](monitoring-overview-activity-logs.md)

