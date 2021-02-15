---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 617608f382d3331e59ae92c9eb272347c736b768
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828752"
---
As funções permitem o uso de chaves para dificultar o acesso aos pontos de extremidade de função HTTP durante o desenvolvimento. A menos que o nível de acesso HTTP em uma função disparada por HTTP seja definido como `anonymous`, as solicitações devem incluir uma chave de acesso à API na solicitação. 

Embora as chaves forneçam um mecanismo de segurança padrão, talvez você queira considerar opções adicionais para proteger um ponto de extremidade HTTP na produção. Por exemplo, geralmente não é uma boa prática distribuir o segredo compartilhado em aplicativos públicos. Se sua função estiver sendo chamada de um cliente público, talvez você queira considerar a implementação de outro mecanismo de segurança. Para obter mais informações, confira [Proteger um ponto de extremidade HTTP em produção](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production).

Ao renovar os valores de chave de função, você deve redistribuir manualmente os valores de chave atualizados para todos os clientes que chamam sua função.  

#### <a name="authorization-scopes-function-level"></a>Escopos de autorização (nível de função)

Há dois escopos de acesso para chaves de nível de função:

* **Função**: essas chaves se aplicam apenas às funções específicas sob as quais elas foram definidas. Quando usadas como uma chave de API, elas permitem acesso apenas às funções em questão.

* **Host**: As chaves com um escopo de host podem ser usadas para acessar todas as funções no aplicativo de funções. Quando usadas como uma chave de API, elas permitem acesso a qualquer função no aplicativo de funções. 

Cada chave é nomeada para referência e há uma chave padrão (chamada "default") no nível de função e de host. As chaves de função têm precedência sobre as chaves de host. Quando duas chaves forem definidas com o mesmo nome, a chave de função sempre será usada.

#### <a name="master-key-admin-level"></a>Chave mestra (nível de administrador) 

Cada aplicativo de funções também tem uma chave de host de nível de administrador chamada `_master`. Além de fornecer acesso em nível de host a todas as funções no aplicativo, a chave mestra também fornece acesso administrativo às APIs REST de tempo de execução. Não é possível revogar essa chave. Quando você define o nível de acesso como `admin`, as solicitações precisam usar a chave mestra. Nesse caso, o uso de outra chave resulta em falha no acesso.

> [!CAUTION]  
> Devido às permissões elevadas no aplicativo de funções concedidas pela chave mestra, você não deve compartilhar essa chave com terceiros nem distribuí-la em aplicativos cliente nativos. Tenha cuidado ao escolher o nível de acesso do administrador.
