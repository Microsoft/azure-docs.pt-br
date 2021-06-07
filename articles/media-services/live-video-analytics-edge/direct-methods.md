---
title: Usar métodos diretos na análise de vídeo ao vivo no IoT Edge-Azure
description: A análise de vídeo ao vivo em IoT Edge expõe vários métodos diretos. Os métodos diretos se baseiam nas convenções descritas neste tópico.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8b5c16dc72beed4ec757e48461a2fc194c113f8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656233"
---
# <a name="direct-methods"></a>Métodos diretos

A análise de vídeo ao vivo em IoT Edge expõe vários métodos diretos que podem ser invocados do Hub IoT. Os métodos diretos representam uma interação entre solicitação e resposta com um dispositivo semelhante a uma chamada HTTP, na qual eles são bem-sucedidos ou falham imediatamente (depois que o tempo limite especificado pelo usuário é atingido). Essa abordagem é útil para cenários em que o curso de ação imediata é diferente dependendo se o dispositivo foi capaz de responder. Para obter mais informações, consulte [entender e invocar métodos diretos do Hub IOT](../../iot-hub/iot-hub-devguide-direct-methods.md).

Este tópico descreve esses métodos e convenções.

## <a name="conventions"></a>Convenções

Os métodos diretos se baseiam nas seguintes convenções:

1. Os métodos diretos têm uma versão especificada em MAJOR. Formato secundário (conforme mostrado no exemplo a seguir):

    ```
    {
        "methodName": "GraphTopologySet",
        "payload": {
            // API version matches matches module MAJOR.MINOR version
            "@apiVersion": "1.0"
            //payload here…
        }
    }
    ```

2. Uma determinada versão da análise de vídeo ao vivo no módulo IoT Edge oferecerá suporte a todos os métodos diretos até sua versão atual. Por exemplo, a versão do módulo 1,3 dará suporte a métodos diretos com versões 1,3, 1,2, 1,1 e 1,0.
3. Todos os métodos diretos são síncronos.
4. Os resultados de erro seguem o esquema de erro OData.
5. Os nomes devem observar as seguintes restrições:
    
    * Apenas caracteres alfanuméricos e traços, desde que não comecem e terminem com um traço
    * Sem espaços
    * Máximo de 32 caracteres

### <a name="example"></a>Exemplo

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "Graph instance is not found."
    }
  }
}
```    

### <a name="top-level-error-codes"></a>Códigos de erro de nível superior     

|Status |Código   |Mensagem|
|---|---|---|
|400|   BadRequest| A solicitação não é válida|
|400|   InvalidResource|    O recurso não é válido|
|400|   InvalidVersion| A versão da API não é válida|
|402|   ConnectivityRequired    |O módulo do Edge requer conectividade de nuvem para funcionar corretamente.|
|404|   NotFound    |O recurso não foi encontrado|
|409|   Conflito|   Conflito de operação|
|500|   InternalServerError|    Erro interno do servidor|
|503|   ServerBusy| Servidor ocupado|

### <a name="detailed-error-codes"></a>Códigos de erro detalhados

O erro de validações detalhados, como validações de módulo de grafo, é adicionado como detalhes do erro:

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "InvalidResource",
      "message": "The resource format is invalid.",
      "details": [
        {
          "code": "RtspEndpointUrlInvalidScheme",
          "target": "$.Properties.Sources[0]",
          "message": "Uri scheme 'http' is not valid for an RTSP source endpoint. Valid values are: rtsp"
        },
        {
          "code": "PropertyValueInvalidPattern",
          "target": "$.Properties.Sinks[0].AssetNamePattern",
          "message": "The value must match the regular expression '^[^<>%&:\\\\\\/?*+.']{1,260}$'"
        }
      ]
    }
  }
}
}
```

|Status|    Código Detalhado   |Descrição|
|---|---|---|
|400|   GraphValidationError|   Erros gerais de grafo, como ciclos ou particionamento, etc.|
|400|   ModuleValidationError|  Erros de validação específicos do módulo.|
|409|   GraphTopologyInUse| A topologia do grafo ainda é referenciada por uma ou mais instâncias de grafo.|
|409|   OperationNotAllowedInState| A operação solicitada não pode ser executada no estado atual.|
|409|   ResourceValidationError|    O recurso referenciado (exemplo: ativo) não está em um estado válido.|

## <a name="details"></a>Detalhes  

### <a name="graphtopologyget"></a>GraphTopologyGet

Esse método direto recupera uma única topologia de grafo.

#### <a name="request"></a>Solicitação

```
{
    "methodName": "GraphTopologyGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": {
        "name": "CaptureAndRecord",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Códigos de status

| Condição | Código de status | Código de erro detalhado |
|--|--|--|
| Entidade encontrada | 200 | N/D |
| Erros gerais do usuário | Intervalo de 400 |  |
| Entidade não encontrada | 404 |  |
| Erros gerais do servidor | Intervalo de 500 |  |

### <a name="graphtopologyset"></a>GraphTopologySet

Cria uma única topologia se não houver uma existente com o nome ou as atualizações especificadas e uma existente com o mesmo nome.

Principais aspectos:

* A topologia pode ser atualizada livremente não há nenhum grafo fazendo referência a ela.
* A topologia poderá ser atualizada livremente se todos os grafos de referência forem desativados desde que:

    * Parâmetros adicionados recentemente têm valores padrão
    * Parâmetros removidos não são referenciados por nenhum grafo
* As atualizações de topologia não são permitidas se houver grafos ativos

#### <a name="request"></a>Solicitação

```
{
    "methodName": "GraphTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord",
        "properties": {
            // Desired graph topology properties
        }
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 201,
    "payload": {
        "name": " CaptureAndRecord ",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Códigos de status

|Condição  |Código de status    |Código de erro detalhado|
|---|---|---|
Entidade existente atualizada |200|   N/D|
Nova entidade criada  |201|   N/D|
Erros gerais do usuário |Intervalo de 400  ||
Erros de validação de grafo |400    |GraphValidationError|
Erros de validação de módulo|   400 |ModuleValidationError|
Erros gerais do servidor   |Intervalo de 500  ||

### <a name="graphtopologydelete"></a>GraphTopologyDelete

Exclui uma única topologia de grafo.

#### <a name="request"></a>Solicitação

```
{
    "methodName": "GraphTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Códigos de status

| Condição | Código de status | Código de erro detalhado |
|--|--|--|
| Entidade excluída | 200 | N/D |
| Entidade não encontrada | 204 | N/D |
| Erros gerais do usuário | Intervalo de 400 |  |
| A topologia de grafo está sendo referenciada por uma ou mais instâncias de grafo | 409 | GraphTopologyInUse |
| Erros gerais do servidor | Intervalo de 500 |  |

### <a name="graphtopologylist"></a>GraphTopologyList

Recupera uma lista de todas as topologias de grafo que correspondem aos critérios de filtro.

#### <a name="request"></a>Solicitação

```
{
    "methodName": "GraphTopologyList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Suporte a filtro

|Operação |Campo (s)    |Operadores|
|---|---|---|
|$orderby|name  |ASC|


#### <a name="status-codes"></a>Códigos de status

| Condição | Código de status | Código de erro detalhado |
|--|--|--|
| Sucesso | 200 | N/D |
| Erros gerais do usuário | Intervalo de 400 |  |
| Erros gerais do servidor | Intervalo de 500 |  |

### <a name="graphinstanceget"></a>GraphInstanceGet

Recupera uma única instância de grafo:

#### <a name="request"></a>Solicitação

```
{
    "methodName": "GraphInstanceGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": {
        "name": "Camera001",
        "properties": {
            // Complete graph instance
        }
    }
}
```

#### <a name="status-codes"></a>Códigos de status

| Condição | Código de status | Código de erro detalhado |
|--|--|--|
| Entidade encontrada | 200 | N/D |
| Erros gerais do usuário | Intervalo de 400 |  |
| Entidade não encontrada | 404 |  |
| Erros gerais do servidor | Intervalo de 500 |  |

### <a name="graphinstanceset"></a>GraphInstanceSet

Cria uma única instância de grafo se não houver uma existente com o nome ou as atualizações especificadas e uma existente com o mesmo nome.

Principais aspectos:

* A instância de grafo pode ser atualizada livremente no estado "desativado".

    * O grafo é revalidado em cada atualização.
* As atualizações da instância do Graph são parcialmente restritas enquanto o grafo não está no estado "inativo".
* As atualizações da instância do grafo não são permitidas em grafos ativos.

#### <a name="request"></a>Solicitação

```
{
"methodName": "GraphInstanceSet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001",
        "properties": {
            // Desired graph instance properties
        }
    }
}
```

#### <a name="response"></a>Resposta

````
{
    "status": 201,
    "payload": {
        "name": " Camera001",
        "properties": {
            // Complete graph instance
        }   
    }
}
````

#### <a name="status-codes"></a>Códigos de status

| Condição | Código de status | Código de erro detalhado |
|--|--|--|
| Entidade existente atualizada | 200 | N/D |
| Nova entidade criada | 201 | N/D |
| Erros gerais do usuário | Intervalo de 400 |  |
| Erros de validação de grafo | 400 | GraphValidationError |
| Erros de validação de módulo | 400 | ModuleValidationError |
| Erros de validação de recurso | 409 | ResourceValidationError |
| Erros gerais do servidor | Intervalo de 500 |  |  |

### <a name="graphinstancedelete"></a>GraphInstanceDelete

Exclui uma única instância de grafo.

Principais aspectos:

* Somente grafos desativados podem ser excluídos.

#### <a name="request"></a>Solicitação

```
{
    "methodName": "GraphInstanceDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Códigos de status

| Condição | Código de status | Código de erro detalhado |
|--|--|--|
| Grafo excluído com êxito | 200 | N/D |
| Grafo não encontrado | 204 | N/D |
| Erros gerais do usuário | Intervalo de 400 |  |
| O grafo não está no estado "parado" | 409 | OperationNotAllowedInState |
| Erros gerais do servidor | Intervalo de 500 |  |

### <a name="graphinstancelist"></a>GraphInstanceList

Isso é semelhante a GraphTopologyList. Ele permite usar para enumerar as instâncias de grafo.
Recupera uma lista de todas as instâncias de grafos que correspondem aos critérios de filtro.

#### <a name="request"></a>Solicitação

```
{
    "methodName": "GraphInstanceList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Suporte a filtro

|Operação  |   Campo (s)|   Operadores|
|---|---|---|
|$orderby|  name|   ASC|

#### <a name="status-codes"></a>Códigos de status

| Condição | Código de status | Código de erro detalhado |
|--|--|--|
| Sucesso | 200 | N/D |
| Erros gerais do usuário | Intervalo de 400 |  |
| Erros gerais do servidor | Intervalo de 500 |  |

### <a name="graphinstanceactivate"></a>GraphInstanceActivate

Ativa uma única instância de grafo. 

Principais aspectos

* O método retorna somente quando o grafo é ativado 
* O grafo assume o estado "Ativando" durante a ativação.

    * Uma operação de lista/obtenção no grafo retornaria o grafo no estado apropriado.
* Idempotência

    * Iniciar um grafo no estado "Ativando" comporta-se da mesma maneira que se o grafo fosse desativado (ou seja: blocos de chamada até que o grafo seja ativado)
    * Ativar um grafo no estado "ativo" retorna com êxito imediatamente.

#### <a name="request"></a>Solicitação

```
{
    "methodName": "GraphInstanceActivate",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Códigos de status

| Condição | Código de status | Código de erro detalhado |
|--|--|--|
| Grafo ativado com êxito | 200 | N/D |
| Nova entidade criada | 201 | N/D |
| Erros gerais do usuário | Intervalo de 400 |  |
| Erros de validação de módulo | 400 | ModuleValidationError |
| Erros de validação de recurso | 409 | ResourceValidationError |
| O grafo está no estado de desativação | 409 | OperationNotAllowedInState |
| Erros gerais do servidor | Intervalo de 500 |  |

### <a name="graphinstancedeactivate"></a>GraphInstanceDeactivate

Desativa uma única instância de grafo. A desativação de um grafo desativa normalmente o processamento de mídia e garante que todos os eventos e mídias armazenados de forma transitória no Edge sejam comprometidos na nuvem, sempre que aplicável.

Principais aspectos:

* O método só retorna quando o grafo é desativado
* O grafo assume o estado "desativando" durante a desativação.

    * Uma operação de lista/obtenção no grafo retornaria o grafo no estado apropriado.
    * Stop somente é concluído quando todas as mídias são carregadas na nuvem.
* Idempotência

    * A desativação de um grafo no estado "desativando" comporta-se da mesma maneira que se o grafo fosse desativado (ou seja: blocos de chamada até o grafo ser desativado)
    * A desativação de um grafo no estado "inativo" retorna com êxito imediatamente.

#### <a name="request"></a>Solicitação

```
{
    "methodName": "GraphInstanceDeactivate",
    // A high response timeout is recommended here since there might be data movement    // being executed as part of this operation
    "responseTimeoutInSeconds": 300,
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Resposta

```
{
    "status": 200,
    "payload": null
}
```

| Condição | Código de status | Código de erro detalhado |
|--|--|--|
| Grafo ativado com êxito | 200 | N/D |
| Nova entidade criada | 201 | N/D |
| Erros gerais do usuário | Intervalo de 400 |  |
| O grafo está no estado de ativação | 409 | OperationNotAllowedInState |
| Erros gerais do servidor | Intervalo de 500 |  |

## <a name="next-steps"></a>Próximas etapas

[Esquema de configuração do Módulo Gêmeo](module-twin-configuration-schema.md)
