---
title: Persistência de dados e serialização no Durable Functions-Azure
description: Saiba como a extensão de Durable Functions para Azure Functions mantém os dados
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: ea4aaa1cdbe10e2db9cf619452558d104a2293ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449366"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Persistência de dados e serialização em Durable Functions (Azure Functions)

Durable Functions mantém automaticamente os parâmetros de função, valores de retorno e outro Estado para um back-end durável a fim de fornecer execução confiável. No entanto, a quantidade e a frequência dos dados persistidos para o armazenamento durável podem afetar o desempenho do aplicativo e os custos de transação de armazenamento. Dependendo do tipo de dados que seu aplicativo armazena, a retenção de dados e as políticas de privacidade também podem precisar ser consideradas.

## <a name="azure-storage"></a>Armazenamento do Azure

Por padrão, Durable Functions mantém os dados em filas, tabelas e blobs em uma conta de [armazenamento do Azure](https://azure.microsoft.com/services/storage/) que você especificar.

### <a name="queues"></a>Filas

Durable Functions usa filas do armazenamento do Azure para agendar de forma confiável todas as execuções de função. Essas mensagens de fila contêm entradas de função ou saídas, dependendo se a mensagem está sendo usada para agendar uma execução ou retornar um valor para uma função de chamada. Essas mensagens de fila também incluem metadados adicionais que Durable Functions usa para fins internos, como roteamento e correlação de ponta a ponta. Depois que uma função termina de ser executada em resposta a uma mensagem recebida, essa mensagem é excluída e o resultado da execução também pode ser persistido para tabelas de armazenamento do Azure ou BLOBs de armazenamento do Azure.

Em um único [Hub de tarefas](durable-functions-task-hubs.md), o Durable Functions cria e adiciona mensagens a uma fila de *itens de trabalho* chamada `<taskhub>-workitem` para funções de atividade de agendamento e uma ou mais *filas de controle* nomeadas `<taskhub>-control-##` para agendar ou retomar funções de orquestrador e entidade. O número de filas de controle é igual ao número de partições configuradas para seu aplicativo. Para obter mais informações sobre filas e partições, consulte a [documentação de desempenho e escalabilidade](durable-functions-perf-and-scale.md).

### <a name="tables"></a>Tabelas

Depois que as orquestrações processam mensagens com êxito, os registros de suas ações resultantes são persistidos na tabela de *histórico* denominada `<taskhub>History` . As entradas de orquestração, as saídas e os dados de status personalizados também são persistidos na tabela de *instâncias* denominada `<taskhub>Instances` .

### <a name="blobs"></a>Blobs

Na maioria dos casos, Durable Functions não usa os blobs de armazenamento do Azure para manter os dados. No entanto, filas e tabelas têm [limites de tamanho](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits) que podem impedir Durable Functions de persistir todos os dados necessários em uma mensagem de fila ou linha de armazenamento. Por exemplo, quando um dado que precisa ser persistido em uma fila é maior que 45 KB Quando serializado, Durable Functions compactará os dados e os armazenará em um blob. Ao persistir dados no armazenamento de BLOBs dessa forma, a função durável armazena uma referência a esse blob na linha da tabela ou na mensagem da fila. Quando Durable Functions precisa recuperar os dados, ele será obtido automaticamente do blob. Esses BLOBs são armazenados no contêiner de blob `<taskhub>-largemessages` .

> [!NOTE]
> As etapas de compactação extra e operação de BLOB para mensagens grandes podem ser caras em termos de custos de latência de CPU e e/s. Além disso, Durable Functions precisa carregar dados persistentes na memória e pode fazer isso para muitas execuções de função diferentes ao mesmo tempo. Como resultado, a persistência de grandes cargas de dados pode causar um alto uso de memória também. Para minimizar a sobrecarga de memória, considere a possibilidade de manter cargas de dados grandes manualmente (por exemplo, no armazenamento de BLOBs) e, em vez disso, passar referências a esses dados. Dessa forma, seu código pode carregar os dados somente quando necessário para evitar cargas redundantes durante as [repetições de função do Orchestrator](durable-functions-orchestrations.md#reliability). No entanto, *não* é recomendável armazenar cargas em disco, pois o estado do disco não tem garantia de estar disponível, pois as funções podem ser executadas em VMs diferentes durante seus tempos de vida.

### <a name="types-of-data-that-is-serialized-and-persisted"></a>Tipos de dados que são serializados e persistentes
Veja a seguir uma lista dos diferentes tipos de dados que serão serializados e persistidos ao usar os recursos do Durable Functions:

- Todas as entradas e saídas de orquestrador, atividade e funções de entidade, incluindo quaisquer IDs e exceções sem tratamento
- Nomes de funções do Orchestrator, da atividade e da entidade
- Nomes e cargas de eventos externos
- Cargas de status de orquestração personalizadas
- Mensagens de encerramento de orquestração
- Cargas de temporizador durável
- URLs de solicitação e resposta de HTTP duráveis, cabeçalhos e cargas
- Conteúdo de chamada de entidade e de sinal
- Cargas de estado de entidade

### <a name="working-with-sensitive-data"></a>Trabalhando com dados confidenciais
Ao usar o armazenamento do Azure, todos os dados são criptografados automaticamente em repouso. No entanto, qualquer pessoa com acesso à conta de armazenamento pode ler os dados em sua forma descriptografada. Se você precisar de proteção mais forte para dados confidenciais, considere primeiro criptografar os dados usando suas próprias chaves de criptografia para que Durable Functions persista os dados em um formato previamente criptografado.

Como alternativa, os usuários do .NET têm a opção de implementar provedores de serialização personalizados que fornecem criptografia automática. Um exemplo de serialização personalizada com criptografia pode ser encontrado neste [exemplo do GitHub](https://github.com/charleszipp/azure-durable-entities-encryption).

> [!NOTE]
> Se você decidir implementar a criptografia no nível do aplicativo, lembre-se de que as orquestrações e entidades podem existir por períodos indefinidos de tempo. Isso é importante quando se trata de girar suas chaves de criptografia porque uma orquestração ou entidades podem ser executadas por mais tempo do que a política de rotação de chaves. Se ocorrer uma rotação de chaves, a chave usada para criptografar seus dados poderá não estar mais disponível para descriptografá-la na próxima vez que sua orquestração ou entidade for executada. A criptografia do cliente é, portanto, recomendada somente quando as orquestrações e entidades são esperadas para serem executadas por períodos de tempo relativamente curtos.

## <a name="customizing-serialization-and-deserialization"></a>Personalizando a serialização e desserialização

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>Lógica de serialização padrão

O Durable Functions usa internamente [JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar dados de orquestração e entidade para JSON. As configurações padrão Durable Functions usadas para Json.NET são:

**Entradas, saídas e estado:**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**Exceção**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

Leia a documentação mais detalhada sobre `JsonSerializerSettings` [aqui](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm).

## <a name="customizing-serialization-with-net-attributes"></a>Personalizando a serialização com atributos .NET

Ao serializar dados, o Json.NET procura [vários atributos](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm) em classes e propriedades que controlam como os dados são serializados e desserializados do JSON. Se você possui o código-fonte para o tipo de dados passado para Durable Functions APIs, considere adicionar esses atributos ao tipo para personalizar a serialização e desserialização.

## <a name="customizing-serialization-with-dependency-injection"></a>Personalizando a serialização com injeção de dependência

Os aplicativos de funções direcionados ao .NET e executados no tempo de execução do Functions v3 podem usar [injeção de dependência (di)](../functions-dotnet-dependency-injection.md) para personalizar como os dados e as exceções são serializados. O código de exemplo a seguir demonstra como usar DI para substituir as configurações de serialização de Json.NET padrão usando implementações personalizadas das `IMessageSerializerSettingsFactory` `IErrorSerializerSettingsFactory` interfaces de serviço e.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Extensions.DependencyInjection;
using Newtonsoft.Json;
using System.Collections.Generic;

[assembly: FunctionsStartup(typeof(MyApplication.Startup))]
namespace MyApplication
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddSingleton<IMessageSerializerSettingsFactory, CustomMessageSerializerSettingsFactory>();
            builder.Services.AddSingleton<IErrorSerializerSettingsFactory, CustomErrorSerializerSettingsFactory>();
        }

        /// <summary>
        /// A factory that provides the serialization for all inputs and outputs for activities and
        /// orchestrations, as well as entity state.
        /// </summary>
        internal class CustomMessageSerializerSettingsFactory : IMessageSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }

        /// <summary>
        /// A factory that provides the serialization for all exceptions thrown by activities
        /// and orchestrations
        /// </summary>
        internal class CustomErrorSerializerSettingsFactory : IErrorSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="serialization-and-deserialization-logic"></a>Lógica de serialização e desserialização

Azure Functions aplicativos de nó usam [ `JSON.stringify()` para serialização](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) e [ `JSON.Parse()` para desserialização](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse). A maioria dos tipos deve serializar e desserializar diretamente. Nos casos em que a lógica padrão é insuficiente, a definição de um `toJSON()` método no objeto seqüestrará a lógica de serialização. No entanto, não existe nenhuma analogia para desserialização de objeto.

Para obter a personalização completa do pipeline de serialização/desserialização, considere manipular a serialização e desserialização com seu próprio código e passar dados como cadeias de caracteres.


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>Lógica de serialização e desserialização

É altamente recomendável usar anotações de tipo para garantir que Durable Functions serializa e desserializa os dados corretamente. Embora muitos tipos internos sejam tratados automaticamente, alguns tipos de dados internos exigem anotações de tipo para preservar o tipo durante a desserialização.

Para tipos de dados personalizados, você deve definir a serialização JSON e a desserialização de um tipo de dados exportando um estático `to_json` e um `from_json` método de sua classe.

---
