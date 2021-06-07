---
title: Conceitos, termos e entidades
description: Aprenda os conceitos, a terminologia e a hierarquia de entidades, incluindo trabalhos e coleções de trabalhos, no Agendador do Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 08/18/2016
ms.openlocfilehash: 899c64e818896cde18e955d6abd82594734c4b57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368155"
---
# <a name="concepts-terminology-and-entities-in-azure-scheduler"></a>Conceitos, terminologia e entidades do Agendador do Azure

> [!IMPORTANT]
> Os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) estão substituindo o Agendador do Azure, que está [sendo desativado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar trabalhando com os trabalhos que você configurou no Agendador, [migre para o aplicativo lógico do Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) assim que possível. 
>
> O Agendador não está mais disponível no portal do Azure, mas a [API REST](/rest/api/scheduler) e os [cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md) permanecem disponíveis no momento para que você possa gerenciar seus trabalhos e suas coleções de trabalhos.

## <a name="entity-hierarchy"></a>Hierarquia de entidades

A API REST do Agendador do Azure expõe e usa essas entidades principais, ou recursos:

| Entidade | Descrição |
|--------|-------------|
| **Trabalho** | Define uma única ação recorrente com estratégias simples ou complexas para execução. As ações podem incluir solicitações HTTP, de fila de armazenamento, de fila de Barramento de Serviço ou de tópico do Barramento de Serviço. | 
| **Coleção de trabalhos** | Contém um grupo de trabalhos e mantém as configurações, cotas e limites que são compartilhados pelos trabalhos na coleção. Como proprietário de uma assinatura do Azure, você pode criar coleções de trabalhos e trabalhos de grupo com base em limites de aplicativo ou uso. Uma coleção de trabalhos tem estes atributos: <p>– Restrita a uma região. <br>– Permite que você imponha cotas para que possa restringir o uso de todos os trabalhos em uma coleção. <br>– As cotas incluem MaxJobs e MaxRecurrence. | 
| **Histórico de trabalho** | Descreve os detalhes para a execução de um trabalho, por exemplo, o status e os detalhes da resposta. |
||| 

## <a name="entity-management"></a>Gerenciamento de entidade

Em um alto nível, a API REST do Agendador expõe essas operações para gerenciar as entidades.

### <a name="job-management"></a>Gerenciamento de trabalho

Compatível com operações para criar e editar trabalhos. Todos os trabalhos devem pertencer a uma coleção de trabalhos existente, para que não haja criação implícita. Para obter mais informações, veja [API REST do Agendador – Trabalhos](/rest/api/scheduler/jobs). Aqui está o endereço URI para essas operações:

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}
```

### <a name="job-collection-management"></a>Gerenciamento de coleção de trabalhos

Compatível com operações para criar e editar trabalhos e coleções de trabalhos, que são mapeados para cotas e configurações compartilhadas. Por exemplo, as cotas especificam o número máximo de trabalhos e o menor intervalo de recorrência. Para obter mais informações, veja [API REST do Agendador – Coleções de trabalhos](/rest/api/scheduler/jobcollections). Aqui está o endereço URI para essas operações:

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}
```

### <a name="job-history-management"></a>Gerenciamento de histórico de trabalhos

Compatível com a operação GET para buscar 60 dias de histórico de execução do trabalho, por exemplo, o tempo decorrido do trabalho e os resultados da execução do trabalho. Inclui o suporte ao parâmetro de cadeia de caracteres consulta para filtrar com base no estado e status. Para obter mais informações, veja [API REST do Agendador – Trabalhos – Histórico de trabalhos de lista](/rest/api/scheduler/jobs/listjobhistory). Aqui está o endereço URI para esta operação:

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history
```

## <a name="job-types"></a>Tipos de trabalho

O Agendador do Azure é compatível com vários tipos de trabalho: 

* Trabalhos HTTP, incluindo trabalhos HTTPS que dão suporte a TLS, para quando você tem o ponto de extremidade para um serviço ou carga de trabalho existente
* Trabalhos de fila de armazenamento para cargas de trabalho que usam filas de armazenamento, como o envio de mensagens para filas de armazenamento
* Trabalhos de fila do Barramento de Serviço para cargas de trabalho que usam filas do Barramento de Serviço
* Trabalhos de tópico do Barramento de Serviço para cargas de trabalho que usam tópicos do Barramento de Serviço

## <a name="job-definition"></a>Definição de trabalho

Em alto nível, um trabalho do Agendador tem essas partes básicas:

* A ação executada quando o temporizador do trabalho é disparado
* Opcional: o tempo para executar o trabalho
* Opcional: quando e com que frequência repetir o trabalho
* Opcional: uma ação de erro que é executada se a ação principal falha

O trabalho também inclui os dados fornecidos pelo sistema, como tempo de execução agendada do próximo do trabalho. A definição do código desse trabalho é um objeto no formato JSON (JavaScript Object Notation), que tem estes elementos:

| Elemento | Obrigatório | Descrição | 
|---------|----------|-------------| 
| [**startTime**](#start-time) | Não | A hora de início do trabalho com um deslocamento de fuso horário em [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) | 
| [**action**](#action) | Sim | Os detalhes para a ação principal, que podem incluir um objeto **errorAction** | 
| [**errorAction**](#error-action) | Não | Os detalhes para a ação secundária que é executada se a ação principal falha |
| [**Recurrence**](#recurrence) | Não | Os detalhes, como a frequência e o intervalo para um trabalho recorrente | 
| [**retryPolicy**](#retry-policy) | Não | Os detalhes de quantas vezes repetir uma ação | 
| [**state**](#state) | Sim | Os detalhes do estado atual do trabalho |
| [**Estado**](#status) | Sim | Os detalhes do status atual do trabalho, que é controlado pelo serviço |
||||

Aqui está um exemplo que mostra uma definição de trabalho abrangente para uma ação HTTP com detalhes de elemento mais completos descritos nas seções posteriores: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z",
   "action": {
      "type": "Http",
      "request": {
         "uri": "http://contoso.com/some-method", 
         "method": "PUT",          
         "body": "Posting from a timer",
         "headers": {
            "Content-Type": "application/json"
         },
         "retryPolicy": { 
             "retryType": "None" 
         },
      },
      "errorAction": {
         "type": "Http",
         "request": {
            "uri": "http://contoso.com/notifyError",
            "method": "POST"
         }
      }
   },
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]
      },
      "count": 10,
      "endTime": "2012-11-04"
   },
   "state": "Disabled",
   "status": {
      "lastExecutionTime": "2007-03-01T13:00:00Z",
      "nextExecutionTime": "2007-03-01T14:00:00Z ",
      "executionCount": 3,
      "failureCount": 0,
      "faultedCount": 0
   }
}
```

<a name="start-time"></a>

## <a name="starttime"></a>startTime

No objeto **startTime**, você pode especificar a hora de início e um deslocamento de fuso horário no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).

<a name="action"></a>

## <a name="action"></a>ação

Seu trabalho do Agendador executa uma **action** primária com base no agendamento especificado. O Agendador é compatível com ações HTTP, de fila de armazenamento, de tópico do Barramento de Serviço e de fila do Barramento de Serviço. Se a **action** primária falha, o Agendador pode executar uma [**errorAction**](#erroraction) secundária que cuida do erro. O objeto **action** descreve estes elementos:

* O tipo de serviço da ação
* Os detalhes da ação
* Uma **errorAction** alternativa

O exemplo anterior descreve uma ação HTTP. Aqui está um exemplo de uma ação de fila de armazenamento:

```json
"action": {
   "type": "storageQueue",
   "queueMessage": {
      "storageAccount": "myStorageAccount",  
      "queueName": "myqueue",                
      "sasToken": "TOKEN",                   
      "message": "My message body"
    }
}
```

Aqui está um exemplo de uma ação de fila do Barramento de Serviço:

```json
"action": {
   "type": "serviceBusQueue",
   "serviceBusQueueMessage": {
      "queueName": "q1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {  
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",  
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Aqui está um exemplo de uma ação de tópico do Barramento de Serviço:

```json
"action": {
   "type": "serviceBusTopic",
   "serviceBusTopicMessage": {
      "topicPath": "t1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Para obter mais informações sobre os tokens de SAS (Assinatura de Acesso Compartilhado), consulte [Autorizar com Assinaturas de Acesso Compartilhado](../storage/common/storage-sas-overview.md).

<a name="error-action"></a>

## <a name="erroraction"></a>errorAction

Se a **action** primária do trabalho falha, o Agendador pode executar um **errorAction** que cuida do erro. Na **action** primária, você pode especificar um objeto **errorAction** para que o Agendador possa chamar um ponto de extremidade de tratamento de erros ou enviar uma notificação de usuário. 

Por exemplo, se ocorrer um desastre no ponto de extremidade primário, você poderá usar **errorAction** para chamar um ponto de extremidade secundário ou para notificar um ponto de extremidade de tratamento de erro. 

Assim como **action** primária, você pode fazer com que a ação de erro use a lógica simples ou composta com base em outras ações. 

<a name="recurrence"></a>

## <a name="recurrence"></a>recurrence

Um trabalho se repetirá se a definição do JSON do trabalho incluir o objeto **recurrence**, por exemplo:

```json
"recurrence": {
   "frequency": "Week",
   "interval": 1,
   "schedule": {
      "hours": [10, 22],
      "minutes": [0, 30],
      "weekDays": ["Monday", "Wednesday", "Friday"]
   },
   "count": 10,
   "endTime": "2012-11-04"
},
```

| Propriedade | Obrigatório | Valor | Descrição | 
|----------|----------|-------|-------------| 
| **frequência** | Sim, quando **recurrence** é usado | "Minute", "Hour", "Day", "Week", "Month", "Year" | A unidade de tempo entre ocorrências | 
| **intervalo** | Não | 1 a 1000, inclusive | Um inteiro positivo que determina o número de unidades de tempo entre cada ocorrência com base em **frequency** | 
| **agendamento** | Não | Varia | Os detalhes de agendamentos mais avançados e complexos. Veja **hours**, **minutes**, **weekDays**, **months** e **monthDays** | 
| **duração** | Não | 1 a 24 | Uma matriz com as marcas de hora para quando executar o trabalho | 
| **minutes** | Não | 0 a 59 | Uma matriz com as marcas de minutos para quando executar o trabalho | 
| **months** | Não | 1 a 12 | Uma matriz com os meses para quando executar o trabalho | 
| **monthDays** | Não | Varia | Uma matriz com os dias do mês para quando executar o trabalho | 
| **Dias da semana** | Não | "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday" | Uma matriz com os dias da semana para quando executar o trabalho | 
| **contagem** | Não | <*None*> | O número de recorrências. O padrão é repetir indefinidamente. Não é possível usar **count** e **endTime**, mas a regra que termina primeiro é respeitada. | 
| **Final** | Não | <*None*> | A data e hora para quando interromper a recorrência. O padrão é repetir indefinidamente. Não é possível usar **count** e **endTime**, mas a regra que termina primeiro é respeitada. | 
||||

Para obter mais informações sobre esses elementos, consulte [Criar agendamentos complexos e recorrências avançadas](../scheduler/scheduler-advanced-complexity.md).

<a name="retry-policy"></a>

## <a name="retrypolicy"></a>retryPolicy

Para o caso quando um trabalho do Agendador falhar, você pode configurar uma política de repetição, que determina se e como o Agendador repete a ação. Por padrão, o Agendador tenta o trabalho novamente mais quatro vezes, em intervalos de 30 segundos. Você pode fazer com que essa política seja mais ou menos agressiva, por exemplo, essa política tentar novamente uma ação duas vezes por dia:

```json
"retryPolicy": { 
   "retryType": "Fixed",
   "retryInterval": "PT1D",
   "retryCount": 2
},
```

| Propriedade | Obrigatório | Valor | Descrição | 
|----------|----------|-------|-------------| 
| **retryType** | Sim | **Fixed**, **None** | Determina se você especifica uma política de repetição (**fixed**) ou não (**none**). | 
| **retryInterval** | Não | PT30S | Especifica o intervalo e a frequência entre as tentativas de repetição no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). O valor mínimo é 15 segundos, enquanto o valor máximo é 18 meses. | 
| **retryCount** | Não | 4 | Especifica o número de tentativas de repetição. O valor máximo é 20. | 
||||

Para obter mais informações, consulte [Alta disponibilidade e confiabilidade](../scheduler/scheduler-high-availability-reliability.md).

<a name="status"></a>

## <a name="state"></a>state

O estado de um trabalho é **Enabled**, **Disabled**, **Completed** ou **Faulted**, por exemplo: 

`"state": "Disabled"`

Para alterar os trabalhos para o estado **Enabled** ou **Disabled**, você pode usar a operação PUT ou PATCH nesses trabalhos.
No entanto, se um trabalho tiver o estado **Completed** ou **Faulted**, não será possível atualizar o estado, embora você possa executar a operação DELETE no trabalho. O Agendador exclui os trabalhos concluídos ou com falha após 60 dias. 

<a name="status"></a>

## <a name="status"></a>status

Depois que um trabalho é iniciado, o Agendador retorna informações sobre o status do trabalho por meio do objeto **status**, que apenas o Agendador controla. No entanto, você pode encontrar o objeto **status** dentro do objeto **job**. Aqui estão as informações que o status do trabalho incluem:

* Hora da execução anterior, se houver
* Hora da próxima execução agendada para trabalhos em andamento
* O número de execuções de trabalho
* O número de falhas, se houver
* O número de falhas, se houver

Por exemplo:

```json
"status": {
   "lastExecutionTime": "2007-03-01T13:00:00Z",
   "nextExecutionTime": "2007-03-01T14:00:00Z ",
   "executionCount": 3,
   "failureCount": 0,
   "faultedCount": 0
}
```

## <a name="next-steps"></a>Próximas etapas

* [Criar agendamentos complexos e recorrência avançada](scheduler-advanced-complexity.md)
* [Referência da API REST do Agendador do Azure](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)
* [Limites, cotas, valores padrão e códigos de erro](scheduler-limits-defaults-errors.md)