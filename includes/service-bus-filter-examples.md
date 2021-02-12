---
title: incluir arquivo
description: incluir arquivo
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 01/22/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ddc0234accd9f434aad850d2404e2f3001c4bae
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742669"
---
## <a name="examples"></a>Exemplos

### <a name="filter-on-system-properties"></a>Filtrar nas propriedades do sistema
Para fazer referência a uma propriedade do sistema em um filtro, use o seguinte formato: `sys.<system-property-name>` . 

```csharp
sys.Label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

### <a name="filter-on-message-properties"></a>Filtrar nas propriedades da mensagem
Aqui estão os exemplos de como usar as propriedades de mensagem em um filtro. Você pode acessar as propriedades da mensagem usando `user.property-name` ou apenas `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

### <a name="filter-on-message-properties-with-special-characters"></a>Filtrar as propriedades da mensagem com caracteres especiais
Se o nome da propriedade da mensagem tiver caracteres especiais, use aspas duplas ( `"` ) para incluir o nome da propriedade. Por exemplo, se o nome da propriedade for `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` , use a sintaxe a seguir no filtro. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

### <a name="filter-on-message-properties-with-numeric-values"></a>Filtrar as propriedades da mensagem com valores numéricos
Os exemplos a seguir mostram como você pode usar propriedades com valores numéricos em filtros. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

### <a name="parameter-based-filters"></a>Filtros baseados em parâmetros
Aqui estão alguns exemplos de como usar filtros baseados em parâmetros. Nesses exemplos, `DataTimeMp` é uma propriedade Message do tipo `DateTime` e `@dtParam` é um parâmetro passado para o filtro como um `DateTime` objeto.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

### <a name="using-in-and-not-in"></a>Usando IN e NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Para obter um exemplo em C#, consulte o [tópico filtros de exemplo no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


### <a name="set-rule-action-for-a-sql-filter"></a>Definir ação de regra para um filtro SQL

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="correlation-filter-using-correlationid"></a>Filtro de correlação usando CorrelationId

```csharp
new CorrelationFilter("Contoso");
```

Ele filtra mensagens com `CorrelationID` definido como `Contoso` . 

### <a name="correlation-filter-using-system-and-user-properties"></a>Filtro de correlação usando propriedades do sistema e do usuário

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

É equivalente a: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

