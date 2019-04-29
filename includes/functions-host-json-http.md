---
ms.openlocfilehash: 5abefd55aa06f53bc3b437b29a2582b3e2239a65
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737170"
---
```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------| 
|routePrefix|api|O prefixo da rota que se aplica a todas as rotas. Use uma cadeia de caracteres vazia para remover o prefixo padrão. |
|maxOutstandingRequests|200*|O número máximo de solicitações pendentes mantidas em um dado momento. Esse limite inclui solicitações que estão na fila, mas não iniciaram a execução, bem como qualquer execução em andamento. Quaisquer solicitações recebidas acima desse limite são rejeitadas com uma resposta "Muito ocupado" 429. Isso permite que os chamadores empreguem estratégias de repetição com base em tempo e também ajuda você a controlar as latências máximas de solicitação. Isso controla apenas o enfileiramento que ocorre no caminho de execução do host de script. Outras filas, como a fila de solicitação ASP.NET, ainda estarão vigor e não serão afetadas por essa configuração. *O padrão para a versão 1.x é ilimitado. O padrão para a versão 2.x em um plano de consumo é 200. O padrão para a versão 2.x em um plano dedicado é ilimitado.|
|maxConcurrentRequests|100*|O número máximo de funções http que serão executadas em paralelo. Isso permite controlar a simultaneidade, o que pode ajudar a gerenciar a utilização de recursos. Por exemplo, talvez você tenha uma função http que use muitos recursos do sistema (memória/cpu/soquetes), de modo que ela causará problemas quando a simultaneidade for muito alta. Ou talvez você tenha uma função que faça solicitações de saída a um serviço de terceiro, e essas chamadas precisem ser limitadas por taxa. Nesses casos, aplicar uma limitação aqui pode ajudar. *O padrão para a versão 1.x é ilimitado. O padrão para a versão 2.x em um plano de consumo é 100. O padrão para a versão 2.x em um plano dedicado é ilimitado.|
|dynamicThrottlesEnabled|true*|Quando habilitada, essa configuração faz o pipeline de processamento de solicitação verificar periodicamente os contadores de desempenho do sistema como conexões/threads/processos/memória/cpu/etc. e, se algum desses contadores tiver excedido um alto limite interno (80%), as solicitações serão rejeitadas com a resposta 429 "Muito ocupado" até que os contadores retornem aos níveis normais. *O padrão para a versão 1.x é falso. O padrão para a versão 2.x em um plano de consumo é verdadeiro. O padrão para a versão 2.x em um plano dedicado é falso.|

<!-- ms.date: 04/26/2019 -->