---
title: Gatilhos e associações no Azure Functions
description: Saiba como usar gatilhos e associações para conectar-se a sua função do Azure para eventos online e serviços baseados em nuvem.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 02/18/2019
ms.date: 03/04/2019
ms.author: v-junlch
ms.openlocfilehash: 3865f748a9ca2fe09660d6454542d64f73a8e3c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020955"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Conceitos de gatilhos e de associações do Azure Functions

Neste artigo, você aprenderá os conceitos de alto nível que envolvem funções gatilhos e associações.

Os gatilhos são o que fazer com que uma função a ser executada. Um gatilho define como uma função é invocada e uma função deve ter exatamente um gatilho. Gatilhos têm dados associados, que geralmente são fornecidos como a carga da função. 

Associação a uma função é uma maneira de conectar declarativamente a outro recurso para a função; associações podem estar conectadas como *associações de entrada*, *associações de saída*, ou ambos. Dados de associações são fornecidos para a função como parâmetros.

Você pode misturar e combinar diferentes vinculações para atender às suas necessidades. Associações são opcionais e uma função pode ter um ou vários entrada e/ou associações de saída.

Gatilhos e associações permitem evitar embutir o acesso a outros serviços. Sua função recebe dados (por exemplo, o conteúdo de uma mensagem da fila) em parâmetros de função. Você envia dados (por exemplo, para criar uma mensagem da fila) usando o valor retornado da função. 

Considere os seguintes exemplos de como você poderia implementar funções diferentes.

| Cenário de exemplo | Gatilho | Associação de entrada | Associação de saída |
|-------------|---------|---------------|----------------|
| Chega de uma nova mensagem de fila que executa uma função para gravar em outra fila. | fila<sup>*</sup> | *Nenhum* | fila<sup>*</sup> |
|Um trabalho agendado lê o conteúdo do armazenamento de BLOBs e cria um novo documento do Cosmos DB. | Timer | Armazenamento de Blobs | Cosmos DB |
|A grade de eventos é usada para ler uma imagem do armazenamento de BLOBs e um documento do Cosmos DB para enviar um email. | Grade de Eventos | O armazenamento de BLOBs e o Cosmos DB | SendGrid |
| Um webhook que usa o Microsoft Graph para atualizar uma planilha do Excel. | HTTP | *Nenhum* | Microsoft Graph |

<sup>\*</sup> Representa as diferentes filas

Esses exemplos não devem ser exaustivos, mas são fornecidos para ilustrar como você pode usar gatilhos e associações juntos.

###  <a name="trigger-and-binding-definitions"></a>Definições de gatilho e associação

Gatilhos e associações são definidas forma diferente dependendo da abordagem de desenvolvimento.

| Plataforma | Gatilhos e associações são configuradas por... |
|-------------|--------------------------------------------|
| C#biblioteca de classes | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decorando métodos e parâmetros com o C# atributos |
| Todos os outros (incluindo o portal do Azure) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;updating [function.json](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

O portal fornece uma interface do usuário para essa configuração, mas você pode editar o arquivo diretamente, abrindo o **editor avançado** disponíveis por meio de **integrar** guia da sua função.

No .NET, o tipo de parâmetro define o tipo de dados para dados de entrada. Por exemplo, use `string` para associar ao texto de um gatilho de fila, uma matriz de bytes para ler como binário e um tipo personalizado para desserializar a um objeto.

Para idiomas que são digitados dinamicamente como JavaScript, use a propriedade `dataType` no arquivo *function.json*. Por exemplo, para ler o conteúdo de uma solicitação HTTP em formato binário, defina `dataType` para `binary`:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Outras opções para `dataType` são `stream` e `string`.

## <a name="binding-direction"></a>Direção de associação

Todos os disparadores e associações têm uma propriedade `direction` no arquivo [function.json](./functions-reference.md):

- Para gatilhos, a direção sempre é `in`
- Associações de entrada e saída usam `in` e `out`
- Algumas associações dão suporte a uma direção especial `inout`. Se você usar `inout`, somente o **editor avançado** está disponível por meio de **integrar** guia no portal.

Quando você usa [atributos em uma biblioteca de classes](functions-dotnet-class-library.md) para configurar associações e gatilhos, a direção é fornecida em um construtor de atributo ou inferida do tipo de parâmetro.

## <a name="supported-bindings"></a>Associações com suporte

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Para obter informações sobre quais associações estão na visualização ou são aprovadas para o uso de produção, consulte [Idiomas com suporte](supported-languages.md).

## <a name="resources"></a>Recursos
- [Padrões e expressões de associação](./functions-bindings-expressions-patterns.md)
- [Usando o valor de retorno de função do Azure](./functions-bindings-return-value.md)
- [Como registrar uma expressão de associação](./functions-bindings-register.md)
- Teste:
  - [Estratégias para testar seu código no Azure Functions](functions-test-a-function.md)
  - [Executar manualmente uma função não disparada por HTTP](functions-manually-run-non-http.md)
- [Tratamento de erros de associação](./functions-bindings-errors.md)

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Registrar as extensões de associação de funções do Azure](./functions-bindings-register.md)

<!-- Update_Description: wording update -->
