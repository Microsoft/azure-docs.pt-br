---
author: baanders
description: arquivo de inclusão para opções de filtro de rota de gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 12/04/2020
ms.author: baanders
ms.openlocfilehash: d93f484e318c10489eb1db3e9c65c6e0c7479c90
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98859831"
---
| Nome do filtro | Descrição | Filtrar esquema de texto | Valores com suporte | 
| --- | --- | --- | --- |
| Verdadeiro/falso | Permite criar uma rota sem filtragem ou desabilitar uma rota para que nenhum evento seja enviado | `<true/false>` | `true` = a rota está habilitada sem filtragem <br> `false` = a rota está desabilitada |
| Tipo | O [tipo de evento](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) que flui pela sua instância de cópia digital | `type = '<eventType>'` | Estes são os valores de tipo de evento possíveis: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Fonte | Nome da instância do gêmeos digital do Azure | `source = '<hostname>'`| Estes são os possíveis valores de nome de host: <br> **Para notificações**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net` <br> **Para telemetria**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>`|
| Assunto | Uma descrição do evento no contexto da origem do evento acima | `subject = '<subject>'` | Estes são os possíveis valores de assunto: <br>**Para notificações**: o assunto é `<twinid>` <br> ou um formato de URI para entidades, que são identificadas exclusivamente por várias partes ou IDs:<br>`<twinid>/relationships/<relationshipid>`<br> **Para telemetria**: o assunto é o caminho do componente (se a telemetria for emitida de um componente de entrelaçamento), como `comp1.comp2` . Se a telemetria não for emitida de um componente, o campo assunto estará vazio. |
| Esquema de dados | ID do modelo DTDL | `dataschema = '<model-dtmi-ID>'` | **Para telemetria**: o esquema de dados é a ID do modelo de a/ou o componente que emite a telemetria. Por exemplo, `dtmi:example:com:floor4;2` <br>**Para notificações (criar/excluir)**: o esquema de dados pode ser acessado no corpo da notificação em `$body.$metadata.$model` . <br>**Para notificações (atualização)**: o esquema de dados pode ser acessado no corpo da notificação em `$body.modelId`|
| Tipo de conteúdo | Tipo de conteúdo do valor de dados | `datacontenttype = '<contentType>'` | O tipo de conteúdo é `application/json` |
| Versão de especificação | A versão do esquema de evento que você está usando | `specversion = '<version>'` | A versão deve ser `1.0` . Isso indica o esquema CloudEvents versão 1,0 |
| Corpo da notificação | Referenciar qualquer propriedade no `data` campo de uma notificação | `$body.<property>` | Consulte [*como: entender os dados do evento*](../articles/digital-twins/how-to-interpret-event-data.md) para obter exemplos de notificações. Qualquer propriedade no `data` campo pode ser referenciada usando `$body`

Os tipos de dados a seguir têm suporte como valores retornados por referências aos dados acima:

| Tipo de dados | Exemplo |
|-|-|-|
|**Cadeia de caracteres**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**Inteiro**|`$body.errorCode > 200`|
|**Double**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**Nulo**|`$body.prop != null`|

Os seguintes operadores têm suporte ao definir filtros de rota:

|Família|Operadores|Exemplo|
|-|-|-|
|Lógico|AND, OR, ()|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|Comparação|<, <=, >, >=, =,! =|`$body.temperature <= 5.5`

As funções a seguir têm suporte ao definir filtros de rota:

|Função|Descrição|Exemplo|
|--|--|--|
|STARTS_WITH (x, y)|Retornará true se o valor `x` começar com a cadeia de caracteres `y` .|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH (x, y) | Retornará true se o valor `x` terminar com a cadeia de caracteres `y` .|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| Retornará true se o valor `x` contiver a cadeia de caracteres `y` .|`CONTAINS(subject, '<twinID>')`|

Quando você implementa ou atualiza um filtro, a alteração pode levar alguns minutos para ser refletida no pipeline de dados.