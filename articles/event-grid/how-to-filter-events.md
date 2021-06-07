---
title: Como filtrar eventos para o Azure Event Grid
description: Este artigo mostra como filtrar eventos (por tipo de evento, por assunto, por operadores e dados, etc.) ao criar uma assinatura de grade de eventos.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 99fb00f99a055033ccfcd99e32a52d423878fb44
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86105550"
---
# <a name="filter-events-for-event-grid"></a>Filtrar eventos de grade de eventos

Este artigo mostra como filtrar eventos ao criar uma assinatura de grade de eventos. Para saber mais sobre as opções de filtragem de eventos, consulte [Entender a filtragem de eventos para assinaturas de grade de eventos](event-filtering.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filtrar por tipo de evento

Ao criar uma assinatura da Grade de Eventos, você pode especificar quais [tipos de eventos](event-schema.md) devem enviar para o terminal. Os exemplos nesta seção criam assinaturas de eventos para um grupo de recursos, mas limitam os eventos que são enviados para `Microsoft.Resources.ResourceWriteFailure` e `Microsoft.Resources.ResourceWriteSuccess`. Se você precisar de mais flexibilidade ao filtrar eventos pelo tipo, consulte Filtrar por operadores avançados e campos de dados.

Para o PowerShell, use o parâmetro `-IncludedEventType` ao criar a assinatura.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

CLI do Azure, use o `--included-event-types` parâmetro. O exemplo a seguir usa a CLI do Azure em um shell Bash:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Para um modelo do Resource Manager, use a propriedade `includedEventTypes`.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Filtrar por assunto

Você pode filtrar eventos pelo assunto nos dados do evento. Você pode especificar um valor para corresponder ao início ou fim do assunto. Se você precisar de mais flexibilidade ao filtrar eventos por assunto, consulte Filtrar por operadores avançados e campos de dados.

No seguinte exemplo do PowerShell, você cria uma inscrição de evento que filtra no início do assunto. Você usa o parâmetro `-SubjectBeginsWith` para limitar eventos aos de um recurso específico. Você passa o ID do recurso de um grupo de segurança de rede.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

O próximo exemplo do PowerShell cria uma assinatura para um armazenamento de blobs. Ele limita eventos para aqueles com um assunto que termina em `.jpg`.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

No seguinte exemplo de CLI do Azure, você cria uma inscrição de evento que filtra no início do assunto. Você usa o parâmetro `--subject-begins-with` para limitar eventos aos de um recurso específico. Você passa o ID do recurso de um grupo de segurança de rede.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

O próximo exemplo de CLI do Azure cria uma inscrição para um armazenamento de blobs. Ele limita eventos para aqueles com um assunto que termina em `.jpg`.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

No seguinte exemplo de modelo do Resource Manager, você cria uma inscrição de evento que filtra no início do assunto. Você usa a propriedade `subjectBeginsWith` para limitar eventos aos de um recurso específico. Você passa o ID do recurso de um grupo de segurança de rede.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

O próximo exemplo de modelo do Resource Manager cria uma assinatura para um armazenamento de blobs. Ele limita eventos para aqueles com um assunto que termina em `.jpg`.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Filtrar por dados e operadores

Para obter mais flexibilidade na filtragem, você pode usar os operadores e as propriedades de dados para filtrar eventos.

### <a name="subscribe-with-advanced-filters"></a>Inscreva-se com filtros avançados

Para saber mais sobre os operadores e chaves que você pode usar para filtragem avançada, consulte [Filtragem avançada](event-filtering.md#advanced-filtering).

Esses exemplos criam um tópico personalizado. Eles assinam o tópico personalizado e filtram por um valor no objeto de dados. Os eventos que possuem a propriedade color definida como azul, vermelha ou verde são enviados para a assinatura.

Para a CLI do Azure, use:

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

Observe que uma [data de expiração](concepts.md#event-subscription-expiration) está definida para a assinatura.

Para o PowerShell, use:

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Filtro de teste

Para testar o filtro, envie um evento com o campo de cores definido como verde. Porque verde é um dos valores no filtro, o evento é entregue ao ponto de extremidade.

Para a CLI do Azure, use:

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Para o PowerShell, use:

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Para testar um cenário em que o evento não é enviado, envie um evento com o campo de cores definido como amarelo. Amarelo não é um dos valores especificados na assinatura, portanto, o evento não é entregue à sua assinatura.

Para a CLI do Azure, use:

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
Para o PowerShell, use:

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o monitoramento de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Para saber mais sobre a chave de autenticação, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
