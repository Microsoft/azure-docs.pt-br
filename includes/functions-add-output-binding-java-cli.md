---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673298"
---
Em um projeto Java, as associações são definidas como anotações de associação no método de função. O arquivo *function.json* é então gerado automaticamente com base nessas anotações.

Procure a localização do código de função em _src/main/java_, abra o arquivo de projeto *Function.java* e adicione o seguinte parâmetro à definição do método `run`:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

O parâmetro `msg` é um tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), que representa uma coleção de cadeias de caracteres que são gravadas como mensagens em uma associação de saída quando a função é concluída. Nesse caso, a saída é uma fila de armazenamento denominada `outqueue`. A cadeia de conexão para a conta de armazenamento é definida pelo método `connection`. Em vez da própria cadeia de conexão, passe a configuração de aplicativo que contém a cadeia de conexão da conta de armazenamento.

A definição do método `run` agora deverá ser semelhante ao seguinte exemplo:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```