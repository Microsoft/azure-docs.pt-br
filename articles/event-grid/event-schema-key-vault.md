---
title: Azure Key Vault como fonte da grade de eventos
description: Descreve as propriedades e o esquema fornecido para eventos de Azure Key Vault com a grade de eventos do Azure
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: ea8821b15000b74a10f28730ccf82b538e7819e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363399"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault como fonte da grade de eventos

Este artigo fornece as propriedades e o esquema para eventos no [Azure Key Vault](../key-vault/index.yml). Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md).


## <a name="available-event-types"></a>Tipos de evento disponíveis

Uma conta de Azure Key Vault gera os seguintes tipos de evento:

| Nome completo do evento | Nome de exibição do evento | Descrição |
| ---------- | ----------- |---|
| Microsoft. keyvault. CertificateNewVersionCreated | Nova versão de certificado criada | Disparado quando um novo certificado ou nova versão do certificado é criado. |
| Microsoft. keyvault. CertificateNearExpiry | Certificado próximo à expiração | Disparado quando a versão atual do certificado está prestes a expirar. (O evento é disparado 30 dias antes da data de validade.) |
| Microsoft. keyvault. CertificateExpired | O Certificado Expirou | Disparado quando o certificado expira. |
| Microsoft. keyvault. KeyNewVersionCreated | Nova versão de chave criada | Disparado quando uma nova versão de chave ou nova chave é criada. |
| Microsoft. keyvault. KeyNearExpiry | Chave próxima à expiração | Disparado quando a versão atual de uma chave está prestes a expirar. (O evento é disparado 30 dias antes da data de validade.) |
| Microsoft. keyvault. keyvenceu | Chave expirada | Disparado quando uma chave expira. |
| Microsoft. keyvault. SecretNewVersionCreated | Nova versão secreta criada | Disparado quando uma nova versão secreta ou nova secreta é criada. |
| Microsoft. keyvault. SecretNearExpiry | Segredo próximo da expiração | Disparado quando a versão atual de um segredo está prestes a expirar. (O evento é disparado 30 dias antes da data de validade.) |
| Microsoft. keyvault. SecretExpired | Segredo expirado | Disparado quando um segredo expira. |
| Microsoft. keyvault. VaultAccessPolicyChanged | Política de acesso do cofre alterada | Disparado quando uma política de acesso em Key Vault alterada. Ele inclui um cenário quando Key Vault modelo de permissão é alterado de/para o controle de acesso baseado em função do Azure.   |

## <a name="event-examples"></a>Exemplos de eventos

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)

O exemplo a seguir mostra o esquema para **Microsoft. keyvault. SecretNewVersionCreated**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

O exemplo a seguir mostra o esquema para **Microsoft. keyvault. SecretNewVersionCreated**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "source":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "type":"Microsoft.KeyVault.SecretNewVersionCreated",
      "time":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "specversion":"1.0"
   }
]
```

---

### <a name="event-properties"></a>Propriedades do evento

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)
Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `topic` | string | Caminho de recurso completo para a origem do evento. Este campo não é gravável. A Grade de Eventos fornece esse valor. |
| `subject` | string | Caminho definido pelo publicador para o assunto do evento. |
| `eventType` | string | Um dos tipos de evento registrados para a origem do evento. |
| `eventTime` | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| `id` | string | Identificador exclusivo do evento. |
| `data` | object | Dados de evento de configuração de aplicativo. |
| `dataVersion` | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| `metadataVersion` | string | A versão do esquema dos metadados do evento. Grade de Eventos define o esquema de propriedades de nível superior. A Grade de Eventos fornece esse valor. |


# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| `source` | string | Caminho de recurso completo para a origem do evento. Este campo não é gravável. A Grade de Eventos fornece esse valor. |
| `subject` | string | Caminho definido pelo publicador para o assunto do evento. |
| `type` | string | Um dos tipos de evento registrados para a origem do evento. |
| `time` | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| `id` | string | Identificador exclusivo do evento. |
| `data` | object | Dados de evento de configuração de aplicativo. |
| `specversion` | string | Versão de especificação de esquema CloudEvents. |

---
 

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| ---------- | ----------- |---|
| `id` | string | A ID do objeto que disparou este evento |
| `vaultName` | string | O nome do cofre de chaves do objeto que disparou este evento |
| `objectType` | string | O tipo do objeto que disparou este evento |
| `objectName` | string | O nome do objeto que disparou este evento |
| `version` | string | A versão do objeto que disparou este evento |
| `nbf` | número | A data de não-antes, em segundos, desde 1970-01-01T00:00:00Z do objeto que disparou este evento |
| `exp` | número | A data de validade em segundos desde 1970-01-01T00:00:00Z do objeto que disparou este evento |

## <a name="tutorials-and-how-tos"></a>Tutoriais e instruções
|Título  |Descrição  |
|---------|---------|
| [Monitorando Key Vault eventos com a grade de eventos do Azure](../key-vault/general/event-grid-overview.md) | Visão geral da integração de Key Vault com a grade de eventos. |
| [Tutorial: criar e monitorar eventos de Key Vault com a grade de eventos](../key-vault/general/event-grid-logicapps.md) | Saiba como configurar notificações de grade de eventos para Key Vault. |


## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à grade de eventos do Azure, consulte [o que é a grade de eventos?](overview.md).
* Para obter mais informações sobre como criar uma assinatura da grade de eventos do Azure, consulte [esquema de assinatura da grade de eventos](subscription-creation-schema.md).
* Para obter mais informações sobre Key Vault, consulte [o que é Azure Key Vault?](../key-vault/general/overview.md)

