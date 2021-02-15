---
title: Referência de API do Hub IoT do Azure MQTT 5 (versão prévia)
description: Saiba mais sobre a referência de API do MQTT 5 do Hub IoT
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603150"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>Referência de API do plano de dados do Hub IoT MQTT 5

Este documento define as operações disponíveis na versão 2,0 (API-Version: `2020-10-01-preview` ) da API do plano de dados do Hub IOT.

## <a name="operations"></a>Operações

### <a name="get-twin"></a>Colocar o entrelaçado

Obter estado de entrelaçamento

#### <a name="request"></a>Solicitação

**Nome do tópico:**`$iothub/twin/get`

**Propriedades**: nenhuma

**Carga**: vazia

#### <a name="success-response"></a>Resposta de êxito

**Propriedades**: nenhuma

**Carga**: "/"

#### <a name="alternative-responses"></a>Respostas alternativas

| Status | Nome | Descrição |
| :----- | :--- | :---------- |
| 0100 |  Solicitação incorreta | A mensagem da operação está malformada e não pode ser processada. |
| 0101 |  Não autorizado | O cliente não está autorizado a executar a operação. |
| 0102 |  Não Permitido | A operação não é permitida. |
| 0501 |  Acelerado | a taxa de solicitação é muito alta por SKU |
| 0502 |  Cota Excedida | a cota diária por SKU atual foi excedida |
| 0601 |  Erro de servidor | erro interno do servidor |
| 0602 |  Tempo limite | a operação atingiu o tempo limite antes de ser concluída |
| 0603 |  Servidor ocupado | servidor ocupado |

#### <a name="pseudo-code-sample"></a>Exemplo de pseudocódigo

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="patch-twin-reported"></a>Patch de atualização de porta

Estado relatado do patch "s"

#### <a name="request"></a>Solicitação

**Nome do tópico:**`$iothub/twin/patch/reported`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| If-Version | u64 | no |  |

**Payload**:

#### <a name="success-response"></a>Resposta de êxito

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| version | u64 | sim | Versão do estado relatado após a aplicação do patch |

**Carga**: vazia

#### <a name="alternative-responses"></a>Respostas alternativas

| Status | Nome | Descrição |
| :----- | :--- | :---------- |
| 0104 |  Falha na Pré-condição | a pré-condição não foi atendida, resultando na cancelamento da solicitação |
| 0100 |  Solicitação incorreta | A mensagem da operação está malformada e não pode ser processada. |
| 0101 |  Não autorizado | O cliente não está autorizado a executar a operação. |
| 0102 |  Não Permitido | A operação não é permitida. |
| 0501 |  Acelerado | a taxa de solicitação é muito alta por SKU |
| 0502 |  Cota Excedida | a cota diária por SKU atual foi excedida |
| 0601 |  Erro de servidor | erro interno do servidor |
| 0602 |  Tempo limite | a operação atingiu o tempo limite antes de ser concluída |
| 0603 |  Servidor ocupado | servidor ocupado |

#### <a name="pseudo-code-sample"></a>Exemplo de pseudocódigo

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-commands"></a>Receber comandos

Receber e manipular comandos

#### <a name="message"></a>Mensagem

**Nome do tópico:**`$iothub/commands`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| sequência-não | u64 | sim | Número de sequência da mensagem |
| em tempo de enfileiramento | time | sim | Carimbo de data/hora de quando a mensagem entrou no sistema |
| Contagem de entrega | u32 | sim | Número de vezes que a entrega da mensagem foi tentada |
| creation-time | time | no | Carimbo de data/hora de quando a mensagem foi criada (fornecida pelo remetente) |
| message-id | string | no | Identidade da mensagem (fornecida pelo remetente) |
| user-id | string | no | Identidade do usuário (fornecida pelo remetente) |
| correlation-id | string | no | Identidade de correlação (fornecida pelo remetente) |
| Tipo de conteúdo | string | no | determina o tipo de conteúdo da carga |
| content-encoding | string | no | determina a codificação de conteúdo da carga |

**Carga**: qualquer sequência de bytes

#### <a name="success-acknowledgment"></a>Confirmação de sucesso

Indica que o comando foi aceito para manipulação pelo cliente

**Propriedades**: nenhuma

**Carga**: vazia

#### <a name="alternative-acknowledgments"></a>Confirmações alternativas

| Código de motivo | Status | Nome | Descrição |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | Abandonar | Indica que o comando não será processado neste momento e deverá ser entregue novamente no futuro. |
| 131 | 0100 | Rejeitar | Indica que o comando foi rejeitado pelo cliente e não deve ser tentado novamente. |

#### <a name="pseudo-code-sample"></a>Exemplo de pseudocódigo

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### <a name="receive-direct-methods"></a>Receber métodos diretos

Receber e tratar chamadas de método diretas

#### <a name="request"></a>Solicitação

**Nome do tópico:**`$iothub/methods/{name}`

**Propriedades**: nenhuma

**Carga**: qualquer sequência de bytes

#### <a name="success-response"></a>Resposta de êxito

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| código de resposta | u32 | sim |  |

**Carga**: qualquer sequência de bytes

#### <a name="alternative-responses"></a>Respostas alternativas

| Status | Nome | Descrição |
| :----- | :--- | :---------- |
| 06A0 |  Indisponível | Indica que o cliente não está acessível por essa conexão. |

#### <a name="pseudo-code-sample"></a>Exemplo de pseudocódigo

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-twin-desired-state-changes"></a>Receber alterações de estado desejado de entrelaçamento

Receber atualizações para o estado desejado do entrelaçamento

#### <a name="message"></a>Mensagem

**Nome do tópico:**`$iothub/twin/patch/desired`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| version | u64 | sim | Versão do estado desejado correspondente a esta atualização |

**Payload**:

#### <a name="pseudo-code-sample"></a>Exemplo de pseudocódigo

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### <a name="send-telemetry"></a>Enviar telemetria

Poste a mensagem no canal de telemetria-EventHubs por padrão ou outro ponto de extremidade por meio da configuração de roteamento.

#### <a name="message"></a>Mensagem

**Nome do tópico:**`$iothub/telemetry`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| Tipo de conteúdo | string | no | traduz na `content-type` Propriedade do sistema na mensagem postada |
| content-encoding | string | no | traduz na `content-encoding` Propriedade do sistema na mensagem postada |
| message-id | string | no | traduz na `message-id` Propriedade do sistema na mensagem postada |
| user-id | string | no | traduz na `user-id` Propriedade do sistema na mensagem postada |
| correlation-id | string | no | traduz na `correlation-id` Propriedade do sistema na mensagem postada |
| creation-time | time | no | traduz em `iothub-creation-time-utc` Propriedade na mensagem postada |

**Carga**: qualquer sequência de bytes

#### <a name="success-acknowledgment"></a>Confirmação de sucesso

A mensagem foi postada com êxito no canal de telemetria

**Propriedades**: nenhuma

**Carga**: vazia

#### <a name="alternative-acknowledgments"></a>Confirmações alternativas

| Código de motivo | Status | Nome | Descrição |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | Solicitação incorreta | A mensagem da operação está malformada e não pode ser processada. |
| 135 | 0101 | Não autorizado | O cliente não está autorizado a executar a operação. |
| 131 | 0102 | Não Permitido | A operação não é permitida. |
| 131 | 0601 | Erro de servidor | erro interno do servidor |
| 151 | 0501 | Acelerado | a taxa de solicitação é muito alta por SKU |
| 151 | 0502 | Cota Excedida | a cota diária por SKU atual foi excedida |
| 131 | 0602 | Tempo limite | a operação atingiu o tempo limite antes de ser concluída |
| 131 | 0603 | Servidor ocupado | servidor ocupado |

#### <a name="pseudo-code-sample"></a>Exemplo de pseudocódigo

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## <a name="responses"></a>Respostas

### <a name="bad-request"></a>Solicitação incorreta

A mensagem da operação está malformada e não pode ser processada.

**Código do motivo:**`131`

**Status:**`0100`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contém informações sobre o que especificamente não é válido sobre a mensagem |

**Carga**: vazia

### <a name="conflict"></a>Conflito

A operação está em conflito com outra operação em andamento.

**Código do motivo:**`131`

**Status:**`0103`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| ID do rastreamento | string | no | ID de rastreamento para correlação com diagnóstico adicional para o erro |
| reason | string | no | contém informações sobre o que especificamente não é válido sobre a mensagem |

**Carga**: vazia

### <a name="not-allowed"></a>Não Permitido

A operação não é permitida.

**Código do motivo:**`131`

**Status:**`0102`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contém informações sobre o que especificamente não é válido sobre a mensagem |

**Carga**: vazia

### <a name="not-authorized"></a>Não autorizado

O cliente não está autorizado a executar a operação.

**Código do motivo:**`135`

**Status:**`0101`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| ID do rastreamento | string | no | ID de rastreamento para correlação com diagnóstico adicional para o erro |

**Carga**: vazia

### <a name="not-found"></a>Não encontrado

o recurso solicitado não existe

**Código do motivo:**`131`

**Status:**`0504`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contém informações sobre o que especificamente não é válido sobre a mensagem |

**Carga**: vazia

### <a name="not-modified"></a>Não modificado

O recurso não foi modificado com base na pré-condição fornecida.

**Código do motivo:**`0`

**Status:**`0001`

**Propriedades**: nenhuma

**Carga**: vazia

### <a name="precondition-failed"></a>Falha na Pré-condição

A pré-condição não foi atendida, resultando na cancelamento da solicitação

**Código do motivo:**`131`

**Status:**`0104`

**Propriedades**: nenhuma

**Carga**: vazia

### <a name="quota-exceeded"></a>Cota Excedida

a cota diária por SKU atual foi excedida

**Código do motivo:**`151`

**Status:**`0502`

**Propriedades**: nenhuma

**Carga**: vazia

### <a name="resource-exhausted"></a>Recurso esgotado

o recurso não tem capacidade para concluir a operação

**Código do motivo:**`131`

**Status:**`0503`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contém informações sobre o que especificamente não é válido sobre a mensagem |

**Carga**: vazia

### <a name="server-busy"></a>Servidor ocupado

servidor ocupado

**Código do motivo:**`131`

**Status:**`0603`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| ID do rastreamento | string | no | ID de rastreamento para correlação com diagnóstico adicional para o erro |

**Carga**: vazia

### <a name="server-error"></a>Erro de servidor

erro interno do servidor

**Código do motivo:**`131`

**Status:**`0601`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| ID do rastreamento | string | no | ID de rastreamento para correlação com diagnóstico adicional para o erro |

**Carga**: vazia

### <a name="target-failed"></a>Falha no destino

O destino respondeu, mas a resposta era inválida ou malformada

**Código do motivo:**`131`

**Status:**`06A2`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| reason | string | no | contém informações sobre o que especificamente não é válido sobre a mensagem |

**Carga**: vazia

### <a name="target-timeout"></a>Tempo limite de destino

tempo limite excedido ao aguardar o destino concluir a solicitação

**Código do motivo:**`131`

**Status:**`06A1`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| ID do rastreamento | string | no | ID de rastreamento para correlação com diagnóstico adicional para o erro |
| reason | string | no | contém informações sobre o que especificamente não é válido sobre a mensagem |

**Carga**: vazia

### <a name="target-unavailable"></a>Destino não disponível

O destino está inacessível para concluir a solicitação

**Código do motivo:**`131`

**Status:**`06A0`

**Propriedades**: nenhuma

**Carga**: vazia

### <a name="throttled"></a>Acelerado

a taxa de solicitação é muito alta por SKU

**Código do motivo:**`151`

**Status:**`0501`

**Propriedades**: nenhuma

**Carga**: vazia

### <a name="timeout"></a>Tempo limite

a operação atingiu o tempo limite antes de ser concluída

**Código do motivo:**`131`

**Status:**`0602`

**Propriedades**:

| Nome | Type | Obrigatório | Descrição |
| :--- | :--- | :------- | :---------- |
| ID do rastreamento | string | no | ID de rastreamento para correlação com diagnóstico adicional para o erro |

**Carga**: vazia

