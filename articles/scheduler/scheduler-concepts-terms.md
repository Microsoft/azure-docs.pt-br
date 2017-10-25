---
title: Conceitos, termos e entidades do Agendador | Microsoft Docs
description: "Conceitos, terminologia e hierarquia de entidades do Agendador do Azure, incluindo trabalhos e coleções de trabalhos.  Fornece um exemplo completo de um trabalho agendado."
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 0f035b58ccd140a5481703df7e184206da2ed651
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>Conceitos, terminologia e hierarquia de entidades do Agendador
## <a name="scheduler-entity-hierarchy"></a>Hierarquia de entidade do Agendador
A tabela a seguir descreve os principais recursos expostos ou usados pela API do Agendador:

| Recurso | Descrição |
| --- | --- |
| **Coleção de trabalhos** |Uma coleção de trabalhos contém um grupo de trabalhos e mantém as configurações, cotas e limites que são compartilhados pelos trabalhos dentro da coleção. Uma coleção de trabalhos é criada por um proprietário de assinatura e agrupa os trabalhos com base em limites de uso ou aplicativo. Ele é restrito a uma região. Ele também permite a imposição de cotas para restringir o uso de todos os trabalhos na coleção. As cotas incluem MaxJobs e MaxRecurrence. |
| **Trabalho** |Um trabalho define uma única ação recorrente com estratégias simples ou complexas para execução. As ações podem incluir solicitações HTTP, de fila de armazenamento, de barramento de serviço ou de tópico do barramento de serviço. |
| **Histórico de trabalho** |Um histórico de trabalho representa os detalhes para a execução de um trabalho. Ele contém o êxito versus a falha, bem como os detalhes da resposta. |

## <a name="scheduler-entity-management"></a>Gerenciamento de entidade do Agendador
Em um alto nível, o Agendador e a API de gerenciamento do serviço expõem as seguintes operações nos recursos:

| Recurso | Descrição e endereço de URI |
| --- | --- |
| **Gerenciamento de coleção de trabalhos** |GET, PUT e DELETE dão suporte para criar e modificar as coleções e os trabalhos nelas contidos. Uma coleção de trabalhos é um contêiner para trabalhos, com o mapeamento para cotas e configurações compartilhadas. Os exemplos de cotas, descritos a seguir, são o número máximo de trabalhos e o menor intervalo de recorrência. <p>PUT e DELETE: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>GET: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p> |
| **Gerenciamento de trabalhos** |GET, PUT, POST, PATCH e DELETE dão suporte para criar e modificar trabalhos. Todos os trabalhos devem pertencer a uma coleção de trabalhos que já existe, para que não haja criação implícita. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p> |
| **Gerenciamento de histórico de trabalho** |Suporte de GET para busca de 60 dias do histórico de execução do trabalho, tais como, tempo decorrido do trabalho e resultados de execução do trabalho. Adiciona suporte ao parâmetro de cadeia de caracteres consulta para filtrar com base no estado e status. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p> |

## <a name="job-types"></a>Tipos de trabalho
Existem vários tipos de trabalhos: trabalhos HTTP (incluindo trabalhos HTTPS que oferecem suporte a SSL), trabalhos de fila de armazenamento, trabalhos de fila do barramento de serviço e trabalhos de tópico do barramento de serviço. Trabalhos de HTTP são ideais se você tiver um ponto de extremidade de uma carga de trabalho ou serviço existente. Você pode usar os trabalhos de fila de armazenamento para postar mensagens em filas de armazenamento, portanto, esses trabalhos são ideais para cargas de trabalho que usam filas de armazenamento. Da mesma forma, os trabalhos de barramento de serviço são ideais para as cargas de trabalho que usam tópicos e filas do barramento de serviço.

## <a name="the-job-entity-in-detail"></a>A entidade "trabalho" em detalhes
Em um nível básico, um trabalho agendado tem várias partes:

* A ação a ser executada quando o temporizador do trabalho é disparado  
* (Opcional) O tempo para executar o trabalho  
* (Opcional) Quando e com que frequência repetir o trabalho  
* (Opcional) Uma ação a ser acionada, se a ação principal falhar  

Internamente, um trabalho agendado também contém dados fornecidos pelo sistema, como o próximo tempo de execução agendado.

O código a seguir fornece um exemplo completo de um trabalho agendado. Os detalhes são fornecidos nas seções seguintes.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

Como visto no trabalho agendado no exemplo acima, uma definição de trabalho tem várias partes:

* Hora de início ("startTime")  
* Ação ("action"), que inclui a ação de erro ("errorAction")
* Recorrência ("recurrence")  
* Estado ("state")  
* Status ("status")  
* Política de novas tentativas ("retryPolicy")  

Vamos examinar cada um em detalhes:

## <a name="starttime"></a>startTime
O "startTime" é a hora de início e permite que o chamador especifique um deslocamento de fuso horário na rede no [formato ISO 8601](http://en.wikipedia.org/wiki/ISO_8601).

## <a name="action-and-erroraction"></a>action e errorAction
A “ação” é a ação invocada em cada ocorrência e descreve um tipo de invocação de serviço. A ação é o que será executado na agenda fornecida. O agendador dá suporte a ações HTTP, de fila de armazenamento, de tópico do barramento de serviço e de fila do barramento de serviço.

A ação no exemplo acima é uma ação de http. Abaixo está um exemplo de uma ação de fila de armazenamento:

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

A seguir, um exemplo de uma ação de tópico do barramento de serviço.

  "action": { "type": "serviceBusTopic", "serviceBusTopicMessage": { "topicPath": "t1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Can be either netMessaging or AMQP "authentication": { "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message", "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, }

A seguir, um exemplo de uma ação de fila do barramento de serviço:

  "action": { "serviceBusQueueMessage": { "queueName": "q1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Can be either netMessaging or AMQP "authentication": {  
        "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message",  
      "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, "type": "serviceBusQueue" }

"errorAction" é o manipulador de erro, a ação invocada quando ocorre falha na ação principal. Você pode usar essa variável para chamar um ponto de extremidade de tratamento de erros ou enviar uma notificação do usuário. Isso pode ser usado para atingir um ponto de extremidade secundário no caso em que o principal não está disponível (por exemplo, no caso de um desastre no site do ponto de extremidade) ou pode ser usado para notificar um ponto de extremidade de tratamento de erros. Assim como a ação principal, a ação de erro pode ser simples ou composta lógica com base em outras ações. Para saber como criar um token SAS, consulte [Criar e usar uma assinatura de acesso compartilhado](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="recurrence"></a>recurrence
A recorrência tem várias partes:

* Frequência: uma por minuto, hora, dia, semana, mês, ano  
* Intervalo: intervalo na frequência determinada para a recorrência  
* Agenda prescrita: especificar minutos, horas, dias da semana, meses e dias do mês da recorrência  
* Contagem: contagem de ocorrências  
* Hora de término: nenhum trabalho será executado após a hora de término especificada  

Um trabalho é recorrente se tiver um objeto recorrente especificado em sua definição JSON. Se count e endTime forem especificados, a regra de conclusão que ocorre primeiro será aplicada.

## <a name="state"></a>state
O estado do trabalho é um dos quatro valores: habilitado, desabilitado, concluído ou com falha. Você pode utilizar os recursos PUT ou PATCH nos trabalhos para atualizá-los para o estado habilitado ou desabilitado. Se um trabalho tiver sido concluído ou estiver com falha, este é um estado final que não pode ser atualizado (embora o trabalho possa ser excluído). A seguir está um exemplo da propriedade state:

        "state": "disabled", // enabled, disabled, completed, or faulted
Trabalhos concluídos e com falha são excluídos após 60 dias.

## <a name="status"></a>status
Após o início de um trabalho do Agendador,  informações sobre o status atual do trabalho serão retornadas. Esse objeto não é configurável pelo usuário – ele é definido pelo sistema. No entanto, ele está incluído no objeto de trabalho (em vez de um recurso vinculado separado) para que qualquer um possa obter facilmente o status de um trabalho.

O status do trabalho inclui o tempo de execução anterior (se houver), a hora da próxima execução agendada (para trabalhos em andamento) e a contagem de execução do trabalho.

## <a name="retrypolicy"></a>retryPolicy
Se um trabalho do Agendador falhar, é possível especificar uma política de repetição para determinar se e como a ação é repetida. Isso é determinado pelo objeto **retryType** – será definido como **nenhum** se não houver nenhuma política de repetição, conforme mostrado acima. Defina-o como **fixo** se houver uma política de repetição.

Para definir uma política de nova tentativa, configurações adicionais de dois valores podem ser especificadas: um intervalo de nova tentativa (**retryInterval**) e o número de tentativas (**retryCount**).

Intervalo de nova tentativa, especificado com o objeto **retryInterval** , que é o intervalo entre as tentativas. O valor padrão é de 30 segundos, seu valor configurável mínimo é de 15 segundos e o valor máximo é de 18 meses. Os trabalhos em coleções de trabalhos gratuitas têm um valor mínimo configurável de 1 hora.  Ele é definido no formato ISO 8601. Da mesma forma, o valor do número de tentativas é especificado com o objeto **retryCount** ; esse é o número de vezes que uma nova tentativa será feita. O valor padrão é 4 e o valor máximo é 20\. Ambos **retryInterval** e **retryCount** são opcionais. Eles receberão seus valores padrão se **retryType** for definido como **fixo** e nenhum valor for especificado explicitamente.

## <a name="see-also"></a>Consulte também
 [O que é o Agendador?](scheduler-intro.md)

 [Introdução à utilização do Agendador no Portal do Azure](scheduler-get-started-portal.md)

 [Planos e Cobrança no Agendador do Azure](scheduler-plans-billing.md)

 [Como criar agendas complexas e recorrência avançada com o Agendador do Azure](scheduler-advanced-complexity.md)

 [Referência da API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)

 [Alta disponibilidade e confiabilidade do Agendador do Azure](scheduler-high-availability-reliability.md)

 [Limites, padrões e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Agendador do Azure](scheduler-outbound-authentication.md)

