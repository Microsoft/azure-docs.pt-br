---
title: Obter alterações de recurso
description: Entenda como localizar quando um recurso foi alterado, obter uma lista das propriedades que foram alteradas e avaliar as diferenças.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 500a2d58c5fc9e1b63a544978c4b583eba60a63e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219069"
---
# <a name="get-resource-changes"></a>Obter alterações de recurso

Os recursos são alterados com o uso diário, reconfiguração e até mesmo a reimplantação.
A alteração pode vir de um indivíduo ou de um processo automatizado. A maioria das alterações é por design, mas, às vezes, não é. Com os últimos 14 dias do histórico de alterações, o Azure Resource Graph permite que você:

- Localize quando foram detectadas alterações em uma propriedade Azure Resource Manager
- Para cada alteração de recurso, consulte detalhes de alteração de propriedade
- Veja uma comparação completa do recurso antes e depois da alteração detectada

A detecção de alterações e os detalhes são importantes para os seguintes cenários de exemplo:

- Durante o gerenciamento de incidentes para entender alterações relacionadas _potencialmente_. Consulte eventos de alteração durante uma janela de tempo específica e avalie os detalhes da alteração.
- Manter um banco de dados de gerenciamento de configuração, conhecido como CMDB, atualizado. Em vez de atualizar todos os recursos e seus conjuntos de propriedades completos em uma frequência agendada, só obtenha o que foi alterado.
- Entender quais outras propriedades podem ter sido alteradas quando um estado de conformidade de um recurso mudou. A avaliação dessas propriedades adicionais pode fornecer informações sobre outras propriedades que talvez precisem ser gerenciadas por meio de uma definição de Azure Policy.

Este artigo mostra como reunir essas informações por meio do SDK do Resource Graph. Para ver essas informações na portal do Azure, consulte [histórico de alterações](../../policy/how-to/determine-non-compliance.md#change-history) de Azure Policy ou o [histórico de alterações](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log) do log de atividades do Azure. Para obter detalhes sobre as alterações em seus aplicativos da camada de infraestrutura até a implantação do aplicativo, consulte [Usar a Análise de Alterações de Aplicativo (versão prévia)](../../../azure-monitor/app/change-analysis.md) no Azure Monitor.

> [!NOTE]
> Os detalhes de alteração no Resource Graph para as propriedades do Resource Manager. Para controlar as alterações dentro de uma máquina virtual, consulte [Controlar alterações](../../../automation/change-tracking/overview.md) da Automação do Azure ou [Configuração de convidado para VMs](../../policy/concepts/guest-configuration.md) de Azure Policy.

> [!IMPORTANT]
> O histórico de alterações no Azure Resource Graph está em visualização pública.

## <a name="find-detected-change-events-and-view-change-details"></a>Localizar eventos de alteração detectados e exibir detalhes de alteração

A primeira etapa para ver o que mudou em um recurso é localizar os eventos de alteração relacionados a esse recurso dentro de uma janela de tempo. Cada evento de alteração também inclui detalhes sobre o que mudou no recurso. Essa etapa é feita por meio do ponto de extremidade REST **resourceChanges**.

O ponto de extremidade **resourceChanges** aceita os seguintes parâmetros no corpo da solicitação:

- **resourceId** \[required\]: O recurso do Azure no qual procurar alterações.
- **interval** \[required\]: Uma propriedade com datas de _início_ e _término_ para quando verificar se há um evento de alteração usando o **fuso horário Zulu (Z)** .
- **fetchPropertyChanges** (optional): Uma propriedade booliana que define se o objeto de resposta inclui alterações de propriedade.

Exemplo de corpo de solicitação:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

Com o corpo da solicitação acima, o URI da API REST para **resourceChanges** é:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

A resposta é semelhante a este exemplo:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

Cada evento de alteração detectado para o **resourceId** tem as seguintes propriedades:

- **alterid** - Esse valor é exclusivo para esse recurso. Embora a cadeia de caracteres **changeId** possa, às vezes, conter outras propriedades, só é garantido que seja exclusivo.
- **beforeSnapshot** - Contém o **snapshotid** e **carimbo de data/hora** do instantâneo de recursos que foi realizado antes de uma alteração ser detectada.
- **beforeSnapshot** - Contém o **snapshotid** e **carimbo de data/hora** do instantâneo de recursos que foi feito antes de uma alteração ser detectada.
- **ChangeType**-descreve o tipo de alteração detectada para todo o registro de alteração entre o **beforeSnapshot** e **afterSnapshot**. Os valores são: _Criar_, _Atualizar_ e _Excluir_. A matriz de propriedades **propertyChanges** só é incluída quando **ChangeType** é _Update_.

  > [!IMPORTANT]
  > _Create_ só está disponível em recursos que anteriormente existiam e foram excluídos nos últimos 14 dias.

- **propertyChanges** - Essa matriz de propriedades detalha todas as propriedades de recurso que foram atualizadas entre **beforeSnapshot**  e **afterSnapshot**:
  - **propertyName** - O nome da propriedade de recurso que foi alterada.
  - **changeCategory** - Descreve o que fez a alteração. Os valores são: _Sistema_ e _Usuário_.
  - **ChangeType** - Descreve o tipo de alteração detectada para a propriedade de recurso individual.
    Os valores são: _Inserir_, _Atualizar_, _Remover_.
  - **beforevalue** - O valor da propriedade de recurso em **beforeSnapshot**. Não é exibido quando **ChangeType** é _Insert_.
  - **afterValue** - O valor da propriedade de recurso em **afterSnapshot**. Não é exibido quando **changeType** é _Remove_.

## <a name="compare-resource-changes"></a>Comparar alterações de recurso

Com o **alterid** do ponto de extremidade do **resourceChanges**, o ponto de extremidade REST do **resourceChangeDetails** é usado para obter os instantâneos anterior e posterior do recurso que foi alterado.

O ponto de extremidade do **resourceChangeDetails** requer dois parâmetros no corpo da solicitação:

- **resourceId**: O recurso do Azure no qual comparar alterações.
- **changeId**: O evento de alteração exclusivo para o **resourceId** coletado de **resourceChanges**.

Exemplo de corpo de solicitação:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Com o corpo da solicitação acima, o URI da API REST para **resourceChangesDetails** é:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

A resposta é semelhante a este exemplo:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** e **afterSnapshot** cada uma delas fornecem o tempo que o instantâneo foi tirado e as propriedades nesse momento. A alteração ocorreu em algum ponto entre esses instantâneos. Observando o exemplo anterior, podemos ver que a propriedade alterada foi **supportsHttpsTrafficOnly**.

Para comparar os resultados, use a propriedade **changes** no **resourceChanges** ou avalie a parte **content** de cada instantâneo no **resourceChangeDetails** para determinar a diferença. Se você comparar os instantâneos, o **carimbo de data/hora** sempre será mostrado como uma diferença, apesar de ser esperado.

## <a name="next-steps"></a>Próximas etapas

- Consulte a linguagem em uso em [Consultas iniciais](../samples/starter.md).
- Consulte os usos avançados em [Consultas avançadas](../samples/advanced.md).
- Saiba mais sobre como [explorar recursos](../concepts/explore-resources.md).
- Para obter orientação sobre como trabalhar com consultas em alta frequência, consulte [Diretrizes para solicitações limitadas](../concepts/guidance-for-throttled-requests.md).
